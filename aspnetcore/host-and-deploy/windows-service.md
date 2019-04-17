---
title: Hosten von ASP.NET Core in einem Windows-Dienst
author: guardrex
description: Erfahren Sie, wie eine ASP.NET Core-App in einem Windows-Dienst gehostet wird.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 04/04/2019
uid: host-and-deploy/windows-service
ms.openlocfilehash: 544eefa87898e82ec2bf8f9f61ce4e26dd554bb7
ms.sourcegitcommit: 6bde1fdf686326c080a7518a6725e56e56d8886e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59068335"
---
# <a name="host-aspnet-core-in-a-windows-service"></a>Hosten von ASP.NET Core in einem Windows-Dienst

Von [Luke Latham](https://github.com/guardrex) und [Tom Dykstra](https://github.com/tdykstra)

Eine ASP.NET Core-App kann unter Windows als [Windows-Dienst](/dotnet/framework/windows-services/introduction-to-windows-service-applications) ohne die Verwendung von IIS gehostet werden. Wenn die App als Windows-Dienst gehostet wird, erfolgen Neustarts automatisch.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/windows-service/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Erforderliche Komponenten

* [PowerShell 6.2 oder höher](https://github.com/PowerShell/PowerShell)

> [!NOTE]
> Für Windows-Betriebssysteme vor dem Windows 10-Update vom Oktober 2018 (Version 1809/Build 10.0.17763) muss das Modul [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts) mit dem Modul [WindowsCompatibility](https://github.com/PowerShell/WindowsCompatibility) importiert werden, um Zugriff auf das im Abschnitt [Erstellen eines Benutzerkontos](#create-a-user-account) verwendete Cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) zu erhalten:
>
> ```powershell
> Install-Module WindowsCompatibility -Scope CurrentUser
> Import-WinModule Microsoft.PowerShell.LocalAccounts
> ```

## <a name="deployment-type"></a>Bereitstellungstyp

Sie können entweder eine Framework-abhängige oder eine eigenständige Windows-Dienstbereitstellung erstellen. Weitere Informationen und Tipps zu Bereitstellungsszenarien finden Sie unter [.NET Core-Anwendungsbereitstellung](/dotnet/core/deploying/).

### <a name="framework-dependent-deployment"></a>Framework-abhängige Bereitstellung

Eine Framework-abhängige Bereitstellung (Framework-Dependent Deployment, FDD) benötigt eine gemeinsame systemweite Version von .NET Core auf dem Zielsystem. Wenn das FDD-Szenario mit einer ASP.NET Core-Windows-Dienst-App verwendet wird, erzeugt das SDK eine ausführbare Datei (*\*.exe*). Diese wird *Framework-abhängige ausführbare Datei* genannt.

### <a name="self-contained-deployment"></a>Eigenständige Bereitstellung

Bei einer eigenständigen Bereitstellung (Self-Contained Deployment, SCD) müssen die freigegebenen Komponenten nicht auf dem Zielsystem vorhanden sein. Die Runtime und die App Abhängigkeiten werden mit der App auf dem Hostsystem bereitgestellt.

## <a name="convert-a-project-into-a-windows-service"></a>Konvertieren eines Projekts in einen Windows-Dienst

Nehmen Sie die folgenden Änderungen an einem vorhandenen ASP.NET Core-Projekt vor, um die App als Dienst auszuführen:

### <a name="project-file-updates"></a>Projektdateiupdates

Aktualisieren Sie die Projektdatei basierend auf dem von Ihnen gewählten [Bereitstellungstyp](#deployment-type):

#### <a name="framework-dependent-deployment-fdd"></a>Framework-abhängige Bereitstellung

Fügen Sie einen Windows [Runtime-Bezeichner](/dotnet/core/rid-catalog) (Runtime Identifier, RID) zu dem `<PropertyGroup>` hinzu, der das Zielframework enthält. Im folgenden Beispiel wird die RID auf `win7-x64` festgelegt. Fügen Sie den `<SelfContained>` -Eigenschaftensatz zu `false` hinzu. Diese Eigenschaften weisen das SDK an, eine ausführbare Datei (*EXE*) für Windows zu generieren.

Eine *web.config*-Datei, die normalerweise erstellt wird, wenn Sie eine ASP.NET Core-App veröffentlichen, ist für eine Windows Services-App nicht erforderlich. Um die Erstellung der *web.config*-Datei zu deaktivieren, fügen Sie die auf `true` festgelegte `<IsTransformWebConfigDisabled>`-Eigenschaft hinzu.

::: moniker range=">= aspnetcore-2.2"

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.1"

Fügen Sie den `<UseAppHost>` -Eigenschaftensatz zu `true` hinzu. Diese Eigenschaft stellt für den Dienst einen Aktivierungspfad (eine ausführbare Datei, *EXE*) für eine frameworkabhängige Bereitstellung bereit.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.1</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

#### <a name="self-contained-deployment-scd"></a>Eigenständige Bereitstellung

Bestätigen Sie das Vorhandensein eines Windows [Runtime-Bezeichners](/dotnet/core/rid-catalog), oder fügen Sie einen solchen Bezeichner zu der `<PropertyGroup>` hinzu, die das Zielframework enthält. Deaktivieren Sie die Erstellung einer *web.config"*-Datei durch Hinzufügen des `<IsTransformWebConfigDisabled>`-Eigenschaftensatzes zu `true`.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

So führen Sie die Veröffentlichung für mehrere RIDs aus:

* Geben Sie die RIDs in einer durch Semikolons getrennten Liste an.
* Verwenden Sie den Eigenschaftennamen `<RuntimeIdentifiers>` (Plural).

  Weitere Informationen finden Sie im [.NET Core RID-Katalog](/dotnet/core/rid-catalog).

Fügen Sie einen Paketverweis auf [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) hinzu.

Um die Protokollierung für Windows-Ereignisprotokolle zu aktivieren, fügen Sie einen Paketverweis für [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) hinzu.

Weitere Informationen finden Sie im Abschnitt [Behandeln von Start- und Stopereignissen](#handle-starting-and-stopping-events).

### <a name="programmain-updates"></a>Program.Main-Updates

Nehmen Sie in `Program.Main` die folgenden Änderungen vor:

* Um bei der Ausführung außerhalb eines Dienstes zu testen und zu debuggen, fügen Sie Code hinzu, um festzustellen, ob die Anwendung als Dienst oder als Konsolenanwendung ausgeführt wird. Überprüfen Sie, ob der Debugger angefügt ist, oder ob ein `--console`-Befehlszeilenargument vorhanden ist.

  Wenn eine der Bedingungen zutrifft (die App wird nicht als Dienst ausgeführt), rufen Sie <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> auf dem Webhost auf.

  Wenn die Bedingungen nicht zutreffen (die App als Dienst ausgeführt wird):

  * Rufen Sie <xref:System.IO.Directory.SetCurrentDirectory*> auf, und verwenden Sie einen Pfad zum veröffentlichten Speicherort der App. Rufen Sie nicht <xref:System.IO.Directory.GetCurrentDirectory*> auf, um den Pfad abzurufen, da eine Windows-Dienst-App den Ordner *C:\\WINDOWS\\system32* zurückgibt, wenn <xref:System.IO.Directory.GetCurrentDirectory*> aufgerufen wird. Weitere Informationen finden Sie im Abschnitt [Aktuelles Verzeichnis und Inhaltsstammverzeichnis](#current-directory-and-content-root).
  * Rufen Sie <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> auf, um die App als Dienst auszuführen.

  Da der [Anbieter der Befehlszeilenkonfiguration](xref:fundamentals/configuration/index#command-line-configuration-provider) Name/Wert-Paare für Befehlszeilenargumente benötigt, wird der `--console`-Schalter aus den Argumenten entfernt, bevor <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> sie empfängt.

* Um das Windows-Ereignisprotokoll zu schreiben, fügen Sie den EventLog-Anbieter zu <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*> hinzu. Legen Sie den Protokollierungsgrad mit dem `Logging:LogLevel:Default`-Schlüssel in der *appsettings.Production.json*-Datei fest. Zu Demonstrations- und Testzwecken setzt die Produktionseinstellungsdatei der Beispiel-App den Protokollierungsgrad auf `Information`. In der Produktion wird der Wert in der Regel auf `Error` festgelegt. Weitere Informationen finden Sie unter <xref:fundamentals/logging/index#windows-eventlog-provider>.

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="publish-the-app"></a>Veröffentlichen der App

Veröffentlichen Sie die App mit [dotnet publish](/dotnet/articles/core/tools/dotnet-publish), einem [Visual Studio-Veröffentlichungsprofil](xref:host-and-deploy/visual-studio-publish-profiles) oder Visual Studio Code. Wählen Sie bei Verwendung von Visual Studio das **FolderProfile** aus, und konfigurieren Sie den **Zielspeicherort**, bevor Sie auf die Schaltfläche **Veröffentlichen** klicken.

Um die Beispiel-App mit CLI-Tools (Befehlszeilenschnittstelle) zu veröffentlichen, führen Sie den Befehl [dotnet publish](/dotnet/core/tools/dotnet-publish) in einer Windows-Befehlsshell im Projektordner mit einer Releasekonfiguration aus, die an die [-c|--configuration](/dotnet/core/tools/dotnet-publish#options)-Option übergeben wurde. Verwenden Sie die [-o|--output](/dotnet/core/tools/dotnet-publish#options)-Option mit einem Pfad für die Veröffentlichung in einem Ordner außerhalb der App.

### <a name="publish-a-framework-dependent-deployment-fdd"></a>Veröffentlichen einer Framework-abhängigen Bereitstellung

Im folgenden Beispiel wird die App im Ordner *c:\\svc* veröffentlicht:

```console
dotnet publish --configuration Release --output c:\svc
```

### <a name="publish-a-self-contained-deployment-scd"></a>Veröffentlichen einer eigenständigen Bereitstellung

Der RID muss in der Eigenschaft `<RuntimeIdenfifier>` (oder `<RuntimeIdentifiers>`) der Projektdatei angegeben werden. Stellen Sie die Runtime für die [-r|--runtime](/dotnet/core/tools/dotnet-publish#options)-Option des `dotnet publish`-Befehls bereit.

Im folgenden Beispiel wird die App für die `win7-x64`-Runtime im Ordner *c:\\svc* veröffentlicht:

```console
dotnet publish --configuration Release --runtime win7-x64 --output c:\svc
```

## <a name="create-a-user-account"></a>Erstellen eines Benutzerkontos

Erstellen Sie in einer administrativen PowerShell 6-Befehlsshell mithilfe des Cmdlets [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) ein Benutzerkonto für den Dienst:

```powershell
New-LocalUser -Name {NAME}
```

Geben Sie bei Aufforderung ein [sicheres Kennwort](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) ein.

Erstellen Sie für die Beispiel-App ein Benutzerkonto mit dem Namen `ServiceUser`.

```powershell
New-LocalUser -Name ServiceUser
```

Solange der Parameter `-AccountExpires` dem Cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) nicht mit einem Ablaufdatum <xref:System.DateTime> angegeben wird, läuft das Konto nicht ab.

Weitere Informationen finden Sie unter [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) und [Dienstbenutzerkonten](/windows/desktop/services/service-user-accounts).

Eine alternative Methode zum Verwalten von Benutzern bei Verwendung von Active Directory ist die Verwendung von verwalteten Dienstkonten. Weitere Informationen finden Sie unter [Gruppenverwaltete Dienstkonten: Übersicht](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).

## <a name="set-permission-log-on-as-a-service"></a>Festgelegte Berechtigung: Anmelden als Dienst

Gewähren Sie in einer administrativen PowerShell 6-Befehlsshell mit dem Befehl [icacls](/windows-server/administration/windows-commands/icacls) Schreib-/Lese-/Ausführungszugriff auf den Ordner der App.

```powershell
icacls "{PATH}" /grant "{USER ACCOUNT}:(OI)(CI){PERMISSION FLAGS}" /t
```

* `{PATH}` &ndash; Pfad zum Ordner der App.
* `{USER ACCOUNT}` &ndash; Das Benutzerkonto (SID).
* `(OI)` &ndash; Mit dem Flag für die Objektvererbung werden Berechtigungen an untergeordnete Dateien weitergegeben.
* `(CI)` &ndash; Mit dem Flag für die Containervererbung werden Berechtigungen an untergeordnete Ordner weitergegeben.
* `{PERMISSION FLAGS}` &ndash; Legt die Zugriffsberechtigungen für die App fest.
  * Schreiben (`W`)
  * Lesen (`R`)
  * Ausführen (`X`)
  * Vollzugriff (`F`)
  * Ändern (`M`)
* `/t` &ndash; Rekursive Anwendung auf vorhandene untergeordnete Ordner und Dateien.

Führen Sie für die im Ordner *c:\\svc* veröffentlichte Beispiel-App und das Konto `ServiceUser` mit Schreib-/Lese-/Ausführungsberechtigungen den folgenden Befehl in einer administrativen PowerShell 6-Befehlsshell aus.

```powershell
icacls "c:\svc" /grant "ServiceUser:(OI)(CI)WRX" /t
```

Weitere Informationen finden Sie unter [icacls](/windows-server/administration/windows-commands/icacls).

## <a name="create-the-service"></a>Erstellen Sie den Dienst.

Verwenden Sie das PowerShell-Skript [RegisterService.ps1](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/windows-service/scripts), um den Dienst zu registrieren. Führen Sie in einer administrativen PowerShell 6-Befehlsshell das Skript mit dem folgenden Befehl aus:

```powershell
.\RegisterService.ps1 
    -Name {NAME} 
    -DisplayName "{DISPLAY NAME}" 
    -Description "{DESCRIPTION}" 
    -Exe "{PATH TO EXE}\{ASSEMBLY NAME}.exe" 
    -User {DOMAIN\USER}
```

Im folgenden Beispiel für die Beispiel-App:

* Der Dienst heißt **MyService**.
* Der veröffentlichte Dienst ist im Ordner *c:\\svc* vorhanden. Die ausführbare Datei der App heißt *SampleApp.exe*.
* Der Dienst wird mit dem Konto `ServiceUser` ausgeführt. Beim folgenden Beispielbefehl lautet der Name des lokalen Computers `Desktop-PC`. Ersetzen Sie `Desktop-PC` durch den Computernamen oder die Domäne für Ihr System.

```powershell
.\RegisterService.ps1 
    -Name MyService 
    -DisplayName "My Cool Service" 
    -Description "This is the Sample App service." 
    -Exe "c:\svc\SampleApp.exe" 
    -User Desktop-PC\ServiceUser
```

## <a name="manage-the-service"></a>Verwalten des Diensts

### <a name="start-the-service"></a>Starten des Diensts

Starten Sie den Dienst mithilfe des PowerShell 6-Befehls `Start-Service -Name {NAME}`.

Verwenden Sie zum Starten des Diensts der Beispiel-App den folgenden Befehl:

```powershell
Start-Service -Name MyService
```

Das Starten des Diensts dauert ein paar Sekunden.

### <a name="determine-the-service-status"></a>Ermitteln des Dienststatus

Um den Status des Diensts zu überprüfen, verwenden Sie den PowerShell 6-Befehl `Get-Service -Name {NAME}`. Der Status wird als einer der folgenden Werte gemeldet:

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

Verwenden Sie den folgenden Befehl, um den Status des Diensts der Beispiel-App zu überprüfen:

```powershell
Get-Service -Name MyService
```

### <a name="browse-a-web-app-service"></a>Durchsuchen eines Web-App-Diensts

Befindet sich der Dienst im Status `RUNNING`, und ist der Dienst gleichzeitig eine Web-App, rufen Sie die App über ihren Pfad auf (Standardpfad ist `http://localhost:5000`, der umgeleitet wird zu `https://localhost:5001`, wenn [Middleware für HTTPS-Umleitung](xref:security/enforcing-ssl) verwendet wird).

Rufen Sie die App für den Dienst der Beispiel-App über `http://localhost:5000` auf.

### <a name="stop-the-service"></a>Dienst beenden

Beenden Sie den Dienst mithilfe des PowerShell 6-Befehls `Stop-Service -Name {NAME}`.

Verwenden Sie zum Beenden des Diensts der Beispiel-App den folgenden Befehl:

```powershell
Stop-Service -Name MyService
```

### <a name="remove-the-service"></a>Entfernen des Diensts

Nach einer kurzen Verzögerung zum Beenden eines Dienstes entfernen Sie den Dienst mit dem Powershell 6-Befehl `Remove-Service -Name {NAME}`.

Überprüfen Sie den Status des Diensts der Beispiel-App:

```powershell
Remove-Service -Name MyService
```

## <a name="handle-starting-and-stopping-events"></a>Behandeln von Start- und Stopereignissen

Nehmen Sie die folgenden zusätzlichen Änderungen vor, um Ereignisse vom Typ <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> und <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> zu handhaben:

1. Erstellen Sie eine von <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> abgeleitete Klasse mit den `OnStarting`-, `OnStarted`- und `OnStopping`-Methoden:

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. Erstellen Sie eine Erweiterungsmethode für <xref:Microsoft.AspNetCore.Hosting.IWebHost>, die den `CustomWebHostService` an <xref:System.ServiceProcess.ServiceBase.Run*> übergibt:

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. Rufen Sie in `Program.Main` anstelle von <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> die Erweiterungsmethode `RunAsCustomService` auf:

   ```csharp
   host.RunAsCustomService();
   ```

   Um den Speicherort von <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main` anzuzeigen, lesen Sie bitte das Codebeispiel im Abschnitt [Konvertieren eines Projekts in einen Windows-Dienst](#convert-a-project-into-a-windows-service).

## <a name="proxy-server-and-load-balancer-scenarios"></a>Proxyserver und Lastenausgleichsszenarien

Dienste, die mit Anforderungen aus dem Internet oder einem Unternehmensnetzwerk interagieren und hinter einem Proxy oder Lastenausgleich ausgeführt werden, erfordern möglicherweise zusätzliche Konfigurationen. Weitere Informationen finden Sie unter <xref:host-and-deploy/proxy-load-balancer>.

## <a name="configure-https"></a>HTTPS konfigurieren

So konfigurieren Sie den Dienst mit einem sicheren Endpunkt:

1. Erstellen Sie über die Mechanismen zum Abrufen und Bereitstellen eines Zertifikats für Ihre Plattform ein X.509-Zertifikat für das Hostsystem.

1. Geben Sie eine [Kestrel-Server-HTTPS-Endpunktkonfiguration](xref:fundamentals/servers/kestrel#endpoint-configuration) an, um das Zertifikat zu verwenden.

Die Verwendung des ASP.NET Core-HTTPS-Entwicklerzertifikats zum Schützen eines Dienstendpunkts wird nicht unterstützt.

## <a name="current-directory-and-content-root"></a>Aktuelles Verzeichnis und Inhaltsstammverzeichnis

Das aktuelle Arbeitsverzeichnis, das durch Aufrufen von <xref:System.IO.Directory.GetCurrentDirectory*> für einen Windows-Dienst zurückgegeben wird, ist der Ordner *C:\\WINDOWS\\system32*. Der Ordner *system32* ist kein geeigneter Speicherort für die Dateien eines Diensts (z.B. Einstellungsdateien). Verwenden Sie einen der folgenden Ansätze, um die Objekt- und Einstellungsdateien eines Dienstes beizubehalten und darauf zuzugreifen.

### <a name="set-the-content-root-path-to-the-apps-folder"></a>Legen Sie den Inhaltsstammpfad auf den Ordner der App fest.

<xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> entspricht dem gleichen Pfad, der für das Argument `binPath` bereitgestellt wird, wenn der Dienst erstellt wird. Anstatt `GetCurrentDirectory` aufzurufen, um Pfade zu Einstellungsdateien zu erstellen, rufen Sie <xref:System.IO.Directory.SetCurrentDirectory*> mit dem Pfad zum Inhaltsstamm der App auf.

Bestimmen Sie unter `Program.Main` den Pfad zum Ordner der ausführbaren Datei des Dienstes und verwenden Sie den Pfad, um das Inhaltsverzeichnis der App festzulegen:

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-the-services-files-in-a-suitable-location-on-disk"></a>Speichern Sie die Dateien des Dienstes an einem geeigneten Speicherort auf dem Datenträger.

Geben Sie mit <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> beim Verwenden von <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> einen absoluten Pfad zu dem Ordner an, der die Dateien enthält.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Kestrel-Endpunktkonfiguration](xref:fundamentals/servers/kestrel#endpoint-configuration) (einschließlich HTTPS-Konfiguration und Unterstützung für SNI)
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

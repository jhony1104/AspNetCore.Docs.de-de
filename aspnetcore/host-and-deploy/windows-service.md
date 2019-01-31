---
title: Hosten von ASP.NET Core in einem Windows-Dienst
author: guardrex
description: Erfahren Sie, wie eine ASP.NET Core-App in einem Windows-Dienst gehostet wird.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 01/22/2019
uid: host-and-deploy/windows-service
ms.openlocfilehash: eedaf64710506f2a2aac65c178a9888d2ab33d38
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837480"
---
# <a name="host-aspnet-core-in-a-windows-service"></a>Hosten von ASP.NET Core in einem Windows-Dienst

Von [Luke Latham](https://github.com/guardrex) und [Tom Dykstra](https://github.com/tdykstra)

Eine ASP.NET Core-App kann unter Windows als [Windows-Dienst](/dotnet/framework/windows-services/introduction-to-windows-service-applications) ohne die Verwendung von IIS gehostet werden. Wenn die App als Windows-Dienst gehostet wird, erfolgen Neustarts automatisch.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

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

  * Rufen Sie <xref:System.IO.Directory.SetCurrentDirectory*> auf, und verwenden Sie einen Pfad zum veröffentlichten Speicherort der App. Rufen Sie nicht <xref:System.IO.Directory.GetCurrentDirectory*> auf, um den Pfad abzurufen, da eine Windows-Dienst-App den Ordner *C:\\WINDOWS\\system32* zurückgibt, wenn `GetCurrentDirectory` aufgerufen wird. Weitere Informationen finden Sie im Abschnitt [Aktuelles Verzeichnis und Inhaltsstammverzeichnis](#current-directory-and-content-root).
  * Rufen Sie <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> auf, um die App als Dienst auszuführen.

  Da der [Anbieter der Befehlszeilenkonfiguration](xref:fundamentals/configuration/index#command-line-configuration-provider) Name/Wert-Paare für Befehlszeilenargumente benötigt, wird der `--console`-Schalter aus den Argumenten entfernt, bevor <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> sie empfängt.

* Um das Windows-Ereignisprotokoll zu schreiben, fügen Sie den EventLog-Anbieter zu <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*> hinzu. Legen Sie den Protokollierungsgrad mit dem `Logging:LogLevel:Default`-Schlüssel in der *appsettings.Production.json*-Datei fest. Zu Demonstrations- und Testzwecken setzt die Produktionseinstellungsdatei der Beispiel-App den Protokollierungsgrad auf `Information`. In der Produktion wird der Wert in der Regel auf `Error` festgelegt. Weitere Informationen finden Sie unter <xref:fundamentals/logging/index#windows-eventlog-provider>.

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

### <a name="publish-the-app"></a>Veröffentlichen der App

Veröffentlichen Sie die App mit [dotnet publish](/dotnet/articles/core/tools/dotnet-publish), einem [Visual Studio-Veröffentlichungsprofil](xref:host-and-deploy/visual-studio-publish-profiles) oder Visual Studio Code. Wählen Sie bei Verwendung von Visual Studio das **FolderProfile** aus, und konfigurieren Sie den **Zielspeicherort**, bevor Sie auf die Schaltfläche **Veröffentlichen** klicken.

Um die Beispiel-App mit CLI-Tools (Befehlszeilenschnittstelle) zu veröffentlichen, führen Sie den Befehl [dotnet publish](/dotnet/core/tools/dotnet-publish) an einer Eingabeaufforderung aus dem Projektordner mit einer Releasekonfiguration aus, die an die [-c|--configuration](/dotnet/core/tools/dotnet-publish#options)-Option übergeben wurde. Verwenden Sie die [-o|--output](/dotnet/core/tools/dotnet-publish#options)-Option mit einem Pfad für die Veröffentlichung in einem Ordner außerhalb der App.

#### <a name="publish-a-framework-dependent-deployment-fdd"></a>Veröffentlichen einer Framework-abhängigen Bereitstellung

Im folgenden Beispiel wird die App im Ordner *c:\\svc* veröffentlicht:

```console
dotnet publish --configuration Release --output c:\svc
```

#### <a name="publish-a-self-contained-deployment-scd"></a>Veröffentlichen einer eigenständigen Bereitstellung

Der RID muss in der Eigenschaft `<RuntimeIdenfifier>` (oder `<RuntimeIdentifiers>`) der Projektdatei angegeben werden. Stellen Sie die Runtime für die [-r|--runtime](/dotnet/core/tools/dotnet-publish#options)-Option des `dotnet publish`-Befehls bereit.

Im folgenden Beispiel wird die App für die `win7-x64`-Runtime im Ordner *c:\\svc* veröffentlicht:

```console
dotnet publish --configuration Release --runtime win7-x64 --output c:\svc
```

### <a name="create-a-user-account"></a>Erstellen eines Benutzerkontos

Erstellen Sie mit dem `net user`-Befehl ein Benutzerkonto für den Dienst:

```console
net user {USER ACCOUNT} {PASSWORD} /add
```

Erstellen Sie für die Beispiel-App ein Benutzerkonto mit dem Namen `ServiceUser` und einem Kennwort. Ersetzen Sie im folgenden Befehl `{PASSWORD}` durch ein [sicheres Kennwort](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements).

```console
net user ServiceUser {PASSWORD} /add
```

Wenn Sie den Benutzer einer Gruppe hinzufügen müssen, verwenden Sie den Befehl `net localgroup`. Hierbei steht `{GROUP}` für den Namen der Gruppe:

```console
net localgroup {GROUP} {USER ACCOUNT} /add
```

Weitere Informationen finden Sie unter [Dienstbenutzerkonten](/windows/desktop/services/service-user-accounts).

### <a name="set-permissions"></a>Festlegen von Berechtigungen

Gewähren Sie mit dem Befehl [icacls](/windows-server/administration/windows-commands/icacls) Schreib-/Lese-/Ausführungszugriff für den App-Ordner:

```console
icacls "{PATH}" /grant {USER ACCOUNT}:(OI)(CI){PERMISSION FLAGS} /t
```

* `{PATH}` &ndash; Pfad zum App-Ordner.
* `{USER ACCOUNT}` &ndash; Das Benutzerkonto (SID).
* `(OI)`&ndash; Mit dem Flag für die Objektvererbung werden Berechtigungen an untergeordnete Dateien weitergegeben.
* `(CI)`&ndash; Mit dem Flag für die Containervererbung werden Berechtigungen an untergeordnete Ordner weitergegeben.
* `{PERMISSION FLAGS}` &ndash; Legt die Zugriffsberechtigungen für die App fest.
  * Schreiben (`W`)
  * Lesen (`R`)
  * Ausführen (`X`)
  * Vollzugriff (`F`)
  * Ändern (`M`)
* `/t` &ndash; Rekursive Anwendung auf vorhandene untergeordnete Ordner und Dateien.

Verwenden Sie für die im Ordner *c:\\svc* veröffentlichte Beispiel-App und das Konto `ServiceUser` mit Schreib-/Lese-/Ausführungsberechtigungen den folgenden Befehl:

```console
icacls "c:\svc" /grant ServiceUser:(OI)(CI)WRX /t
```

Weitere Informationen finden Sie unter [icacls](/windows-server/administration/windows-commands/icacls).

## <a name="manage-the-service"></a>Verwalten des Diensts

### <a name="create-the-service"></a>Erstellen Sie den Dienst.

Verwenden Sie das Befehlszeilentool [sc.exe](https://technet.microsoft.com/library/bb490995), um den Dienst zu erstellen. Der Wert `binPath` ist der Pfad zu der ausführbaren Datei der App, der den Namen der ausführbaren Datei enthält. **Das Leerzeichen zwischen dem Gleichheitszeichen und dem Anführungszeichen für jeden Parameter und Wert ist erforderlich.**

```console
sc create {SERVICE NAME} binPath= "{PATH}" obj= "{DOMAIN}\{USER ACCOUNT}" password= "{PASSWORD}"
```

* `{SERVICE NAME}` &ndash; Der Name, der dem Dienst im [Dienststeuerungs-Manager](/windows/desktop/services/service-control-manager) zugewiesen wird.
* `{PATH}`&ndash; Der Pfad zur ausführbaren Datei des Diensts.
* `{DOMAIN}` &ndash; Die Domäne eines in eine Domäne eingebundenen Computers. Wenn der Computer nicht in die Domäne eingebunden ist, ist dies der Name des lokalen Computers.
* `{USER ACCOUNT}` &ndash; Das Benutzerkonto, unter dem der Dienst ausgeführt wird.
* `{PASSWORD}` &ndash; Das Kennwort für das Benutzerkonto.

> [!WARNING]
> Lassen Sie den Parameter`obj` **nicht** aus. Der Standardwert für `obj` ist das [LocalSystem-Konto](/windows/desktop/services/localsystem-account). Die Ausführung eines Diensts mit dem `LocalSystem`-Konto stellt ein erhebliches Sicherheitsrisiko dar. Führen Sie einen Dienst immer mit einem Benutzerkonto mit eingeschränkten Berechtigungen aus.

Im folgenden Beispiel für die Beispiel-App:

* Der Dienst heißt **MyService**.
* Der veröffentlichte Dienst ist im Ordner *c:\\svc* vorhanden. Die ausführbare Datei der App heißt *SampleApp.exe*. Setzen Sie den `binPath`-Wert in doppelte Anführungszeichen (").
* Der Dienst wird mit dem Konto `ServiceUser` ausgeführt. Ersetzen Sie `{DOMAIN}` durch den Domänennamen oder den lokalen Computernamen für das Benutzerkonto. Setzen Sie den `obj`-Wert in doppelte Anführungszeichen ("). Beispiel: Wenn das Hostsystem ein lokaler Computer namens `MairaPC` ist, legen Sie `obj` auf `"MairaPC\ServiceUser"` fest.
* Ersetzen Sie `{PASSWORD}` durch das Kennwort für das Benutzerkonto. Setzen Sie den `password`-Wert in doppelte Anführungszeichen (").

```console
sc create MyService binPath= "c:\svc\sampleapp.exe" obj= "{DOMAIN}\ServiceUser" password= "{PASSWORD}"
```

> [!IMPORTANT]
> Stellen Sie sicher, dass Leerzeichen zwischen den Gleichheitszeichen des Parameters und den Parameterwerten vorhanden sind.

### <a name="start-the-service"></a>Starten des Diensts

Starten Sie den Dienst mithilfe des Befehls `sc start {SERVICE NAME}`.

Verwenden Sie zum Starten des Diensts der Beispiel-App den folgenden Befehl:

```console
sc start MyService
```

Das Starten des Diensts dauert ein paar Sekunden.

### <a name="determine-the-service-status"></a>Ermitteln des Dienststatus

Um den Status des Diensts zu überprüfen, verwenden Sie den `sc query {SERVICE NAME}`-Befehl. Der Status wird als einer der folgenden Werte gemeldet:

* `START_PENDING`
* `RUNNING`
* `STOP_PENDING`
* `STOPPED`

Verwenden Sie den folgenden Befehl, um den Status des Diensts der Beispiel-App zu überprüfen:

```console
sc query MyService
```

### <a name="browse-a-web-app-service"></a>Durchsuchen eines Web-App-Diensts

Befindet sich der Dienst im Status `RUNNING`, und ist der Dienst gleichzeitig eine Web-App, rufen Sie die App über ihren Pfad auf (Standardpfad ist `http://localhost:5000`, der umgeleitet wird zu `https://localhost:5001`, wenn [Middleware für HTTPS-Umleitung](xref:security/enforcing-ssl) verwendet wird).

Rufen Sie die App für den Dienst der Beispiel-App über `http://localhost:5000` auf.

### <a name="stop-the-service"></a>Dienst beenden

Verwenden Sie zum Beenden des Diensts den Befehl `sc stop {SERVICE NAME}`.

Verwenden Sie zum Beenden des Diensts der Beispiel-App den folgenden Befehl:

```console
sc stop MyService
```

### <a name="delete-the-service"></a>Löschen des Diensts

Deinstallieren Sie den Dienst nach einer kurzen Verzögerung zum Beenden des Diensts mit dem Befehl `sc delete {SERVICE NAME}`.

Überprüfen Sie den Status des Diensts der Beispiel-App:

```console
sc query MyService
```

Befindet sich der Dienst der Beispiel-App im Status `STOPPED`, verwenden Sie zum Deinstallieren des Diensts der Beispiel-App den folgenden Befehl:

```console
sc delete MyService
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

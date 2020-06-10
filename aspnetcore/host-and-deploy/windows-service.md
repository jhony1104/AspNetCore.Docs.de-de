---
title: 'title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:'
author: rick-anderson
description: "'Blazor'"
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/windows-service
ms.openlocfilehash: af0c3836362233e41a79e72bd28b4a331e9763bc
ms.sourcegitcommit: 58722eb309767e462bdbf3082bd38737a4ef168f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84106480"
---
# <a name="host-aspnet-core-in-a-windows-service"></a>'Identity'

::: moniker range=">= aspnetcore-3.0"

'Let's Encrypt' 'Razor'

'SignalR' uid:

## <a name="prerequisites"></a>Hosten von ASP.NET Core in einem Windows-Dienst

* Eine ASP.NET Core-App kann unter Windows als [Windows-Dienst](/dotnet/framework/windows-services/introduction-to-windows-service-applications) ohne die Verwendung von IIS gehostet werden.
* Wenn die App als Windows-Dienst gehostet und der Server neu gestartet wird, startet diese automatisch.

## <a name="worker-service-template"></a>[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Voraussetzungen [ASP.NET Core SDK 2.1 oder höher](https://dotnet.microsoft.com/download)

1. [PowerShell 6.2 oder höher](https://github.com/PowerShell/PowerShell)
1. Workerdienstvorlage

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a>Die ASP.NET Core-Workerdienstvorlage dient als Ausgangspunkt für das Programmieren von zeitintensiven Dienstanwendungen.

So können Sie diese Vorlage als Grundlage für eine Windows-Dienstanwendung verwenden:

Erstellen Sie eine Workerdienstanwendung über die .NET Core-Vorlage. Befolgen Sie die Anweisungen im Abschnitt [App-Konfiguration](#app-configuration), um die Workerdienstanwendung so zu aktualisieren, dass sie als Windows-Dienst ausgeführt werden kann.

* App-Konfiguration
* Die App erfordert einen Paketverweis auf [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices). Beim Erstellen des Hosts wird `IHostBuilder.UseWindowsService` aufgerufen.
* Wenn die App als Windows-Dienst ausgeführt wird, sorgt die Methode für Folgendes:
  * Sie legt die Lebensdauer des Hosts auf `WindowsServiceLifetime` fest.
  * [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory) wird als [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) festgelegt.
  * Weitere Informationen finden Sie im Abschnitt [Aktuelles Verzeichnis und Inhaltsstammverzeichnis](#current-directory-and-content-root).
  * Ermöglicht die Protokollierung zum Ereignisprotokoll: Der Anwendungsname wird als Standardname für die Quelle verwendet.

Für eine App basierend auf einer ASP.NET Core-Vorlage, die `CreateDefaultBuilder` zur Erstellung des Hosts aufruft, lautet die Standardprotokollebene *Warnung* oder höher.

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

Setzen Sie die Standardprotokollebene mit dem `Logging:EventLog:LogLevel:Default`-Schlüssel in *appsettings.json*/*appsettings.{Environment}.json* oder einem anderen Konfigurationsanbieter außer Kraft.

* Nur Administratoren können neue Ereignisquellen erstellen.
* Wenn keine Ereignisquellen mithilfe des Anwendungsnamens erstellt werden können, wird in der *Anwendungsquelle* eine Warnung protokolliert, und die Ereignisprotokolle werden deaktiviert.

In `CreateHostBuilder` von *Program.cs*:

## <a name="deployment-type"></a>In diesem Thema werden die folgenden Beispiel-Apps behandelt:

Beispiel für einen Hintergrundworkerdienst: Dabei handelt es sich um ein Beispiel einer nicht webbasierten App, die auf der [Workerdienstvorlage](#worker-service-template) basiert und [gehostete Dienste](xref:fundamentals/host/hosted-services) für Hintergrundaufgaben verwendet.

### <a name="sdk"></a>Beispiel für einen Web-App-Dienst: Dabei handelt es sich um ein Razor Pages-Web-App-Beispiel, das als Windows-Dienst mit [gehosteten Diensten](xref:fundamentals/host/hosted-services) für Hintergrundaufgaben ausgeführt wird.

MVC-Anleitungen finden Sie in den Artikeln unter <xref:mvc/overview> und <xref:migration/22-to-30>.

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Bereitstellungstyp

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a>Weitere Informationen und Tipps zu Bereitstellungsszenarien finden Sie unter [.NET Core-Anwendungsbereitstellung](/dotnet/core/deploying/).

SDK Geben Sie für einen Web-App-basierten Dienst, der die Razor Pages- oder MVC-Frameworks verwendet, das Web-SDK in der Projektdatei an:

Geben Sie das Worker-SDK in der Projektdatei an, wenn der Dienst nur Hintergrundaufgaben ausführt (z. B. [gehostete Dienste](xref:fundamentals/host/hosted-services)): Frameworkabhängige Bereitstellung (Framework-dependent deployment, FDD)

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a>Eine Framework-abhängige Bereitstellung (Framework-Dependent Deployment, FDD) benötigt eine gemeinsame systemweite Version von .NET Core auf dem Zielsystem.

Wenn das FDD-Szenario gemäß der Anleitung in diesem Artikel übernommen wird, erzeugt das SDK eine ausführbare Datei ( *.exe*). Diese wird als *frameworkabhängige ausführbare Datei* bezeichnet. Bei Verwendung des [Web-SDK](#sdk) ist eine *web.config*-Datei, die normalerweise erstellt wird, wenn Sie eine ASP.NET Core-App veröffentlichen, für eine Windows Services-App nicht erforderlich.

Um die Erstellung der *web.config*-Datei zu deaktivieren, fügen Sie die auf `true` festgelegte `<IsTransformWebConfigDisabled>`-Eigenschaft hinzu.

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

Eigenständige Bereitstellung

* Eigenständige Bereitstellungen (Self-Contained Deployment, SCD) benötigen kein gemeinsames Framework auf dem Hostsystem.
* Die Runtime und die App-Abhängigkeiten werden mit der App bereitgestellt.

Ein [Runtimebezeichner](/dotnet/core/rid-catalog) (Runtime Identifier, RID) für Windows ist im `<PropertyGroup>`-Objekt enthalten, das wiederum das Zielframework enthält:

## <a name="service-user-account"></a>So führen Sie die Veröffentlichung für mehrere RIDs aus:

Geben Sie die RIDs in einer durch Semikolons getrennten Liste an.

Verwenden Sie den Eigenschaftennamen [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (Plural).

```powershell
New-LocalUser -Name {SERVICE NAME}
```

Weitere Informationen finden Sie im [.NET Core RID-Katalog](/dotnet/core/rid-catalog).

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

Benutzerkonten für Dienste

Wenn Sie ein Benutzerkonto für einen Dienst erstellen möchten, verwenden Sie das Cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) in einer PowerShell 6-Befehlsshell.

Unter Windows 10 mit dem Update von Oktober 2018 (Version 1809/Build 10.0.17763) oder höher:

Unter Windows mit einer früheren Betriebssystemversion als Windows 10 mit dem Update von Oktober 2018 (Version 1809/Build 10.0.17763): Geben Sie bei Aufforderung ein [sicheres Kennwort](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) ein.

## <a name="log-on-as-a-service-rights"></a>Solange der Parameter `-AccountExpires` dem Cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) nicht mit einem Ablaufdatum <xref:System.DateTime> angegeben wird, läuft das Konto nicht ab.

Weitere Informationen finden Sie unter [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) und [Dienstbenutzerkonten](/windows/desktop/services/service-user-accounts).

1. Eine alternative Methode zum Verwalten von Benutzern bei Verwendung von Active Directory ist die Verwendung von verwalteten Dienstkonten.
1. Weitere Informationen finden Sie unter [Gruppenverwaltete Dienstkonten: Übersicht](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).
1. Rechte zum Anmelden als Dienst
1. Gehen Sie wie folgt vor, um Rechte zum *Anmelden als Dienst* für das Benutzerkonto eines Diensts einzurichten:
1. Öffnen Sie den Editor für lokale Sicherheitsrichtlinien, indem Sie die Datei *secpol.msc* ausführen.
   1. Erweitern Sie den Knoten **Lokale Richtlinien**, und klicken Sie auf **Zuweisen von Benutzerrechten**.
   1. Öffnen Sie die Richtlinie **Anmelden als Dienst**. Wählen Sie **Benutzer oder Gruppe hinzufügen** aus. Geben Sie den Objektnamen (das Benutzerkonto) an. Dafür gibt es zwei Möglichkeiten: Geben Sie die Bezeichnung des Benutzerkontos (`{DOMAIN OR COMPUTER NAME\USER}`) in das Feld für den Objektnamen ein, und klicken Sie auf **OK**, um den Benutzer der Richtlinie hinzuzufügen. Wählen Sie **Erweitert** aus.
1. Klicken Sie auf **Find Now** (Suche starten).

## <a name="create-and-manage-the-windows-service"></a>Wählen Sie das betreffende Benutzerkonto aus der Liste aus.

### <a name="create-a-service"></a>Klicken Sie auf **OK**.

Klicken Sie erneut auf **OK**, um den Benutzer zur Richtlinie hinzuzufügen. Klicken Sie auf **OK** oder auf **Anwenden**, um die Änderungen zu übernehmen.

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* Erstellen und Verwalten des Windows-Diensts Erstellen eines Diensts Verwenden Sie PowerShell-Befehle, um einen Dienst zu registrieren.
* Führen Sie über eine administrative PowerShell 6-Befehlsshell die folgenden Befehle aus:
* `{EXE PATH}`: entspricht dem Pfad zum Ordner der App auf dem Host (z. B. `d:\myservice`).
* Fügen Sie nicht die ausführbare Datei der App in den Pfad ein. Außerdem benötigen Sie keinen nachstehenden Schrägstrich.
* `{DOMAIN OR COMPUTER NAME\USER}`: entspricht dem Benutzerkonto für den Dienst (z. B. `Contoso\ServiceUser`).
* `{SERVICE NAME}`: entspricht dem Dienstnamen (z. B. `MyService`).

### <a name="start-a-service"></a>`{EXE FILE PATH}`: entspricht dem Pfad zur ausführbaren Datei der App (z. B. `d:\myservice\myservice.exe`).

Fügen Sie den Namen der ausführbaren Datei einschließlich ihrer Erweiterung hinzu.

```powershell
Start-Service -Name {SERVICE NAME}
```

`{DESCRIPTION}`: ist eine Beschreibung des Diensts (z. B. `My sample service`).

### <a name="determine-a-services-status"></a>`{DISPLAY NAME}`: entspricht dem Anzeigenamen des Diensts (z. B. `My Service`).

Starten eines Diensts

```powershell
Get-Service -Name {SERVICE NAME}
```

Sie können Dienste mithilfe des folgenden PowerShell 6-Befehls starten:

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a>Das Starten des Diensts dauert ein paar Sekunden.

Ermitteln des Status eines Diensts

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a>Sie können den Status von Diensten mithilfe des folgenden PowerShell 6-Befehls überprüfen:

Der Status wird als einer der folgenden Werte gemeldet:

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a>Beenden eines Diensts

Sie können Dienste mithilfe des folgenden PowerShell 6-Befehls beenden: Entfernen eines Diensts

## <a name="configure-endpoints"></a>Nach einer kurzen Verzögerung durch das Beenden des Diensts können Sie diesen mithilfe des folgenden Powershell 6-Befehls entfernen:

Proxyserver und Lastenausgleichsszenarien Dienste, die mit Anforderungen aus dem Internet oder einem Unternehmensnetzwerk interagieren und hinter einem Proxy oder Lastenausgleich ausgeführt werden, erfordern möglicherweise zusätzliche Konfigurationen.

Weitere Informationen finden Sie unter <xref:host-and-deploy/proxy-load-balancer>.

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

Konfigurieren von Endpunkten Standardmäßig ist ASP.NET Core an `http://localhost:5000` gebunden.

> [!NOTE]
> Konfigurieren Sie URL und Port, indem Sie die `ASPNETCORE_URLS`-Umgebungsvariable festlegen.

## <a name="current-directory-and-content-root"></a>Weitere Informationen zu den Konfigurationsansätzen für URLs und Ports finden Sie im relevanten Serverartikel:

Die vorangehende Anleitung behandelt die Unterstützung für HTTPS-Endpunkte. Konfigurieren Sie z. B. die APP für HTTPS, wenn die Authentifizierung mit einem Windows-Dienst verwendet wird. Die Verwendung des ASP.NET Core-HTTPS-Entwicklerzertifikats zum Schützen eines Dienstendpunkts wird nicht unterstützt.

### <a name="use-contentrootpath-or-contentrootfileprovider"></a>Aktuelles Verzeichnis und Inhaltsstammverzeichnis

Das aktuelle Arbeitsverzeichnis, das durch Aufrufen von <xref:System.IO.Directory.GetCurrentDirectory*> für einen Windows-Dienst zurückgegeben wird, ist der Ordner *C:\\WINDOWS\\system32*.

Der Ordner *system32* ist kein geeigneter Speicherort für die Dateien eines Diensts (z.B. Einstellungsdateien).

Verwenden Sie einen der folgenden Ansätze, um die Objekt- und Einstellungsdateien eines Dienstes beizubehalten und darauf zuzugreifen.

Verwenden von ContentRootPath oder ContentRootFileProvider Verwenden Sie [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) oder <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider>, um die Ressourcen einer App zu finden.

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

Wenn die App als Dienst ausgeführt wird, [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory) durch <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> für <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> festgelegt.

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a>Die Standardeinstellungsdateien *appsettings.json* und *appsettings.{Environment}.json* der App, werden über das Inhaltsstammverzeichnis der App geladen, indem [CreateDefaultBuilder](xref:fundamentals/host/generic-host#set-up-a-host) während der Hosterstellung aufgerufen wird.

Für andere Einstellungsdateien, die in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> durch Entwicklercode geladen werden, muss <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> nicht aufgerufen werden.

## <a name="troubleshoot"></a>Im folgenden Beispiel ist die Datei *custom_settings.json* im Inhaltsstammverzeichnis der App enthalten und wird geladen, ohne das ein Basispfad explizit festgelegt wird:

Versuchen Sie nicht, <xref:System.IO.Directory.GetCurrentDirectory*> zum Abrufen eines Ressourcenpfads zu verwenden, da eine Windows-Dienstanwendung den Ordner *C:\\WINDOWS\\system32* als aktuelles Verzeichnis zurückgibt.

### <a name="common-errors"></a>Speichern der Dateien eines Diensts an einem geeigneten Ort auf dem Datenträger

* Geben Sie mit <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> beim Verwenden von <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> einen absoluten Pfad zu dem Ordner an, der die Dateien enthält.
* Problembehandlung Informationen zur Problembehandlung für Windows-Dienstanwendungen finden Sie unter <xref:test/troubleshoot>. Häufige Fehler
  * Eine alte oder eine Vorabversion von PowerShell wird verwendet.
  * Der registrierte Dienst verwendet nicht die **veröffentlichte** Ausgabe des Befehls [dotnet publish](/dotnet/core/tools/dotnet-publish) der Anwendung.
* Die Ausgabe des Befehls [dotnet build](/dotnet/core/tools/dotnet-build) wird nicht für die Anwendungsbereitstellung unterstützt.
* Veröffentlichte Ressourcen sind je nach Bereitstellungstyp in einem der folgenden Ordner vorzufinden: *bin/Release/{ZIELFRAMEWORK}/publish* (FDD)
* *bin/Release/{ZIELFRAMEWORK}/{RUNTIME-ID}/publish* (SCD)
* Der Dienst weist nicht den Status „WIRD AUSGEFÜHRT“ auf.
* Die App enthält falsche Pfade zu Ressourcen (z. B. Zertifikate).
* Der Basispfad eines Windows-Diensts lautet: *C:\\Windows\\System32*.

### <a name="system-and-application-event-logs"></a>Der Benutzer verfügt nicht über die Berechtigung *Als Dienst anmelden*.

Das Kennwort des Benutzers ist abgelaufen oder wird nicht ordnungsgemäß übermittelt, wenn der PowerShell-Befehl `New-Service` ausgeführt wird.

1. Die App erfordert ASP.NET Core-Authentifizierung, aber sie wurde nicht für sichere (HTTPS-)Verbindungen konfiguriert.
1. Der Port für die Anforderungs-URL in der App ist fehlerhaft oder nicht ordnungsgemäß konfiguriert.
1. System- und Anwendungsereignisprotokolle So greifen Sie auf die System- und Anwendungsereignisprotokolle zu:
1. Öffnen Sie das Startmenü, suchen Sie nach der *Ereignisanzeige*, und wählen Sie dann die App **Ereignisanzeige** aus.

### <a name="run-the-app-at-a-command-prompt"></a>Öffnen Sie unter **Ereignisanzeige** den Knoten **Windows-Protokolle**.

Klicken Sie auf **System**, um das Systemereignisprotokoll zu öffnen. Wählen Sie **Anwendung** aus, um das Anwendungsereignisprotokoll zu öffnen. Suchen Sie nach Fehlern, die mit der fehlerhaften App im Zusammenhang stehen.

### <a name="clear-package-caches"></a>Ausführen der App in einer Eingabeaufforderung

Viele Startfehler erzeugen keine nützlichen Informationen in den Ereignisprotokollen. Sie können die Ursache für einige Fehler ermitteln, indem Sie die App in einer Eingabeaufforderung auf dem Hostsystem ausführen. Senken Sie den [Protokolliergrad](xref:fundamentals/logging/index#log-level), oder führen Sie die App in der [Entwicklungsumgebung](xref:fundamentals/environments) aus, um zusätzliche Informationen von der App zu protokollieren.

1. Bereinigen der Paketcaches
1. Eine funktionsfähige App kann direkt nach der Durchführung eines Upgrades für das .NET Core SDK auf dem Entwicklungscomputer oder einer Änderung der Paketversionen in der App fehlschlagen.

   In einigen Fällen können inkohärente Pakete eine App beschädigen, wenn größere Upgrades durchgeführt werden. Die meisten dieser Probleme können durch Befolgung der folgenden Anweisungen behoben werden:

1. Löschen Sie die Ordner *bin* und *obj*.
1. Bereinigen Sie die Paketcaches, indem Sie [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) über eine Befehlsshell ausführen.

### <a name="slow-or-hanging-app"></a>Sie können die Paketcaches auch mit dem Tool [nuget.exe](https://www.nuget.org/downloads) und dem Befehl `nuget locals all -clear` bereinigen.

*nuget.exe* ist wird unter dem Windows Desktop-Betriebssystem nicht gebündelt installiert und muss separat von der [NuGet-Website](https://www.nuget.org/downloads) abgerufen werden.

#### <a name="app-crashes-or-encounters-an-exception"></a>Stellen Sie das Projekt wieder her und erstellen Sie es neu.

Löschen Sie alle Dateien im Bereitstellungsordner auf dem Server, bevor Sie die App noch mal bereitstellen.

1. Langsame oder hängende App
1. Ein *Absturzabbild* ist eine Momentaufnahme des Systemarbeitsspeichers, die Ihnen dabei behilflich sein kann, die Ursache eines App-Absturzes, eines Startfehlers oder einer langsamen App zu ermitteln.

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. App stürzt ab, oder es tritt eine Ausnahme auf
1. Abrufen und Analysieren eines Speicherabbilds aus der [Windows-Fehlerberichterstattung (WER)](/windows/desktop/wer/windows-error-reporting):

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

Erstellen Sie einen Ordner zum Speichern von Absturzabbilddateien unter `c:\dumps`. Führen Sie das PowerShell-Skript [EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) mit dem Namen des ausführbaren Anwendung aus:

> [!WARNING]
> Führen Sie die App unter den Bedingungen aus, die dazu führen, dass der Absturz auftritt.

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>Nachdem der Absturz stattgefunden hat, führen Sie das [PowerShell-Skript „DisableDumps“](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1) aus:

Nachdem eine Anwendung abgestürzt ist und die Absturzabbildsammlung abgeschlossen ist, kann die App ordnungsgemäß beendet werden.

#### <a name="analyze-the-dump"></a>Das PowerShell-Skript konfiguriert WER für die Erfassung von bis zu fünf Absturzabbildern pro App.

Absturzabbilder können eine große Menge Speicherplatz in Anspruch nehmen (jeweils bis zu mehreren GB). App hat sich aufgehängt, es tritt ein Fehler während des Starts auf, oder sie wird normal ausgeführt

## <a name="additional-resources"></a>Wenn sich eine App *aufgehängt* hat (nicht mehr reagiert, aber nicht abstürzt), ein Fehler während des Starts auftritt bzw. die App normal ausgeführt wird, finden Sie weitere Informationen unter [Benutzermodus-Absturzabbilddateien: Auswählen des besten Tools](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool), um ein geeignetes Tool zum Generieren des Speicherabbilds auszuwählen.

* Analysieren des Speicherabbilds
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Ein Speicherabbild kann mithilfe mehrerer Ansätze analysiert werden. Weitere Informationen finden Sie unter [Analysieren einer Benutzermodus-Speicherabbilddatei](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).

Zusätzliche Ressourcen

## <a name="prerequisites"></a>[Kestrel-Endpunktkonfiguration](xref:fundamentals/servers/kestrel#endpoint-configuration) (einschließlich HTTPS-Konfiguration und Unterstützung für SNI)

* Eine ASP.NET Core-App kann unter Windows als [Windows-Dienst](/dotnet/framework/windows-services/introduction-to-windows-service-applications) ohne die Verwendung von IIS gehostet werden.
* Wenn die App als Windows-Dienst gehostet und der Server neu gestartet wird, startet diese automatisch.

## <a name="app-configuration"></a>[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Voraussetzungen

[ASP.NET Core SDK 2.1 oder höher](https://dotnet.microsoft.com/download) [PowerShell 6.2 oder höher](https://github.com/PowerShell/PowerShell) App-Konfiguration Die App benötigt Paketverweise für [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) und [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).

* Um bei der Ausführung außerhalb eines Dienstes zu testen und zu debuggen, fügen Sie Code hinzu, um festzustellen, ob die Anwendung als Dienst oder als Konsolenanwendung ausgeführt wird. Überprüfen Sie, ob der Debugger angefügt ist oder ob es einen `--console`-Parameter gibt. Wenn eine dieser Bedingungen erfüllt ist (die App wird nicht als Dienst ausgeführt), rufen Sie <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> auf. Wenn die Bedingungen nicht zutreffen (die App als Dienst ausgeführt wird):
* Rufen Sie <xref:System.IO.Directory.SetCurrentDirectory*> auf, und verwenden Sie einen Pfad zum veröffentlichten Speicherort der App.

Rufen Sie nicht <xref:System.IO.Directory.GetCurrentDirectory*> auf, um den Pfad abzurufen, da eine Windows-Dienst-App den Ordner *C:\\WINDOWS\\system32* zurückgibt, wenn <xref:System.IO.Directory.GetCurrentDirectory*> aufgerufen wird.

Weitere Informationen finden Sie im Abschnitt [Aktuelles Verzeichnis und Inhaltsstammverzeichnis](#current-directory-and-content-root). Dieser Schritt wird ausgeführt, bevor die App in `CreateWebHostBuilder` konfiguriert wird.

Rufen Sie <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> auf, um die App als Dienst auszuführen. Da der [Anbieter der Befehlszeilenkonfiguration](xref:fundamentals/configuration/index#command-line-configuration-provider) Name/Wert-Paare für Befehlszeilenargumente benötigt, wird der Parameter `--console` aus den Argumenten entfernt, bevor <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> diese empfängt.

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a>Um das Windows-Ereignisprotokoll zu schreiben, fügen Sie den EventLog-Anbieter zu <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*> hinzu.

Legen Sie den Protokollierungsgrad mit dem `Logging:LogLevel:Default`-Schlüssel in der *appsettings.Production.json*-Datei fest.

### <a name="sdk"></a>Im folgenden Beispiel aus der Beispiel-App wird `RunAsCustomService` anstelle von <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> aufgerufen, um Ereignisse in Bezug auf die Lebensdauer innerhalb der App zu verarbeiten.

Weitere Informationen finden Sie im Abschnitt [Behandeln von Start- und Stopereignissen](#handle-starting-and-stopping-events).

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Bereitstellungstyp

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a>Weitere Informationen und Tipps zu Bereitstellungsszenarien finden Sie unter [.NET Core-Anwendungsbereitstellung](/dotnet/core/deploying/).

SDK Geben Sie für einen Web-App-basierten Dienst, der die Razor Pages- oder MVC-Frameworks verwendet, das Web-SDK in der Projektdatei an:

Geben Sie das Worker-SDK in der Projektdatei an, wenn der Dienst nur Hintergrundaufgaben ausführt (z. B. [gehostete Dienste](xref:fundamentals/host/hosted-services)): Frameworkabhängige Bereitstellung (Framework-dependent deployment, FDD) Eine Framework-abhängige Bereitstellung (Framework-Dependent Deployment, FDD) benötigt eine gemeinsame systemweite Version von .NET Core auf dem Zielsystem. Wenn das FDD-Szenario gemäß der Anleitung in diesem Artikel übernommen wird, erzeugt das SDK eine ausführbare Datei ( *.exe*). Diese wird als *frameworkabhängige ausführbare Datei* bezeichnet.

Der [Runtimebezeichner](/dotnet/core/rid-catalog) (Runtime Identifier, RID) für Windows ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) enthält das Zielframework. Im folgenden Beispiel wird die RID auf `win7-x64` festgelegt.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a>Die `<SelfContained>`-Eigenschaft ist auf `false` festgelegt.

Diese Eigenschaften geben dem SDK die Anweisung, eine ausführbare Datei ( *.exe*) für Windows sowie eine App zu generieren, die vom freigegebenen .NET Core-Framework abhängig ist. Eine *web.config*-Datei, die normalerweise erstellt wird, wenn Sie eine ASP.NET Core-App veröffentlichen, ist für eine Windows Services-App nicht erforderlich.

Um die Erstellung der *web.config*-Datei zu deaktivieren, fügen Sie die auf `true` festgelegte `<IsTransformWebConfigDisabled>`-Eigenschaft hinzu.

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

Eigenständige Bereitstellung

* Eigenständige Bereitstellungen (Self-Contained Deployment, SCD) benötigen kein gemeinsames Framework auf dem Hostsystem.
* Die Runtime und die App-Abhängigkeiten werden mit der App bereitgestellt.

Ein [Runtimebezeichner](/dotnet/core/rid-catalog) (Runtime Identifier, RID) für Windows ist im `<PropertyGroup>`-Objekt enthalten, das wiederum das Zielframework enthält:

So führen Sie die Veröffentlichung für mehrere RIDs aus:

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a>Geben Sie die RIDs in einer durch Semikolons getrennten Liste an.

Verwenden Sie den Eigenschaftennamen [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (Plural).

Weitere Informationen finden Sie im [.NET Core RID-Katalog](/dotnet/core/rid-catalog).

```powershell
New-LocalUser -Name {SERVICE NAME}
```

Eine `<SelfContained>`-Eigenschaft wird auf `true` festgelegt:

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

Benutzerkonten für Dienste

Wenn Sie ein Benutzerkonto für einen Dienst erstellen möchten, verwenden Sie das Cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) in einer PowerShell 6-Befehlsshell.

Unter Windows 10 mit dem Update von Oktober 2018 (Version 1809/Build 10.0.17763) oder höher:

Unter Windows mit einer früheren Betriebssystemversion als Windows 10 mit dem Update von Oktober 2018 (Version 1809/Build 10.0.17763): Geben Sie bei Aufforderung ein [sicheres Kennwort](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) ein.

## <a name="log-on-as-a-service-rights"></a>Solange der Parameter `-AccountExpires` dem Cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) nicht mit einem Ablaufdatum <xref:System.DateTime> angegeben wird, läuft das Konto nicht ab.

Weitere Informationen finden Sie unter [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) und [Dienstbenutzerkonten](/windows/desktop/services/service-user-accounts).

1. Eine alternative Methode zum Verwalten von Benutzern bei Verwendung von Active Directory ist die Verwendung von verwalteten Dienstkonten.
1. Weitere Informationen finden Sie unter [Gruppenverwaltete Dienstkonten: Übersicht](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).
1. Rechte zum Anmelden als Dienst
1. Gehen Sie wie folgt vor, um Rechte zum *Anmelden als Dienst* für das Benutzerkonto eines Diensts einzurichten:
1. Öffnen Sie den Editor für lokale Sicherheitsrichtlinien, indem Sie die Datei *secpol.msc* ausführen.
   1. Erweitern Sie den Knoten **Lokale Richtlinien**, und klicken Sie auf **Zuweisen von Benutzerrechten**.
   1. Öffnen Sie die Richtlinie **Anmelden als Dienst**. Wählen Sie **Benutzer oder Gruppe hinzufügen** aus. Geben Sie den Objektnamen (das Benutzerkonto) an. Dafür gibt es zwei Möglichkeiten: Geben Sie die Bezeichnung des Benutzerkontos (`{DOMAIN OR COMPUTER NAME\USER}`) in das Feld für den Objektnamen ein, und klicken Sie auf **OK**, um den Benutzer der Richtlinie hinzuzufügen. Wählen Sie **Erweitert** aus.
1. Klicken Sie auf **Find Now** (Suche starten).

## <a name="create-and-manage-the-windows-service"></a>Wählen Sie das betreffende Benutzerkonto aus der Liste aus.

### <a name="create-a-service"></a>Klicken Sie auf **OK**.

Klicken Sie erneut auf **OK**, um den Benutzer zur Richtlinie hinzuzufügen. Klicken Sie auf **OK** oder auf **Anwenden**, um die Änderungen zu übernehmen.

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* Erstellen und Verwalten des Windows-Diensts Erstellen eines Diensts Verwenden Sie PowerShell-Befehle, um einen Dienst zu registrieren.
* Führen Sie über eine administrative PowerShell 6-Befehlsshell die folgenden Befehle aus:
* `{EXE PATH}`: entspricht dem Pfad zum Ordner der App auf dem Host (z. B. `d:\myservice`).
* Fügen Sie nicht die ausführbare Datei der App in den Pfad ein. Außerdem benötigen Sie keinen nachstehenden Schrägstrich.
* `{DOMAIN OR COMPUTER NAME\USER}`: entspricht dem Benutzerkonto für den Dienst (z. B. `Contoso\ServiceUser`).
* `{SERVICE NAME}`: entspricht dem Dienstnamen (z. B. `MyService`).

### <a name="start-a-service"></a>`{EXE FILE PATH}`: entspricht dem Pfad zur ausführbaren Datei der App (z. B. `d:\myservice\myservice.exe`).

Fügen Sie den Namen der ausführbaren Datei einschließlich ihrer Erweiterung hinzu.

```powershell
Start-Service -Name {SERVICE NAME}
```

`{DESCRIPTION}`: ist eine Beschreibung des Diensts (z. B. `My sample service`).

### <a name="determine-a-services-status"></a>`{DISPLAY NAME}`: entspricht dem Anzeigenamen des Diensts (z. B. `My Service`).

Starten eines Diensts

```powershell
Get-Service -Name {SERVICE NAME}
```

Sie können Dienste mithilfe des folgenden PowerShell 6-Befehls starten:

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a>Das Starten des Diensts dauert ein paar Sekunden.

Ermitteln des Status eines Diensts

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a>Sie können den Status von Diensten mithilfe des folgenden PowerShell 6-Befehls überprüfen:

Der Status wird als einer der folgenden Werte gemeldet:

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a>Beenden eines Diensts

Sie können Dienste mithilfe des folgenden PowerShell 6-Befehls beenden:

1. Entfernen eines Diensts

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. Nach einer kurzen Verzögerung durch das Beenden des Diensts können Sie diesen mithilfe des folgenden Powershell 6-Befehls entfernen:

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. Behandeln von Start- und Stopereignissen

   ```csharp
   host.RunAsCustomService();
   ```

   Gehen Sie wie folgt vor, um <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>-, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>- und <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>-Ereignisse zu bearbeiten:

## <a name="proxy-server-and-load-balancer-scenarios"></a>Erstellen Sie eine von <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> abgeleitete Klasse mit den `OnStarting`-, `OnStarted`- und `OnStopping`-Methoden:

Erstellen Sie eine Erweiterungsmethode für <xref:Microsoft.AspNetCore.Hosting.IWebHost>, die den `CustomWebHostService` an <xref:System.ServiceProcess.ServiceBase.Run*> übergibt: Rufen Sie in `Program.Main` anstelle von <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> die Erweiterungsmethode `RunAsCustomService` auf:

## <a name="configure-endpoints"></a>Wenn Sie Informationen dazu benötigen, wie Sie den Speicherort von <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main` abrufen können, sehen Sie sich das Codebeispiel im Abschnitt [Bereitstellungstyp](#deployment-type) an.

Proxyserver und Lastenausgleichsszenarien Dienste, die mit Anforderungen aus dem Internet oder einem Unternehmensnetzwerk interagieren und hinter einem Proxy oder Lastenausgleich ausgeführt werden, erfordern möglicherweise zusätzliche Konfigurationen.

Weitere Informationen finden Sie unter <xref:host-and-deploy/proxy-load-balancer>.

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

Konfigurieren von Endpunkten Standardmäßig ist ASP.NET Core an `http://localhost:5000` gebunden.

> [!NOTE]
> Konfigurieren Sie URL und Port, indem Sie die `ASPNETCORE_URLS`-Umgebungsvariable festlegen.

## <a name="current-directory-and-content-root"></a>Weitere Informationen zu den Konfigurationsansätzen für URLs und Ports finden Sie im relevanten Serverartikel:

Die vorangehende Anleitung behandelt die Unterstützung für HTTPS-Endpunkte. Konfigurieren Sie z. B. die APP für HTTPS, wenn die Authentifizierung mit einem Windows-Dienst verwendet wird. Die Verwendung des ASP.NET Core-HTTPS-Entwicklerzertifikats zum Schützen eines Dienstendpunkts wird nicht unterstützt.

### <a name="set-the-content-root-path-to-the-apps-folder"></a>Aktuelles Verzeichnis und Inhaltsstammverzeichnis

Das aktuelle Arbeitsverzeichnis, das durch Aufrufen von <xref:System.IO.Directory.GetCurrentDirectory*> für einen Windows-Dienst zurückgegeben wird, ist der Ordner *C:\\WINDOWS\\system32*. Der Ordner *system32* ist kein geeigneter Speicherort für die Dateien eines Diensts (z.B. Einstellungsdateien).

Verwenden Sie einen der folgenden Ansätze, um die Objekt- und Einstellungsdateien eines Dienstes beizubehalten und darauf zuzugreifen.

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a>Legen Sie den Inhaltsstammpfad auf den Ordner der App fest.

<xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> entspricht dem Pfad, der für das Argument `binPath` bereitgestellt wird, wenn ein Dienst erstellt wird.

## <a name="troubleshoot"></a>Anstatt `GetCurrentDirectory` aufzurufen, um Pfade zu Einstellungsdateien zu erstellen, rufen Sie <xref:System.IO.Directory.SetCurrentDirectory*> mit dem Pfad zum [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) der App auf.

Bestimmen Sie unter `Program.Main` den Pfad zum Ordner der ausführbaren Datei des Dienstes und verwenden Sie den Pfad, um das Inhaltsverzeichnis der App festzulegen:

### <a name="common-errors"></a>Speichern der Dateien eines Diensts an einem geeigneten Ort auf dem Datenträger

* Geben Sie mit <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> beim Verwenden von <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> einen absoluten Pfad zu dem Ordner an, der die Dateien enthält.
* Problembehandlung Informationen zur Problembehandlung für Windows-Dienstanwendungen finden Sie unter <xref:test/troubleshoot>. Häufige Fehler
  * Eine alte oder eine Vorabversion von PowerShell wird verwendet.
  * Der registrierte Dienst verwendet nicht die **veröffentlichte** Ausgabe des Befehls [dotnet publish](/dotnet/core/tools/dotnet-publish) der Anwendung.
* Die Ausgabe des Befehls [dotnet build](/dotnet/core/tools/dotnet-build) wird nicht für die Anwendungsbereitstellung unterstützt.
* Veröffentlichte Ressourcen sind je nach Bereitstellungstyp in einem der folgenden Ordner vorzufinden: *bin/Release/{ZIELFRAMEWORK}/publish* (FDD)
* *bin/Release/{ZIELFRAMEWORK}/{RUNTIME-ID}/publish* (SCD)
* Der Dienst weist nicht den Status „WIRD AUSGEFÜHRT“ auf.
* Die App enthält falsche Pfade zu Ressourcen (z. B. Zertifikate).
* Der Basispfad eines Windows-Diensts lautet: *C:\\Windows\\System32*.

### <a name="system-and-application-event-logs"></a>Der Benutzer verfügt nicht über die Berechtigung *Als Dienst anmelden*.

Das Kennwort des Benutzers ist abgelaufen oder wird nicht ordnungsgemäß übermittelt, wenn der PowerShell-Befehl `New-Service` ausgeführt wird.

1. Die App erfordert ASP.NET Core-Authentifizierung, aber sie wurde nicht für sichere (HTTPS-)Verbindungen konfiguriert.
1. Der Port für die Anforderungs-URL in der App ist fehlerhaft oder nicht ordnungsgemäß konfiguriert.
1. System- und Anwendungsereignisprotokolle So greifen Sie auf die System- und Anwendungsereignisprotokolle zu:
1. Öffnen Sie das Startmenü, suchen Sie nach der *Ereignisanzeige*, und wählen Sie dann die App **Ereignisanzeige** aus.

### <a name="run-the-app-at-a-command-prompt"></a>Öffnen Sie unter **Ereignisanzeige** den Knoten **Windows-Protokolle**.

Klicken Sie auf **System**, um das Systemereignisprotokoll zu öffnen. Wählen Sie **Anwendung** aus, um das Anwendungsereignisprotokoll zu öffnen. Suchen Sie nach Fehlern, die mit der fehlerhaften App im Zusammenhang stehen.

### <a name="clear-package-caches"></a>Ausführen der App in einer Eingabeaufforderung

Viele Startfehler erzeugen keine nützlichen Informationen in den Ereignisprotokollen. Sie können die Ursache für einige Fehler ermitteln, indem Sie die App in einer Eingabeaufforderung auf dem Hostsystem ausführen. Senken Sie den [Protokolliergrad](xref:fundamentals/logging/index#log-level), oder führen Sie die App in der [Entwicklungsumgebung](xref:fundamentals/environments) aus, um zusätzliche Informationen von der App zu protokollieren.

1. Bereinigen der Paketcaches
1. Eine funktionsfähige App kann direkt nach der Durchführung eines Upgrades für das .NET Core SDK auf dem Entwicklungscomputer oder einer Änderung der Paketversionen in der App fehlschlagen.

   In einigen Fällen können inkohärente Pakete eine App beschädigen, wenn größere Upgrades durchgeführt werden. Die meisten dieser Probleme können durch Befolgung der folgenden Anweisungen behoben werden:

1. Löschen Sie die Ordner *bin* und *obj*.
1. Bereinigen Sie die Paketcaches, indem Sie [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) über eine Befehlsshell ausführen.

### <a name="slow-or-hanging-app"></a>Sie können die Paketcaches auch mit dem Tool [nuget.exe](https://www.nuget.org/downloads) und dem Befehl `nuget locals all -clear` bereinigen.

*nuget.exe* ist wird unter dem Windows Desktop-Betriebssystem nicht gebündelt installiert und muss separat von der [NuGet-Website](https://www.nuget.org/downloads) abgerufen werden.

#### <a name="app-crashes-or-encounters-an-exception"></a>Stellen Sie das Projekt wieder her und erstellen Sie es neu.

Löschen Sie alle Dateien im Bereitstellungsordner auf dem Server, bevor Sie die App noch mal bereitstellen.

1. Langsame oder hängende App
1. Ein *Absturzabbild* ist eine Momentaufnahme des Systemarbeitsspeichers, die Ihnen dabei behilflich sein kann, die Ursache eines App-Absturzes, eines Startfehlers oder einer langsamen App zu ermitteln.

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. App stürzt ab, oder es tritt eine Ausnahme auf
1. Abrufen und Analysieren eines Speicherabbilds aus der [Windows-Fehlerberichterstattung (WER)](/windows/desktop/wer/windows-error-reporting):

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

Erstellen Sie einen Ordner zum Speichern von Absturzabbilddateien unter `c:\dumps`. Führen Sie das PowerShell-Skript [EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) mit dem Namen des ausführbaren Anwendung aus:

> [!WARNING]
> Führen Sie die App unter den Bedingungen aus, die dazu führen, dass der Absturz auftritt.

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>Nachdem der Absturz stattgefunden hat, führen Sie das [PowerShell-Skript „DisableDumps“](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1) aus:

Nachdem eine Anwendung abgestürzt ist und die Absturzabbildsammlung abgeschlossen ist, kann die App ordnungsgemäß beendet werden.

#### <a name="analyze-the-dump"></a>Das PowerShell-Skript konfiguriert WER für die Erfassung von bis zu fünf Absturzabbildern pro App.

Absturzabbilder können eine große Menge Speicherplatz in Anspruch nehmen (jeweils bis zu mehreren GB). App hat sich aufgehängt, es tritt ein Fehler während des Starts auf, oder sie wird normal ausgeführt

## <a name="additional-resources"></a>Wenn sich eine App *aufgehängt* hat (nicht mehr reagiert, aber nicht abstürzt), ein Fehler während des Starts auftritt bzw. die App normal ausgeführt wird, finden Sie weitere Informationen unter [Benutzermodus-Absturzabbilddateien: Auswählen des besten Tools](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool), um ein geeignetes Tool zum Generieren des Speicherabbilds auszuwählen.

* Analysieren des Speicherabbilds
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Ein Speicherabbild kann mithilfe mehrerer Ansätze analysiert werden. Weitere Informationen finden Sie unter [Analysieren einer Benutzermodus-Speicherabbilddatei](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).

Zusätzliche Ressourcen

## <a name="prerequisites"></a>[Kestrel-Endpunktkonfiguration](xref:fundamentals/servers/kestrel#endpoint-configuration) (einschließlich HTTPS-Konfiguration und Unterstützung für SNI)

* Eine ASP.NET Core-App kann unter Windows als [Windows-Dienst](/dotnet/framework/windows-services/introduction-to-windows-service-applications) ohne die Verwendung von IIS gehostet werden.
* Wenn die App als Windows-Dienst gehostet und der Server neu gestartet wird, startet diese automatisch.

## <a name="app-configuration"></a>[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Voraussetzungen

[ASP.NET Core SDK 2.1 oder höher](https://dotnet.microsoft.com/download) [PowerShell 6.2 oder höher](https://github.com/PowerShell/PowerShell) App-Konfiguration Die App benötigt Paketverweise für [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) und [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).

* Um bei der Ausführung außerhalb eines Dienstes zu testen und zu debuggen, fügen Sie Code hinzu, um festzustellen, ob die Anwendung als Dienst oder als Konsolenanwendung ausgeführt wird. Überprüfen Sie, ob der Debugger angefügt ist oder ob es einen `--console`-Parameter gibt. Wenn eine dieser Bedingungen erfüllt ist (die App wird nicht als Dienst ausgeführt), rufen Sie <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> auf. Wenn die Bedingungen nicht zutreffen (die App als Dienst ausgeführt wird):
* Rufen Sie <xref:System.IO.Directory.SetCurrentDirectory*> auf, und verwenden Sie einen Pfad zum veröffentlichten Speicherort der App.

Rufen Sie nicht <xref:System.IO.Directory.GetCurrentDirectory*> auf, um den Pfad abzurufen, da eine Windows-Dienst-App den Ordner *C:\\WINDOWS\\system32* zurückgibt, wenn <xref:System.IO.Directory.GetCurrentDirectory*> aufgerufen wird.

Weitere Informationen finden Sie im Abschnitt [Aktuelles Verzeichnis und Inhaltsstammverzeichnis](#current-directory-and-content-root). Dieser Schritt wird ausgeführt, bevor die App in `CreateWebHostBuilder` konfiguriert wird.

Rufen Sie <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> auf, um die App als Dienst auszuführen. Da der [Anbieter der Befehlszeilenkonfiguration](xref:fundamentals/configuration/index#command-line-configuration-provider) Name/Wert-Paare für Befehlszeilenargumente benötigt, wird der Parameter `--console` aus den Argumenten entfernt, bevor <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> diese empfängt.

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a>Um das Windows-Ereignisprotokoll zu schreiben, fügen Sie den EventLog-Anbieter zu <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*> hinzu.

Legen Sie den Protokollierungsgrad mit dem `Logging:LogLevel:Default`-Schlüssel in der *appsettings.Production.json*-Datei fest.

### <a name="sdk"></a>Im folgenden Beispiel aus der Beispiel-App wird `RunAsCustomService` anstelle von <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> aufgerufen, um Ereignisse in Bezug auf die Lebensdauer innerhalb der App zu verarbeiten.

Weitere Informationen finden Sie im Abschnitt [Behandeln von Start- und Stopereignissen](#handle-starting-and-stopping-events).

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Bereitstellungstyp

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a>Weitere Informationen und Tipps zu Bereitstellungsszenarien finden Sie unter [.NET Core-Anwendungsbereitstellung](/dotnet/core/deploying/).

SDK Geben Sie für einen Web-App-basierten Dienst, der die Razor Pages- oder MVC-Frameworks verwendet, das Web-SDK in der Projektdatei an:

Geben Sie das Worker-SDK in der Projektdatei an, wenn der Dienst nur Hintergrundaufgaben ausführt (z. B. [gehostete Dienste](xref:fundamentals/host/hosted-services)): Frameworkabhängige Bereitstellung (Framework-dependent deployment, FDD) Eine Framework-abhängige Bereitstellung (Framework-Dependent Deployment, FDD) benötigt eine gemeinsame systemweite Version von .NET Core auf dem Zielsystem. Wenn das FDD-Szenario gemäß der Anleitung in diesem Artikel übernommen wird, erzeugt das SDK eine ausführbare Datei ( *.exe*). Diese wird als *frameworkabhängige ausführbare Datei* bezeichnet.

Der [Runtimebezeichner](/dotnet/core/rid-catalog) (Runtime Identifier, RID) für Windows ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) enthält das Zielframework. Im folgenden Beispiel wird die RID auf `win7-x64` festgelegt.

Die `<SelfContained>`-Eigenschaft ist auf `false` festgelegt. Diese Eigenschaften geben dem SDK die Anweisung, eine ausführbare Datei ( *.exe*) für Windows sowie eine App zu generieren, die vom freigegebenen .NET Core-Framework abhängig ist.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a>Die `<UseAppHost>`-Eigenschaft ist auf `true` festgelegt.

Diese Eigenschaft stellt für den Dienst einen Aktivierungspfad (eine ausführbare Datei, *EXE*) für eine frameworkabhängige Bereitstellung bereit. Eine *web.config*-Datei, die normalerweise erstellt wird, wenn Sie eine ASP.NET Core-App veröffentlichen, ist für eine Windows Services-App nicht erforderlich.

Um die Erstellung der *web.config*-Datei zu deaktivieren, fügen Sie die auf `true` festgelegte `<IsTransformWebConfigDisabled>`-Eigenschaft hinzu.

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

Eigenständige Bereitstellung

* Eigenständige Bereitstellungen (Self-Contained Deployment, SCD) benötigen kein gemeinsames Framework auf dem Hostsystem.
* Die Runtime und die App-Abhängigkeiten werden mit der App bereitgestellt.

Ein [Runtimebezeichner](/dotnet/core/rid-catalog) (Runtime Identifier, RID) für Windows ist im `<PropertyGroup>`-Objekt enthalten, das wiederum das Zielframework enthält:

So führen Sie die Veröffentlichung für mehrere RIDs aus:

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a>Geben Sie die RIDs in einer durch Semikolons getrennten Liste an.

Verwenden Sie den Eigenschaftennamen [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (Plural).

Weitere Informationen finden Sie im [.NET Core RID-Katalog](/dotnet/core/rid-catalog).

```powershell
New-LocalUser -Name {SERVICE NAME}
```

Eine `<SelfContained>`-Eigenschaft wird auf `true` festgelegt:

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

Benutzerkonten für Dienste

Wenn Sie ein Benutzerkonto für einen Dienst erstellen möchten, verwenden Sie das Cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) in einer PowerShell 6-Befehlsshell.

Unter Windows 10 mit dem Update von Oktober 2018 (Version 1809/Build 10.0.17763) oder höher:

Unter Windows mit einer früheren Betriebssystemversion als Windows 10 mit dem Update von Oktober 2018 (Version 1809/Build 10.0.17763): Geben Sie bei Aufforderung ein [sicheres Kennwort](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) ein.

## <a name="log-on-as-a-service-rights"></a>Solange der Parameter `-AccountExpires` dem Cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) nicht mit einem Ablaufdatum <xref:System.DateTime> angegeben wird, läuft das Konto nicht ab.

Weitere Informationen finden Sie unter [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) und [Dienstbenutzerkonten](/windows/desktop/services/service-user-accounts).

1. Eine alternative Methode zum Verwalten von Benutzern bei Verwendung von Active Directory ist die Verwendung von verwalteten Dienstkonten.
1. Weitere Informationen finden Sie unter [Gruppenverwaltete Dienstkonten: Übersicht](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).
1. Rechte zum Anmelden als Dienst
1. Gehen Sie wie folgt vor, um Rechte zum *Anmelden als Dienst* für das Benutzerkonto eines Diensts einzurichten:
1. Öffnen Sie den Editor für lokale Sicherheitsrichtlinien, indem Sie die Datei *secpol.msc* ausführen.
   1. Erweitern Sie den Knoten **Lokale Richtlinien**, und klicken Sie auf **Zuweisen von Benutzerrechten**.
   1. Öffnen Sie die Richtlinie **Anmelden als Dienst**. Wählen Sie **Benutzer oder Gruppe hinzufügen** aus. Geben Sie den Objektnamen (das Benutzerkonto) an. Dafür gibt es zwei Möglichkeiten: Geben Sie die Bezeichnung des Benutzerkontos (`{DOMAIN OR COMPUTER NAME\USER}`) in das Feld für den Objektnamen ein, und klicken Sie auf **OK**, um den Benutzer der Richtlinie hinzuzufügen. Wählen Sie **Erweitert** aus.
1. Klicken Sie auf **Find Now** (Suche starten).

## <a name="create-and-manage-the-windows-service"></a>Wählen Sie das betreffende Benutzerkonto aus der Liste aus.

### <a name="create-a-service"></a>Klicken Sie auf **OK**.

Klicken Sie erneut auf **OK**, um den Benutzer zur Richtlinie hinzuzufügen. Klicken Sie auf **OK** oder auf **Anwenden**, um die Änderungen zu übernehmen.

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* Erstellen und Verwalten des Windows-Diensts Erstellen eines Diensts Verwenden Sie PowerShell-Befehle, um einen Dienst zu registrieren.
* Führen Sie über eine administrative PowerShell 6-Befehlsshell die folgenden Befehle aus:
* `{EXE PATH}`: entspricht dem Pfad zum Ordner der App auf dem Host (z. B. `d:\myservice`).
* Fügen Sie nicht die ausführbare Datei der App in den Pfad ein. Außerdem benötigen Sie keinen nachstehenden Schrägstrich.
* `{DOMAIN OR COMPUTER NAME\USER}`: entspricht dem Benutzerkonto für den Dienst (z. B. `Contoso\ServiceUser`).
* `{SERVICE NAME}`: entspricht dem Dienstnamen (z. B. `MyService`).

### <a name="start-a-service"></a>`{EXE FILE PATH}`: entspricht dem Pfad zur ausführbaren Datei der App (z. B. `d:\myservice\myservice.exe`).

Fügen Sie den Namen der ausführbaren Datei einschließlich ihrer Erweiterung hinzu.

```powershell
Start-Service -Name {SERVICE NAME}
```

`{DESCRIPTION}`: ist eine Beschreibung des Diensts (z. B. `My sample service`).

### <a name="determine-a-services-status"></a>`{DISPLAY NAME}`: entspricht dem Anzeigenamen des Diensts (z. B. `My Service`).

Starten eines Diensts

```powershell
Get-Service -Name {SERVICE NAME}
```

Sie können Dienste mithilfe des folgenden PowerShell 6-Befehls starten:

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a>Das Starten des Diensts dauert ein paar Sekunden.

Ermitteln des Status eines Diensts

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a>Sie können den Status von Diensten mithilfe des folgenden PowerShell 6-Befehls überprüfen:

Der Status wird als einer der folgenden Werte gemeldet:

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a>Beenden eines Diensts

Sie können Dienste mithilfe des folgenden PowerShell 6-Befehls beenden:

1. Entfernen eines Diensts

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. Nach einer kurzen Verzögerung durch das Beenden des Diensts können Sie diesen mithilfe des folgenden Powershell 6-Befehls entfernen:

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. Behandeln von Start- und Stopereignissen

   ```csharp
   host.RunAsCustomService();
   ```

   Gehen Sie wie folgt vor, um <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>-, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>- und <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>-Ereignisse zu bearbeiten:

## <a name="proxy-server-and-load-balancer-scenarios"></a>Erstellen Sie eine von <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> abgeleitete Klasse mit den `OnStarting`-, `OnStarted`- und `OnStopping`-Methoden:

Erstellen Sie eine Erweiterungsmethode für <xref:Microsoft.AspNetCore.Hosting.IWebHost>, die den `CustomWebHostService` an <xref:System.ServiceProcess.ServiceBase.Run*> übergibt: Rufen Sie in `Program.Main` anstelle von <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> die Erweiterungsmethode `RunAsCustomService` auf:

## <a name="configure-endpoints"></a>Wenn Sie Informationen dazu benötigen, wie Sie den Speicherort von <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main` abrufen können, sehen Sie sich das Codebeispiel im Abschnitt [Bereitstellungstyp](#deployment-type) an.

Proxyserver und Lastenausgleichsszenarien Dienste, die mit Anforderungen aus dem Internet oder einem Unternehmensnetzwerk interagieren und hinter einem Proxy oder Lastenausgleich ausgeführt werden, erfordern möglicherweise zusätzliche Konfigurationen.

Weitere Informationen finden Sie unter <xref:host-and-deploy/proxy-load-balancer>.

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

Konfigurieren von Endpunkten Standardmäßig ist ASP.NET Core an `http://localhost:5000` gebunden.

> [!NOTE]
> Konfigurieren Sie URL und Port, indem Sie die `ASPNETCORE_URLS`-Umgebungsvariable festlegen.

## <a name="current-directory-and-content-root"></a>Weitere Informationen zu den Konfigurationsansätzen für URLs und Ports finden Sie im relevanten Serverartikel:

Die vorangehende Anleitung behandelt die Unterstützung für HTTPS-Endpunkte. Konfigurieren Sie z. B. die APP für HTTPS, wenn die Authentifizierung mit einem Windows-Dienst verwendet wird. Die Verwendung des ASP.NET Core-HTTPS-Entwicklerzertifikats zum Schützen eines Dienstendpunkts wird nicht unterstützt.

### <a name="set-the-content-root-path-to-the-apps-folder"></a>Aktuelles Verzeichnis und Inhaltsstammverzeichnis

Das aktuelle Arbeitsverzeichnis, das durch Aufrufen von <xref:System.IO.Directory.GetCurrentDirectory*> für einen Windows-Dienst zurückgegeben wird, ist der Ordner *C:\\WINDOWS\\system32*. Der Ordner *system32* ist kein geeigneter Speicherort für die Dateien eines Diensts (z.B. Einstellungsdateien).

Verwenden Sie einen der folgenden Ansätze, um die Objekt- und Einstellungsdateien eines Dienstes beizubehalten und darauf zuzugreifen.

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a>Legen Sie den Inhaltsstammpfad auf den Ordner der App fest.

<xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> entspricht dem Pfad, der für das Argument `binPath` bereitgestellt wird, wenn ein Dienst erstellt wird.

## <a name="troubleshoot"></a>Anstatt `GetCurrentDirectory` aufzurufen, um Pfade zu Einstellungsdateien zu erstellen, rufen Sie <xref:System.IO.Directory.SetCurrentDirectory*> mit dem Pfad zum [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) der App auf.

Bestimmen Sie unter `Program.Main` den Pfad zum Ordner der ausführbaren Datei des Dienstes und verwenden Sie den Pfad, um das Inhaltsverzeichnis der App festzulegen:

### <a name="common-errors"></a>Speichern der Dateien eines Diensts an einem geeigneten Ort auf dem Datenträger

* Geben Sie mit <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> beim Verwenden von <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> einen absoluten Pfad zu dem Ordner an, der die Dateien enthält.
* Problembehandlung Informationen zur Problembehandlung für Windows-Dienstanwendungen finden Sie unter <xref:test/troubleshoot>. Häufige Fehler
  * Eine alte oder eine Vorabversion von PowerShell wird verwendet.
  * Der registrierte Dienst verwendet nicht die **veröffentlichte** Ausgabe des Befehls [dotnet publish](/dotnet/core/tools/dotnet-publish) der Anwendung.
* Die Ausgabe des Befehls [dotnet build](/dotnet/core/tools/dotnet-build) wird nicht für die Anwendungsbereitstellung unterstützt.
* Veröffentlichte Ressourcen sind je nach Bereitstellungstyp in einem der folgenden Ordner vorzufinden: *bin/Release/{ZIELFRAMEWORK}/publish* (FDD)
* *bin/Release/{ZIELFRAMEWORK}/{RUNTIME-ID}/publish* (SCD)
* Der Dienst weist nicht den Status „WIRD AUSGEFÜHRT“ auf.
* Die App enthält falsche Pfade zu Ressourcen (z. B. Zertifikate).
* Der Basispfad eines Windows-Diensts lautet: *C:\\Windows\\System32*.

### <a name="system-and-application-event-logs"></a>Der Benutzer verfügt nicht über die Berechtigung *Als Dienst anmelden*.

Das Kennwort des Benutzers ist abgelaufen oder wird nicht ordnungsgemäß übermittelt, wenn der PowerShell-Befehl `New-Service` ausgeführt wird.

1. Die App erfordert ASP.NET Core-Authentifizierung, aber sie wurde nicht für sichere (HTTPS-)Verbindungen konfiguriert.
1. Der Port für die Anforderungs-URL in der App ist fehlerhaft oder nicht ordnungsgemäß konfiguriert.
1. System- und Anwendungsereignisprotokolle So greifen Sie auf die System- und Anwendungsereignisprotokolle zu:
1. Öffnen Sie das Startmenü, suchen Sie nach der *Ereignisanzeige*, und wählen Sie dann die App **Ereignisanzeige** aus.

### <a name="run-the-app-at-a-command-prompt"></a>Öffnen Sie unter **Ereignisanzeige** den Knoten **Windows-Protokolle**.

Klicken Sie auf **System**, um das Systemereignisprotokoll zu öffnen. Wählen Sie **Anwendung** aus, um das Anwendungsereignisprotokoll zu öffnen. Suchen Sie nach Fehlern, die mit der fehlerhaften App im Zusammenhang stehen.

### <a name="clear-package-caches"></a>Ausführen der App in einer Eingabeaufforderung

Viele Startfehler erzeugen keine nützlichen Informationen in den Ereignisprotokollen. Sie können die Ursache für einige Fehler ermitteln, indem Sie die App in einer Eingabeaufforderung auf dem Hostsystem ausführen. Senken Sie den [Protokolliergrad](xref:fundamentals/logging/index#log-level), oder führen Sie die App in der [Entwicklungsumgebung](xref:fundamentals/environments) aus, um zusätzliche Informationen von der App zu protokollieren.

1. Bereinigen der Paketcaches
1. Eine funktionsfähige App kann direkt nach der Durchführung eines Upgrades für das .NET Core SDK auf dem Entwicklungscomputer oder einer Änderung der Paketversionen in der App fehlschlagen.

   In einigen Fällen können inkohärente Pakete eine App beschädigen, wenn größere Upgrades durchgeführt werden. Die meisten dieser Probleme können durch Befolgung der folgenden Anweisungen behoben werden:

1. Löschen Sie die Ordner *bin* und *obj*.
1. Bereinigen Sie die Paketcaches, indem Sie [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) über eine Befehlsshell ausführen.

### <a name="slow-or-hanging-app"></a>Sie können die Paketcaches auch mit dem Tool [nuget.exe](https://www.nuget.org/downloads) und dem Befehl `nuget locals all -clear` bereinigen.

*nuget.exe* ist wird unter dem Windows Desktop-Betriebssystem nicht gebündelt installiert und muss separat von der [NuGet-Website](https://www.nuget.org/downloads) abgerufen werden.

#### <a name="app-crashes-or-encounters-an-exception"></a>Stellen Sie das Projekt wieder her und erstellen Sie es neu.

Löschen Sie alle Dateien im Bereitstellungsordner auf dem Server, bevor Sie die App noch mal bereitstellen.

1. Langsame oder hängende App
1. Ein *Absturzabbild* ist eine Momentaufnahme des Systemarbeitsspeichers, die Ihnen dabei behilflich sein kann, die Ursache eines App-Absturzes, eines Startfehlers oder einer langsamen App zu ermitteln.

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. App stürzt ab, oder es tritt eine Ausnahme auf
1. Abrufen und Analysieren eines Speicherabbilds aus der [Windows-Fehlerberichterstattung (WER)](/windows/desktop/wer/windows-error-reporting):

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

Erstellen Sie einen Ordner zum Speichern von Absturzabbilddateien unter `c:\dumps`. Führen Sie das PowerShell-Skript [EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) mit dem Namen des ausführbaren Anwendung aus:

> [!WARNING]
> Führen Sie die App unter den Bedingungen aus, die dazu führen, dass der Absturz auftritt.

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>Nachdem der Absturz stattgefunden hat, führen Sie das [PowerShell-Skript „DisableDumps“](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1) aus:

Nachdem eine Anwendung abgestürzt ist und die Absturzabbildsammlung abgeschlossen ist, kann die App ordnungsgemäß beendet werden.

#### <a name="analyze-the-dump"></a>Das PowerShell-Skript konfiguriert WER für die Erfassung von bis zu fünf Absturzabbildern pro App.

Absturzabbilder können eine große Menge Speicherplatz in Anspruch nehmen (jeweils bis zu mehreren GB). App hat sich aufgehängt, es tritt ein Fehler während des Starts auf, oder sie wird normal ausgeführt

## <a name="additional-resources"></a>Wenn sich eine App *aufgehängt* hat (nicht mehr reagiert, aber nicht abstürzt), ein Fehler während des Starts auftritt bzw. die App normal ausgeführt wird, finden Sie weitere Informationen unter [Benutzermodus-Absturzabbilddateien: Auswählen des besten Tools](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool), um ein geeignetes Tool zum Generieren des Speicherabbilds auszuwählen.

* Analysieren des Speicherabbilds
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

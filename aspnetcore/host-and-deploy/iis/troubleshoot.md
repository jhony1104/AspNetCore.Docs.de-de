---
title: Problembehandlung bei ASP.NET Core in IIS
author: guardrex
description: Erfahren Sie, wie Sie Probleme mit IIS-Bereitstellungen (IIS = Internet Information Services) von ASP.NET Core-Apps diagnostizieren können.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/12/2019
uid: host-and-deploy/iis/troubleshoot
ms.openlocfilehash: e4c93459f2030c7c0a55ea90e0cc8c8d30b76c51
ms.sourcegitcommit: a04eb20e81243930ec829a9db5dd5de49f669450
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/03/2019
ms.locfileid: "66470459"
---
# <a name="troubleshoot-aspnet-core-on-iis"></a>Problembehandlung bei ASP.NET Core in IIS

Von [Luke Latham](https://github.com/guardrex)

Dieser Artikel enthält Anweisungen zum Diagnostizieren eines Startproblems der ASP.NET Core-App beim Hosten mithilfe von [Internet Information Services (IIS)](/iis). Die in diesem Artikel enthaltenen Informationen gelten für das Hosting in IIS unter Windows Server und Windows Desktop.

::: moniker range=">= aspnetcore-2.2"

In Visual Studio entspricht ein ASP.NET Core-Projekt standardmäßig dem [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)-Hosting während des Debuggens. Ein *502.5: Prozessfehler* oder ein *500.30: Startfehler*, der beim lokalen Debuggen auftritt, kann mithilfe der Hinweise in diesem Thema behoben werden.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

In Visual Studio entspricht ein ASP.NET Core-Projekt standardmäßig dem [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)-Hosting während des Debuggens. Ein *502.5: Prozessfehler*, der beim lokalen Debuggen auftritt, kann mithilfe der Hinweise in diesem Thema behoben werden.

::: moniker-end

Weitere Themen zur Problembehandlung:

<xref:host-and-deploy/azure-apps/troubleshoot> Obwohl App Service das [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) und IIS für das Hosten von Apps verwendet, finden Sie im entsprechenden Artikel weitere, für App Service spezifische Anweisungen.

<xref:fundamentals/error-handling> In diesem Artikel erfahren Sie, wie Sie Fehler in ASP.NET Core-Apps in der Entwicklungsphase auf einem lokalen System behandeln.

[Debuggen mit Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger) In diesem Artikel werden die Features des Visual Studio-Debuggers vorgestellt.

[Debuggen mit Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging) In diesem Artikel erfahren Sie, wie Sie mit dem in Visual Studio Code integrierten Debugger arbeiten.

## <a name="app-startup-errors"></a>App-Startfehler

### <a name="5025-process-failure"></a>502.5: Prozessfehler

Der Workerprozess schlägt fehl. Die App wird nicht gestartet.

Das ASP.NET Core-Modul kann den .NET-Back-End-Prozess nicht starten. Die Ursache für einen Fehler beim Starten eines Prozesses kann in der Regel über Einträge im [Anwendungsereignisprotokoll](#application-event-log) und im [stdout-Protokoll des ASP.NET Core-Moduls](#aspnet-core-module-stdout-log) ermittelt werden.

Eine allgemeine Fehlerbedingung ist, dass die App aufgrund einer Version des freigegebenen ASP.NET Core-Frameworks falsch konfiguriert ist, die nicht vorhanden ist. Überprüfen Sie, welche Versionen des freigegebenen ASP.NET Core-Frameworks auf dem Zielcomputer installiert sind. Das *freigegebene Framework* ist der Satz der Assemblys (*DLL*-Dateien), die auf dem Computer installiert werden und auf die ein Metapaket wie `Microsoft.AspNetCore.App` verweist. Der Metapaketverweis kann eine mindestens erforderliche Version angeben. Weitere Informationen finden Sie unter [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/) (Das freigegebene Framework).

Die Fehlerseite *502.5: Prozessfehler* wird zurückgegeben, wenn ein falsch konfiguriertes Hosting oder eine falsch konfigurierte App bewirkt, dass der Workerprozess fehlschlägt:

![Browserfenster mit der Seite „502.5: Prozessfehler“](troubleshoot/_static/process-failure-page.png)

::: moniker range="= aspnetcore-2.2"

### <a name="50030-in-process-startup-failure"></a>500.30: Prozessinterner Startupfehler

Der Workerprozess schlägt fehl. Die App wird nicht gestartet.

Das ASP.NET Core-Modul kann den .NET Core-CLR-In-Process nicht starten. Die Ursache für einen Fehler beim Starten eines Prozesses kann in der Regel über Einträge im [Anwendungsereignisprotokoll](#application-event-log) und im [stdout-Protokoll des ASP.NET Core-Moduls](#aspnet-core-module-stdout-log) ermittelt werden.

Eine allgemeine Fehlerbedingung ist, dass die App aufgrund einer Version des freigegebenen ASP.NET Core-Frameworks falsch konfiguriert ist, die nicht vorhanden ist. Überprüfen Sie, welche Versionen des freigegebenen ASP.NET Core-Frameworks auf dem Zielcomputer installiert sind.

### <a name="5000-in-process-handler-load-failure"></a>500.0: Fehler beim Laden des prozessinternen Handlers

Der Workerprozess schlägt fehl. Die App wird nicht gestartet.

Das ASP.NET Core-Modul kann die .NET Core-CLR und den In-Process-Anforderungshandler (*aspnetcorev2_inprocess.dll*) nicht finden. Überprüfen Sie Folgendes:

* Diese App legt entweder das NuGet-Paket [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) oder das Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) als Ziel fest.
* Die Version des freigegebenen ASP.NET Core-Frameworks, die von der App als Ziel festgelegt ist, ist auf dem Zielcomputer installiert.

### <a name="5000-out-of-process-handler-load-failure"></a>500.0: Fehler beim Laden des prozessexternen Handlers

Der Workerprozess schlägt fehl. Die App wird nicht gestartet.

Das ASP.NET Core-Modul kann den Out-of-Process-Hostinganforderungshandler nicht finden. Stellen Sie sicher, dass sich *aspnetcorev2_outofprocess.dll* in einem Unterordner mit *aspnetcorev2.dll* befindet.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="50031-ancm-failed-to-find-native-dependencies"></a>500.31: Fehler bei der Suche nach nativen Abhängigkeiten in ANCM

Der Workerprozess schlägt fehl. Die App wird nicht gestartet.

Das ASP.NET Core-Modul kann die prozessinterne .NET Core-Runtime nicht starten. Die häufigste Ursache dieses Startfehlers ist die fehlende Installation der Laufzeiten `Microsoft.NETCore.App` oder `Microsoft.AspNetCore.App`. Dieser Fehler tritt auf, wenn die App für die Zielversion 3.0 von ASP.NET Core bereitgestellt wurde, die aber auf dem Computer nicht vorhanden ist. Es wird beispielsweise folgende Fehlermeldung angezeigt:

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

In der Fehlermeldung werden alle installierten .NET Core-Versionen aufgelistet sowie die für die App erforderliche Version. Sie haben folgende Möglichkeiten, um diesen Fehler zu beheben:

* Installieren Sie die entsprechende .NET Core-Version auf Ihrem Computer.
* Ändern Sie die Zielversion der App in eine .NET Core-Version, die auf Ihrem Computer vorhanden ist.
* Veröffentlichen Sie die App als eine [eigenständige Bereitstellung](/dotnet/core/deploying/#self-contained-deployments-scd).

Beim Ausführen im Entwicklungsmodus (wenn die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` auf `Development` festgelegt ist) wird der jeweilige Fehler in die HTTP-Antwort geschrieben. Die Ursache für einen Fehler beim Starten eines Prozesses kann auch über das [Anwendungsereignisprotokoll](#application-event-log) ermittelt werden.

### <a name="50032-ancm-failed-to-load-dll"></a>500.32: Fehler beim Laden der DLL in ANCM

Der Workerprozess schlägt fehl. Die App wird nicht gestartet.

Die häufigste Ursache für diesen Fehler ist die Veröffentlichung der App für eine nicht kompatible Prozessorarchitektur. Der Fehler tritt auf, wenn der Arbeitsprozess als 32-Bit-App ausgeführt wird, die App aber für eine 64-Bit-Ausführung veröffentlicht wurde.

Sie haben folgende Möglichkeiten, um diesen Fehler zu beheben:

* Veröffentlichen Sie die App erneut, sodass Prozessorarchitektur und Arbeitsprozess übereinstimmen.
* Veröffentlichen Sie die App als eine [Framework-abhängige Bereitstellung](/dotnet/core/deploying/#framework-dependent-executables-fde).

### <a name="50033-ancm-request-handler-load-failure"></a>500.33: Fehler beim Laden des Anforderungshandlers in ANCM

Der Workerprozess schlägt fehl. Die App wird nicht gestartet.

Die App verweist nicht auf das Framework `Microsoft.AspNetCore.App`. Das ASP.NET Core-Modul hostet nur Apps mit `Microsoft.AspNetCore.App` als Zielframework.

Vergewissern Sie sich, dass das Zielframework der App `Microsoft.AspNetCore.App` ist, um den Fehler zu beheben. Überprüfen Sie `.runtimeconfig.json`, um das Zielframework der App anzuzeigen.

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a>500.34: Gemischte Hostingmodelle in ANCM nicht unterstützt

Im selben Arbeitsprozess können nicht gleichzeitig eine In-Process- und eine Out-of-Process-App ausgeführt werden.

Führen Sie die Apps in separaten IIS-Anwendungspools aus, um diesen Fehler zu beheben.

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a>500.35: Mehrere In-Process-Anwendungen im selben Prozess in ANCM

Im selben Arbeitsprozess können nicht gleichzeitig eine In-Process- und eine Out-of-Process-App ausgeführt werden.

Führen Sie die Apps in separaten IIS-Anwendungspools aus, um diesen Fehler zu beheben.

### <a name="50036-ancm-out-of-process-handler-load-failure"></a>500.36: Fehler beim Laden des Out-of-Process-Handlers in ANCM

Der Out-of-Process-Anforderungshandler *aspnetcorev2_outofprocess.dll* befindet sich nicht neben der Datei *aspnetcorev2.dll*. Dies deutet auf eine fehlerhafte Installation des ASP.NET Core-Moduls hin.

Reparieren Sie die Installation des [.NET Core-Hostingpakets](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (für IIS) oder von Visual Studio (für IIS Express), um diesen Fehler zu beheben.

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a>500.37: Fehler beim Starten von ANCM innerhalb des Zeitlimits für den Start

ANCM konnte nicht innerhalb des angegebenen Zeitlimits für den Start gestartet werden. Das Standardzeitlimit beträgt 120 Sekunden.

Dieser Fehler kann beim Starten einer großen Anzahl von Apps auf dem gleichen Computer auftreten. Überprüfen Sie, ob beim Starten CPU-/Speicherauslastungsspitzen auf dem Server vorhanden sind. In diesem Fall sollten Sie möglicherweise den Startvorgang einiger Apps staffeln.

### <a name="50030-in-process-startup-failure"></a>500.30: Prozessinterner Startupfehler

Der Workerprozess schlägt fehl. Die App wird nicht gestartet.

Das ASP.NET Core-Modul kann die prozessinterne .NET Core-Runtime nicht starten. Die Ursache für einen Fehler beim Starten eines Prozesses kann in der Regel über Einträge im [Anwendungsereignisprotokoll](#application-event-log) und im [stdout-Protokoll des ASP.NET Core-Moduls](#aspnet-core-module-stdout-log) ermittelt werden.

### <a name="5000-in-process-handler-load-failure"></a>500.0: Fehler beim Laden des prozessinternen Handlers

Der Workerprozess schlägt fehl. Die App wird nicht gestartet.

Beim Laden von Komponenten des ASP.NET Core-Moduls ist ein unbekannter Fehler aufgetreten. Führen Sie eine der folgenden Aktionen aus:

* Wenden Sie sich an den [Microsoft-Support](https://support.microsoft.com/oas/default.aspx?prid=15832) (über **Entwicklertools** > **ASP.NET Core**).
* Stellen Sie Ihre Frage auf Stack Overflow.
* Melden Sie das Problem im [GitHub-Repository](https://github.com/aspnet/AspNetCore).

::: moniker-end

### <a name="500-internal-server-error"></a>500: Interner Serverfehler

Die App wird gestartet, aber ein Fehler verhindert, dass der Server auf die Anforderung eingeht.

Dieser Fehler tritt im Code der App während des Starts oder bei der Erstellung einer Antwort auf. Die Antwort enthält möglicherweise keinen Inhalt oder die Antwort wird als *500: Interner Serverfehler* im Browser angezeigt. Das Anwendungsereignisprotokoll gibt normalerweise an, dass die Anwendung normal gestartet wurde. Aus Sicht des Servers ist dies richtig. Die App wurde gestartet, sie kann jedoch keine gültige Antwort generieren. [Führen Sie die App in einer Eingabeaufforderung](#run-the-app-at-a-command-prompt) auf dem Server aus oder [aktivieren Sie das stdout-Protokoll des ASP.NET Core-Moduls](#aspnet-core-module-stdout-log), um das Problem zu beheben.

### <a name="failed-to-start-application-errorcode-0x800700c1"></a>Fehler beim Starten der Anwendung (ErrorCode 0x800700c1)

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

Die App konnte nicht gestartet werden, weil die Assembly der App ( *.dll*) nicht geladen werden konnte.

Dieser Fehler tritt bei einem Bitanzahlkonflikt zwischen der veröffentlichten App und dem w3wp/iisexpress-Prozess auf.

Überprüfen Sie, ob die 32-Bit-Einstellung des Anwendungspools korrekt ist:

1. Wählen Sie im IIS-Manager unter **Anwendungspools** den Anwendungspool aus.
1. Wählen Sie im Bereich **Aktionen** unter **Anwendungspool bearbeiten** **Erweiterte Einstellungen** aus.
1. Wählen Sie **32-Bit-Anwendungen aktivieren** aus:
   * Wenn Sie eine 32-Bit-(x86)-App bereitstellen, legen Sie den Wert auf `True` fest.
   * Wenn Sie eine 64-Bit-(x64)-App bereitstellen, legen Sie den Wert auf `False` fest.

### <a name="connection-reset"></a>Verbindungszurücksetzung

Falls ein Fehler auftritt, nachdem die Header gesendet wurden, ist es zu spät für den Server, einen **500: Interner Serverfehler** zu senden, wenn ein Fehler auftritt. Dies ist häufig der Fall, wenn während der Serialisierung komplexer Objekte für eine Antwort ein Fehler auftritt. Diese Art von Fehler wird angezeigt, wenn ein *Verbindungszurücksetzungsfehler* auf dem Client auftritt. Mithilfe der [Anwendungsprotokollierung](xref:fundamentals/logging/index) können diese Fehlertypen behoben werden.

## <a name="default-startup-limits"></a>Standardstarteinschränkungen

Das ASP.NET Core-Modul wurde mit dem Standardwert 120 Sekunden für das *StartupTimeLimit* konfiguriert. Wenn der Standardwert beibehalten wird, dauert der Start einer App möglicherweise bis zu zwei Minuten. Erst danach kann das Modul einen Prozessfehler protokollieren. Weitere Informationen zum Konfigurieren des Moduls finden Sie unter [Attribute des aspNetCore-Elements](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

## <a name="troubleshoot-app-startup-errors"></a>Problembehandlung bei App-Startfehlern

### <a name="enable-the-aspnet-core-module-debug-log"></a>Aktivieren des Debugprotokolls des ASP.NET Core-Moduls

Fügen Sie der *web.config*-Datei der App die folgenden Handlereinstellungen hinzu, um Debugprotokolle des ASP.NET Core-Moduls zu aktivieren:

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

Überprüfen Sie, ob der für das Protokoll angegebene Pfad vorhanden ist, und ob die Identität des Anwendungspools Schreibberechtigungen für den Speicherort hat.

### <a name="application-event-log"></a>Anwendungsereignisprotokoll

Greifen Sie auf das Anwendungsereignisprotokoll zu:

1. Öffnen Sie das Menü „Start“, suchen Sie nach der **Ereignisanzeige**, und wählen Sie anschließend die App **Ereignisanzeige** aus.
1. Öffnen Sie unter **Ereignisanzeige** den Knoten **Windows-Protokolle**.
1. Wählen Sie **Anwendung** aus, um das Anwendungsereignisprotokoll zu öffnen.
1. Suchen Sie nach Fehlern, die mit der fehlerhaften App im Zusammenhang stehen. Fehler weisen in der Spalte *Quelle* den Wert *IIS AspNetCore-Modul* oder *IIS Express AspNetCore-Modul* auf.

### <a name="run-the-app-at-a-command-prompt"></a>Ausführen der App in einer Eingabeaufforderung

Viele Startfehler erzeugen keine nützlichen Informationen im Anwendungsereignisprotokoll. Sie können die Ursache für einige Fehler ermitteln, indem Sie die App in einer Eingabeaufforderung auf dem Hostsystem ausführen.

#### <a name="framework-dependent-deployment"></a>Framework-abhängige Bereitstellung

Wenn es sich bei der App um eine [Framework-abhängige Bereitstellung handelt](/dotnet/core/deploying/#framework-dependent-deployments-fdd):

1. Navigieren Sie in einer Eingabeaufforderung zum Bereitstellungsordner und führen Sie die App aus, indem Sie die Assembly der App mit *dotnet.exe* ausführen. Ersetzen Sie in folgendem Befehl den Namen der Assembly der App durch \<assembly_name>: `dotnet .\<assembly_name>.dll`.
1. Die Konsolenausgabe der App, die Fehler anzeigt, wird in das Konsolenfenster geschrieben.
1. Wenn der Fehler während einer Anforderung an die App auftritt, führen Sie eine Anforderung an den Host und Port aus, auf dem Kestrel empfangsbereit ist. Führen Sie unter Verwendung der Standardhosts und -ports eine Anforderung für `http://localhost:5000/` aus. Wenn die App normalerweise auf die Kestrel-Endpunktadresse reagiert, ist die Problemursache wahrscheinlich die Hostingkonfiguration (anstelle der App).

#### <a name="self-contained-deployment"></a>Eigenständige Bereitstellung

Wenn es sich bei der App um eine [eigenständige Bereitstellung](/dotnet/core/deploying/#self-contained-deployments-scd) handelt:

1. Navigieren Sie in einer Eingabeaufforderung zum Bereitstellungsordner und führen Sie die ausführbaren Dateien der App aus. Ersetzen Sie in folgendem Befehl den Namen der Assembly der App durch \<assembly_name>: `<assembly_name>.exe`.
1. Die Konsolenausgabe der App, die Fehler anzeigt, wird in das Konsolenfenster geschrieben.
1. Wenn der Fehler während einer Anforderung an die App auftritt, führen Sie eine Anforderung an den Host und Port aus, auf dem Kestrel empfangsbereit ist. Führen Sie unter Verwendung der Standardhosts und -ports eine Anforderung für `http://localhost:5000/` aus. Wenn die App normalerweise auf die Kestrel-Endpunktadresse reagiert, ist die Problemursache wahrscheinlich die Hostingkonfiguration (anstelle der App).

### <a name="aspnet-core-module-stdout-log"></a>stdout-Protokoll des ASP.NET Core-Moduls

So aktivieren Sie stdout-Protokolle und zeigen diese an:

1. Navigieren Sie zum Bereitstellungsordner der Website auf dem Hostsystem.
1. Erstellen Sie den Ordner *logs*, wenn dieser nicht vorhanden ist. Anweisungen zum Aktivieren von MSBuild für die automatische Erstellung des Ordners *logs* in der Bereitstellung finden Sie im Thema [Verzeichnisstruktur](xref:host-and-deploy/directory-structure).
1. Bearbeiten Sie die Datei *web.config*. Legen Sie **stdoutLogEnabled** auf `true` fest, und ändern Sie den Pfad von **stdoutLogFile** so, dass auf den Ordner *logs* verwiesen wird (Beispiel: `.\logs\stdout`). `stdout` im Pfad ist das Präfix des Protokolldateinamens. Ein Zeitstempel, eine Prozess-ID und eine Dateierweiterung werden bei der Protokollerstellung automatisch hinzugefügt. Mit `stdout` als Präfix des Dateinamens wird eine typische Protokolldatei als *stdout_20180205184032_5412.log* benannt.
1. Stellen Sie sicher, dass die Identität des Anwendungspools über Schreibberechtigungen für den Ordner *Protokolle* verfügt.
1. Speichern Sie die aktualisierte Datei *web.config*.
1. Führen Sie eine Anforderung an die App aus.
1. Navigieren Sie zu dem Ordner *logs*. Suchen und öffnen Sie das aktuelle stdout-Protokoll.
1. Untersuchen Sie das Protokoll auf Fehler.

> [!IMPORTANT]
> Deaktivieren Sie die stdout-Protokollierung, wenn die Problembehandlung abgeschlossen ist.

1. Bearbeiten Sie die Datei *web.config*.
1. Legen Sie **stdoutLogEnabled** auf `false` fest.
1. Speichern Sie die Datei.

> [!WARNING]
> Wenn das stdout-Protokoll nicht deaktiviert wird, können App- oder Serverfehler auftreten. Für die Protokollgröße oder die Anzahl von erstellten Protokolldateien ist kein Grenzwert festgelegt.
>
> Verwenden Sie für die routinemäßige Protokollierung in einer ASP.NET Core-App eine Protokollierungsbibliothek, die die Protokolldateigröße beschränkt und die Protokolle rotiert. Weitere Informationen finden Sie im Artikel zur [Protokollierung von Drittanbietern](xref:fundamentals/logging/index#third-party-logging-providers).

## <a name="enable-the-developer-exception-page"></a>Aktivieren der Seite mit Ausnahmen für Entwickler

Die `ASPNETCORE_ENVIRONMENT` [Umgebungsvariable kann zur Datei web.config hinzugefügt werden](xref:host-and-deploy/aspnet-core-module#setting-environment-variables), um die App in der Entwicklungsumgebung auszuführen. Solange die Umgebung nicht beim Starten der App von `UseEnvironment` im Host-Builder außer Kraft gesetzt wird, kann die [Seite mit Ausnahmen für Entwickler](xref:fundamentals/error-handling) durch Festlegen der Umgebungsvariable angezeigt werden, wenn die App ausgeführt wird.

::: moniker range=">= aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

Das Festlegen der Umgebungsvariablen für `ASPNETCORE_ENVIRONMENT` wird nur bei der Verwendung von Staging- und Testservern empfohlen, die nicht für das Internet verfügbar gemacht werden. Entfernen Sie nach der Fehlerbehebung die Umgebungsvariable aus der Datei *web.config*. Informationen zum Festlegen von Umgebungsvariablen in der Datei *web.config* finden Sie unter [Untergeordnetes environmentVariables-Element von aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).

## <a name="common-startup-errors"></a>Häufige Startfehler

Siehe <xref:host-and-deploy/azure-iis-errors-reference>. Die meisten der häufig auftretenden Probleme, die den Start von Apps verhindern, werden im Referenzartikel behandelt.

## <a name="obtain-data-from-an-app"></a>Abrufen von Daten aus einer App

Wenn eine App in der Lage sind, auf Anforderungen zu reagieren, erhalten Sie Anforderungen, Verbindungen und zusätzliche Daten von den Apps über die Inlinemiddleware des Terminals. Weitere Informationen und Beispielcode finden Sie unter <xref:test/troubleshoot#obtain-data-from-an-app>.

## <a name="create-a-dump"></a>Erstellen eines Speicherabbilds

Ein *Speicherabbild* ist eine Momentaufnahme des Systemarbeitsspeichers. Ein Speicherabbild kann hilfreich sein, um die Ursache eines App-Absturzes, eines Startfehlers oder einer langsamen App zu ermitteln.

### <a name="app-crashes-or-encounters-an-exception"></a>App stürzt ab, oder es tritt eine Ausnahme auf

Abrufen und Analysieren eines Speicherabbilds aus der [Windows-Fehlerberichterstattung (WER)](/windows/desktop/wer/windows-error-reporting):

1. Erstellen Sie einen Ordner zum Speichern von Absturzabbilddateien unter `c:\dumps`. Der App-Pool muss über Schreibzugriff auf den Ordner verfügen.
1. Führen Sie das [PowerShell-Skript „EnableDumps“](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/EnableDumps.ps1) aus:
   * Wenn die App das [In-Process-Hostingmodell](xref:fundamentals/servers/index#in-process-hosting-model) verwendet, führen Sie das Skript für *w3wp.exe* aus:

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * Wenn die App das [Out-of-Process-Hostingmodell](xref:fundamentals/servers/index#out-of-process-hosting-model) verwendet, führen Sie das Skript für *dotnet.exe* aus:

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. Führen Sie die App unter den Bedingungen aus, die dazu führen, dass der Absturz auftritt.
1. Nachdem der Absturz stattgefunden hat, führen Sie das [PowerShell-Skript „DisableDumps“](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/DisableDumps.ps1) aus:
   * Wenn die App das [In-Process-Hostingmodell](xref:fundamentals/servers/index#in-process-hosting-model) verwendet, führen Sie das Skript für *w3wp.exe* aus:

     ```console
     .\DisableDumps w3wp.exe
     ```

   * Wenn die App das [Out-of-Process-Hostingmodell](xref:fundamentals/servers/index#out-of-process-hosting-model) verwendet, führen Sie das Skript für *dotnet.exe* aus:

     ```console
     .\DisableDumps dotnet.exe
     ```

Nachdem eine Anwendung abgestürzt ist und die Absturzabbildsammlung abgeschlossen ist, kann die App ordnungsgemäß beendet werden. Das PowerShell-Skript konfiguriert WER für die Erfassung von bis zu fünf Absturzabbildern pro App.

> [!WARNING]
> Absturzabbilder können eine große Menge Speicherplatz in Anspruch nehmen (jeweils bis zu mehreren GB).

### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>App hat sich aufgehängt, es tritt ein Fehler während des Starts auf, oder sie wird normal ausgeführt

Wenn sich eine App *aufgehängt* hat (nicht mehr reagiert, aber nicht abstürzt), ein Fehler während des Starts auftritt bzw. die App normal ausgeführt wird, finden Sie weitere Informationen unter [Benutzermodus-Absturzabbilddateien: Auswählen des besten Tools](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool), um ein geeignetes Tool zum Generieren des Speicherabbilds auszuwählen.

### <a name="analyze-the-dump"></a>Analysieren des Speicherabbilds

Ein Speicherabbild kann mithilfe mehrerer Ansätze analysiert werden. Weitere Informationen finden Sie unter [Analysieren einer Benutzermodus-Speicherabbilddatei](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).

## <a name="remote-debugging"></a>Remotedebuggen

Siehe [Remotedebuggen von ASP.NET Core auf einem IIS-Remotecomputer in Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer) in der Dokumentation zu Visual Studio.

## <a name="application-insights"></a>Application Insights

[Application Insights](/azure/application-insights/) stellt Telemetriedaten von Apps bereit, die von IIS gehostet werden, einschließlich Features für die Fehlerprotokollierung und die Berichterstellung. Application Insights kann nur Fehler melden, die nach dem Start der App auftreten, wenn die Protokollierungsfeatures der App verfügbar werden. Weitere Informationen finden Sie unter [Application Insights for ASP.NET Core (Application Insights für ASP.NET Core)](/azure/application-insights/app-insights-asp-net-core).

## <a name="additional-advice"></a>Zusätzliche Hinweise

Manchmal schlägt eine funktionsfähige App direkt nach der Durchführung eines Upgrades des .NET Core SDK auf dem Entwicklungscomputer oder der Paketversionen in der App fehl. In einigen Fällen können inkohärente Pakete eine App beschädigen, wenn größere Upgrades durchgeführt werden. Die meisten dieser Probleme können durch Befolgung der folgenden Anweisungen behoben werden:

1. Löschen Sie die Ordner *bin* und *obj*.
1. Löschen Sie die Paketcaches unter *%UserProfile%\\.nuget\\packages* und *%LocalAppData%\\Nuget\\v3-cache*.
1. Stellen Sie das Projekt wieder her und erstellen Sie es neu.
1. Überprüfen Sie, ob die vorherige Bereitstellung auf dem Server vollständig gelöscht wurde, bevor Sie die App erneut bereitstellen.

> [!TIP]
> Sie können Paketcaches ganz einfach löschen, indem Sie `dotnet nuget locals all --clear` über eine Eingabeaufforderung ausführen.
>
> Darüber hinaus können Paketcaches mit dem Tool [nuget.exe](https://www.nuget.org/downloads) und durch Ausführung des Befehls `nuget locals all -clear` gelöscht werden. *nuget.exe* ist wird unter dem Windows Desktop-Betriebssystem nicht gebündelt installiert und muss separat von der [NuGet-Website](https://www.nuget.org/downloads) abgerufen werden.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:test/troubleshoot>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/azure-apps/troubleshoot>

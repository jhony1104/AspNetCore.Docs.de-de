---
title: Problembehandlung bei ASP.NET Core in IIS
author: guardrex
description: Erfahren Sie, wie Sie Probleme mit IIS-Bereitstellungen (IIS = Internet Information Services) von ASP.NET Core-Apps diagnostizieren können.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/19/2019
uid: host-and-deploy/iis/troubleshoot
ms.openlocfilehash: 4df370dd9b1a5a651bcf767b8b9ace4220bdc345
ms.sourcegitcommit: 9f11685382eb1f4dd0fb694dea797adacedf9e20
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/21/2019
ms.locfileid: "67313660"
---
# <a name="troubleshoot-aspnet-core-on-iis"></a>Problembehandlung bei ASP.NET Core in IIS

Von [Luke Latham](https://github.com/guardrex)

Dieser Artikel enthält Anweisungen zum Diagnostizieren eines Startproblems der ASP.NET Core-App beim Hosten mithilfe von [Internet Information Services (IIS)](/iis). Die in diesem Artikel enthaltenen Informationen gelten für das Hosting in IIS unter Windows Server und Windows Desktop.

Weitere Themen zur Problembehandlung:

* Azure App Service verwendet auch das [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) und IIS zum Hosten von Apps. Spezielle Empfehlungen zur Problembehandlung von Azure App Service finden Sie unter <xref:host-and-deploy/azure-apps/troubleshoot>.
* In <xref:fundamentals/error-handling> erläutert, wie Sie während der Entwicklungsphase auf einem lokalen System Fehler in ASP.NET Core-Apps behandeln.
* [Debuggen mit Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger): In diesem Artikel werden die Features des Visual Studio-Debuggers vorgestellt.
* [Debuggen mit Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging): In diesem Artikel wird beschrieben, wie Sie mit dem in Visual Studio Code integrierten Debugger arbeiten.

[!INCLUDE[](~/includes/azure-iis-startup-errors.md)]

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
   * Wenn die App das [In-Process-Hostingmodell](xref:host-and-deploy/iis/index#in-process-hosting-model) verwendet, führen Sie das Skript für *w3wp.exe* aus:

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * Wenn die App das [Out-of-Process-Hostingmodell](xref:host-and-deploy/iis/index#out-of-process-hosting-model) verwendet, führen Sie das Skript für *dotnet.exe* aus:

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. Führen Sie die App unter den Bedingungen aus, die dazu führen, dass der Absturz auftritt.
1. Nachdem der Absturz stattgefunden hat, führen Sie das [PowerShell-Skript „DisableDumps“](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/DisableDumps.ps1) aus:
   * Wenn die App das [In-Process-Hostingmodell](xref:host-and-deploy/iis/index#in-process-hosting-model) verwendet, führen Sie das Skript für *w3wp.exe* aus:

     ```console
     .\DisableDumps w3wp.exe
     ```

   * Wenn die App das [Out-of-Process-Hostingmodell](xref:host-and-deploy/iis/index#out-of-process-hosting-model) verwendet, führen Sie das Skript für *dotnet.exe* aus:

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

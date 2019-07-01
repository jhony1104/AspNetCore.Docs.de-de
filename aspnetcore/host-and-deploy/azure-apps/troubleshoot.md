---
title: Problembehandlung bei ASP.NET Core in Azure App Service
author: guardrex
description: Erfahren Sie, wie Sie Probleme mit ASP.NET Core Azure App Service-Bereitstellungen diagnostizieren können.
ms.author: riande
ms.custom: mvc
ms.date: 06/19/2019
uid: host-and-deploy/azure-apps/troubleshoot
ms.openlocfilehash: d78499c1a82a011239f6b62b546f304a5d5017e2
ms.sourcegitcommit: 9f11685382eb1f4dd0fb694dea797adacedf9e20
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/21/2019
ms.locfileid: "67313756"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service"></a>Problembehandlung bei ASP.NET Core in Azure App Service

Von [Luke Latham](https://github.com/guardrex)

[!INCLUDE [Azure App Service Preview Notice](../../includes/azure-apps-preview-notice.md)]

Dieser Artikel enthält Anweisungen für die Diagnose eines Startproblems einer ASP.NET Core-App unter Verwendung von Azure App Service-Diagnosetools. Weitere Hinweise zur Problembehandlung finden Sie unter [Übersicht: Azure App Service-Diagnose](/azure/app-service/app-service-diagnostics) und [Vorgehensweise: Überwachen von Apps in Azure App Service](/azure/app-service/web-sites-monitor) in der Azure-Dokumentation.

Weitere Themen zur Problembehandlung:

* IIS verwendet auch das [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) zum Hosten von Apps. Spezielle Empfehlungen zur Problembehandlung IIS finden Sie unter <xref:host-and-deploy/iis/troubleshoot>.
* In <xref:fundamentals/error-handling> erläutert, wie Sie während der Entwicklungsphase auf einem lokalen System Fehler in ASP.NET Core-Apps behandeln.
* [Debuggen mit Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger): In diesem Artikel werden die Features des Visual Studio-Debuggers vorgestellt.
* [Debuggen mit Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging): In diesem Artikel wird beschrieben, wie Sie mit dem in Visual Studio Code integrierten Debugger arbeiten.

[!INCLUDE[](~/includes/azure-iis-startup-errors.md)]

## <a name="troubleshoot-app-startup-errors"></a>Problembehandlung bei App-Startfehlern

### <a name="application-event-log"></a>Anwendungsereignisprotokoll

Verwenden Sie das Blatt **Diagnose und Problembehandlung** im Azure-Portal, um auf das Anwendungsereignisprotokoll zuzugreifen:

1. Öffnen Sie die App im Azure-Portal unter **App Services**.
1. Klicken Sie auf **Diagnose und Problembehandlung**.
1. Wählen Sie die Überschrift **Diagnosetools** aus.
1. Klicken Sie unter **Supporttools** auf die Schaltfläche **Anwendungsereignisse**.
1. Überprüfen Sie den letzten vom Eintrag *IIS AspNetCoreModule* oder *IIS AspNetCoreModule V2* in der Spalte **Quelle** angegebenen Fehler.

Anstatt das Blatt **Diagnose und Problembehandlung** zu verwenden, können Sie die Anwendungsereignisprotokoll-Datei auch direkt mit [Kudu](https://github.com/projectkudu/kudu/wiki) untersuchen:

1. Öffnen Sie **Erweiterte Tools** im Bereich **Entwicklungstools**. Klicken Sie auf **Los&rarr;** . Die Kudu-Konsole wird in einer neuen Browserregisterkarte oder in einem neuen Fenster geöffnet.
1. Öffnen Sie mithilfe der Navigationsleiste am oberen Rand der Seite die **Debugging-Konsole**, und wählen Sie **CMD** aus.
1. Öffnen Sie den Ordner **LogFiles**.
1. Wählen Sie den Bleistift neben der Datei *eventlog.xml* aus.
1. Untersuchen Sie das Protokoll. Scrollen Sie zum Ende des Protokolls, um die aktuellsten Ereignisse anzuzeigen.

### <a name="run-the-app-in-the-kudu-console"></a>Führen Sie die App in der Kudu-Konsole aus

Viele Startfehler erzeugen keine nützlichen Informationen im Anwendungsereignisprotokoll. Sie können die App in der [Kudu](https://github.com/projectkudu/kudu/wiki)-Remote-Ausführungskonsole ausführen, um den Fehler zu ermitteln:

1. Öffnen Sie **Erweiterte Tools** im Bereich **Entwicklungstools**. Klicken Sie auf **Los&rarr;** . Die Kudu-Konsole wird in einer neuen Browserregisterkarte oder in einem neuen Fenster geöffnet.
1. Öffnen Sie mithilfe der Navigationsleiste am oberen Rand der Seite die **Debugging-Konsole**, und wählen Sie **CMD** aus.

#### <a name="test-a-32-bit-x86-app"></a>Testen einer 32-Bit-App (x86)

##### <a name="current-release"></a>Aktuelles Release

1. `cd d:\home\site\wwwroot`
1. Führen Sie die App aus:
   * Wenn es sich bei der App um eine [Framework-abhängige Bereitstellung handelt](/dotnet/core/deploying/#framework-dependent-deployments-fdd):

     ```console
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * Wenn es sich bei der App um eine [eigenständige Bereitstellung](/dotnet/core/deploying/#self-contained-deployments-scd) handelt:

     ```console
     {ASSEMBLY NAME}.exe
     ```

Die Konsolenausgabe der App, die Fehler anzeigt, wird an die Kudu-Konsole weitergeleitet.

##### <a name="framework-dependent-deployment-running-on-a-preview-release"></a>Frameworkabhängige Bereitstellung, die in einem Vorschaurelease ausgeführt wird

*Erfordert die Installation der Runtimeerweiterung für ASP.NET Core {VERSION} (x86).*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` ist die Runtimeversion.)
1. Führen Sie die App aus: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

Die Konsolenausgabe der App, die Fehler anzeigt, wird an die Kudu-Konsole weitergeleitet.

#### <a name="test-a-64-bit-x64-app"></a>Testen einer 64-Bit-App (x64)

##### <a name="current-release"></a>Aktuelles Release

* Wenn es sich bei der App um eine [frameworkabhängige Bereitstellung](/dotnet/core/deploying/#framework-dependent-deployments-fdd) (64-Bit, x64) handelt:
  1. `cd D:\Program Files\dotnet`
  1. Führen Sie die App aus: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`
* Wenn es sich bei der App um eine [eigenständige Bereitstellung](/dotnet/core/deploying/#self-contained-deployments-scd) handelt:
  1. `cd D:\home\site\wwwroot`
  1. Führen Sie die App aus: `{ASSEMBLY NAME}.exe`

Die Konsolenausgabe der App, die Fehler anzeigt, wird an die Kudu-Konsole weitergeleitet.

##### <a name="framework-dependent-deployment-running-on-a-preview-release"></a>Frameworkabhängige Bereitstellung, die in einem Vorschaurelease ausgeführt wird

*Erfordert die Installation der Runtimeerweiterung für ASP.NET Core {VERSION} (x64).*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` ist die Runtimeversion.)
1. Führen Sie die App aus: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

Die Konsolenausgabe der App, die Fehler anzeigt, wird an die Kudu-Konsole weitergeleitet.

### <a name="aspnet-core-module-stdout-log"></a>stdout-Protokoll des ASP.NET Core-Moduls

Das stdout-Protokoll des ASP.NET Core-Moduls zeichnet häufig nützliche Fehlermeldungen auf, die nicht im Anwendungsereignisprotokoll enthalten sind. So aktivieren Sie stdout-Protokolle und zeigen diese an:

1. Navigieren Sie zum Blatt **Diagnose und Problembehandlung** im Azure-Portal.
1. Wählen Sie unter **SELECT PROBLEM CATEGORY** (PROBLEMKATEGORIE WÄHLEN) die Schaltfläche **Web App Down** (Web-App ausgefallen) aus.
1. Klicken Sie unter **Suggested Solutions** (Vorgeschlagene Lösungen)  > **Enable Stdout Log Redirection** (stdout-Protokollumleitung aktivieren) auf **Open Kudu Console to edit Web.Config** (Kudu-Konsole öffnen, um web.config zu bearbeiten).
1. Öffnen Sie in der **Diagnosekonsole** die Ordner unter dem Pfad **site** > **wwwroot**. Scrollen Sie nach unten, um die Datei *web.config* am Ende der Liste einzublenden.
1. Klicken Sie auf den Bleistift neben der Datei *web.config*.
1. Setzen Sie **stdoutLogEnabled** auf `true`, und ändern Sie den Pfad **stdoutLogFile** in: `\\?\%home%\LogFiles\stdout`.
1. Wählen Sie **Speichern** aus, um die aktualisierte Datei *web.config* zu speichern.
1. Führen Sie eine Anforderung an die App aus.
1. Kehren Sie zum Azure-Portal zurück. Wählen Sie das Blatt **Erweiterte Tools** im Bereich **ENTWICKLUNGSTOOLS** aus. Klicken Sie auf **Los&rarr;** . Die Kudu-Konsole wird in einer neuen Browserregisterkarte oder in einem neuen Fenster geöffnet.
1. Öffnen Sie mithilfe der Navigationsleiste am oberen Rand der Seite die **Debugging-Konsole**, und wählen Sie **CMD** aus.
1. Wählen Sie den Ordner **LogFiles** aus.
1. Überprüfen Sie die Spalte **Geändert**, und wählen Sie den Bleistift aus, um das stdout-Protokoll mit dem letzten Änderungsdatum zu bearbeiten.
1. Wenn die Protokolldatei geöffnet wird, wird der Fehler angezeigt.

Deaktivieren Sie die stdout-Protokollierung, wenn die Problembehandlung abgeschlossen ist:

1. Kehren Sie in der Kudu-**Diagnosekonsole** zum Pfad **site** > **wwwroot** zurück, um die Datei *web.config* einzublenden. Öffnen Sie die Datei **web.config** erneut, indem Sie den Bleistift auswählen.
1. Legen Sie **stdoutLogEnabled** auf `false` fest.
1. Wählen Sie **Speichern** aus, um die Datei zu speichern.

> [!WARNING]
> Wenn das stdout-Protokoll nicht deaktiviert wird, können App- oder Serverfehler auftreten. Für die Protokollgröße oder die Anzahl von erstellten Protokolldateien ist kein Grenzwert festgelegt. Verwenden Sie die stdout-Protokollierung nur für die Behandlung von App-Startproblemen.
>
> Verwenden Sie für die allgemeine Protokollierung in einer ASP.NET Core-App nach dem Start eine Protokollierungsbibliothek, die die Protokolldateigröße beschränkt und Protokolle rotiert. Weitere Informationen finden Sie im Artikel zur [Protokollierung von Drittanbietern](xref:fundamentals/logging/index#third-party-logging-providers).

::: moniker range=">= aspnetcore-2.2"

### <a name="aspnet-core-module-debug-log"></a>Debugprotokoll des ASP.NET Core-Moduls

Das Debugprotokoll des ASP.NET Core-Moduls ermöglicht die zusätzliche und ausführlichere Protokollierung über das ASP.NET Core-Modul. So aktivieren Sie stdout-Protokolle und zeigen diese an:

1. Führen Sie einen der folgenden Schritte aus, um das erweiterte Diagnoseprotokoll zu aktivieren:
   * Befolgen Sie den Anweisungen in den [erweiterten Diagnoseprotokollen](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs), um die App für die erweiterte Diagnoseprotokollierung zu konfigurieren. Stellen Sie die App erneut bereit.
   * Fügen Sie die in unter [Erweiterte Diagnoseprotokollen](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) enthaltene `<handlerSettings>`-Klasse für die *web.config*-Datei der Live-App über die Kudu-Konsole hinzu:
     1. Öffnen Sie **Erweiterte Tools** im Bereich **Entwicklungstools**. Klicken Sie auf **Los&rarr;** . Die Kudu-Konsole wird in einer neuen Browserregisterkarte oder in einem neuen Fenster geöffnet.
     1. Öffnen Sie mithilfe der Navigationsleiste am oberen Rand der Seite die **Debugging-Konsole**, und wählen Sie **CMD** aus.
     1. Öffnen Sie die Ordner unter dem Pfad **site** > **wwwroot**. Bearbeiten Sie die Datei *web.config*, indem Sie auf die Bleistiftschaltfläche klicken. Fügen Sie den Abschnitt `<handlerSettings>` wie in den [erweiterten Diagnoseprotokollen](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) dargestellt hinzu. Klicken Sie auf die Schaltfläche **Speichern**.
1. Öffnen Sie **Erweiterte Tools** im Bereich **Entwicklungstools**. Klicken Sie auf **Los&rarr;** . Die Kudu-Konsole wird in einer neuen Browserregisterkarte oder in einem neuen Fenster geöffnet.
1. Öffnen Sie mithilfe der Navigationsleiste am oberen Rand der Seite die **Debugging-Konsole**, und wählen Sie **CMD** aus.
1. Öffnen Sie die Ordner unter dem Pfad **site** > **wwwroot**. Wenn Sie keinen Pfad für die *aspnetcore-debug.log*-Datei angegeben haben, wird die Datei in der Liste aufgeführt. Wenn Sie jedoch einen Pfad angegeben haben, navigieren Sie zum Speicherort der Protokolldatei.
1. Öffnen Sie die Protokolldatei über die Bleistiftschaltfläche neben dem Dateinamen.

Deaktivieren Sie die Debugprotokollierung, wenn die Problembehandlung abgeschlossen ist.

1. Führen Sie einen der folgenden Schritte aus, um das erweiterte Debugprotokoll zu deaktivieren:
   * Entfernen Sie `<handlerSettings>` aus der lokalen *web.config*-Datei, und stellen Sie die App erneut bereit.
   * Verwenden Sie die Kudu-Konsole, um die *web.config*-Datei zu bearbeiten und den Abschnitt `<handlerSettings>` zu entfernen. Speichern Sie die Datei.

> [!WARNING]
> Wenn das Debugprotokoll nicht deaktiviert werden kann, können App- oder Serverfehler auftreten. Es gibt keine Begrenzung für die Größe der Protokolldatei. Nutzen Sie die Debugprotokollierung nur für die Behandlung von App-Startproblemen.
>
> Verwenden Sie für die allgemeine Protokollierung in einer ASP.NET Core-App nach dem Start eine Protokollierungsbibliothek, die die Protokolldateigröße beschränkt und Protokolle rotiert. Weitere Informationen finden Sie im Artikel zur [Protokollierung von Drittanbietern](xref:fundamentals/logging/index#third-party-logging-providers).

::: moniker-end

## <a name="common-startup-errors"></a>Häufige Startfehler

Siehe <xref:host-and-deploy/azure-iis-errors-reference>. Die meisten der häufig auftretenden Probleme, die den Start von Apps verhindern, werden im Referenzartikel behandelt.

## <a name="slow-or-hanging-app"></a>Langsame oder hängende App

Wenn eine App bei einer Anforderung langsam reagiert oder hängt, lesen Sie folgende Artikel:

* [Problembehandlung von Leistungsproblemen in Azure App Service](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App (Verwenden der Absturzdiagnose-Websiteerweiterung zum Erfassen des Speicherabbilds bei vorübergehenden Ausnahmeproblemen oder Leistungsproblemen der Azure-Web-App)](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

## <a name="remote-debugging"></a>Remotedebuggen

Informationen hierzu finden Sie in den folgenden Themen:

* [Remote debugging web apps (Remotedebugging von Web-Apps) unter Troubleshoot a web app in Azure App Service using Visual Studio (Problembehandlung in einer Web-App in Azure App Service mithilfe von Visual Studio)](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) (Azure-Dokumentation)
* [Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017 (Remotedebugging von ASP.NET Core in IIS in Azure in Visual Studio 2017)](/visualstudio/debugger/remote-debugging-azure) (Visual Studio-Dokumentation)

## <a name="application-insights"></a>Application Insights

[Application Insights](https://azure.microsoft.com/services/application-insights/) stellt Telemetriedaten von Apps bereit, die im Azure App Service gehostet werden, einschließlich Fehlerprotokollierungs- und Reportingfeatures. Application Insights kann nur Fehler melden, die nach dem Start der App auftreten, wenn die Protokollierungsfeatures der App verfügbar werden. Weitere Informationen finden Sie unter [Application Insights for ASP.NET Core (Application Insights für ASP.NET Core)](/azure/application-insights/app-insights-asp-net-core).

## <a name="monitoring-blades"></a>Überwachungsblätter

Überwachungsblätter bieten eine Alternative zur Problembehandlung mit den weiter oben in diesem Thema beschriebenen Methoden. Die Blätter können zur Diagnose von Serie 500-Fehlern verwendet werden.

Vergewissern Sie sich, dass ASP.NET Core-Erweiterungen installiert sind. Wenn die Erweiterungen nicht installiert sind, installieren Sie diese manuell:

1. Wählen Sie im Blattabschnitt **ENTWICKLUNGSTOOLS** das Blatt **Erweiterungen** aus.
1. Die **ASP.NET Core-Erweiterungen** werden in der Liste angezeigt.
1. Wenn die Erweiterungen nicht installiert sind, klicken Sie auf **Hinzufügen**.
1. Wählen Sie die **ASP.NET Core-Erweiterungen** aus der Liste.
1. Klicken Sie auf **OK**, um die rechtlichen Bedingungen zu akzeptieren.
1. Klicken Sie auf **OK** auf dem Blatt **Erweiterung hinzufügen**.
1. Mit einer Popup-Informationsmeldung wird die erfolgreiche Installation der Erweiterungen angezeigt.

Wenn die stdout-Protokollierung nicht aktiviert ist, gehen Sie folgendermaßen vor:

1. Wählen Sie im Azure-Portal das Blatt **Erweiterte Tools** im Bereich **ENTWICKLUNGSTOOLS** aus. Klicken Sie auf **Los&rarr;** . Die Kudu-Konsole wird in einer neuen Browserregisterkarte oder in einem neuen Fenster geöffnet.
1. Öffnen Sie mithilfe der Navigationsleiste am oberen Rand der Seite die **Debugging-Konsole**, und wählen Sie **CMD** aus.
1. Öffnen Sie die Ordner unter dem Pfad **site** > **wwwroot**, und scrollen Sie nach unten, um die Datei *web.config* am Ende der Liste einzublenden.
1. Klicken Sie auf den Bleistift neben der Datei *web.config*.
1. Setzen Sie **stdoutLogEnabled** auf `true`, und ändern Sie den Pfad **stdoutLogFile** in: `\\?\%home%\LogFiles\stdout`.
1. Wählen Sie **Speichern** aus, um die aktualisierte Datei *web.config* zu speichern.

Fahren Sie mit der Aktivierung der Diagnoseprotokollierung fort:

1. Wählen Sie im Azure-Portal das Blatt **Diagnoseprotokolle** aus.
1. Wählen Sie den Parameter **On** für **Anwendungsprotokollierung (Dateisystem)** und **Detaillierte Fehlermeldungen** aus. Klicken Sie auf **Speichern** am oberen Rand des Blatts.
1. Um die Ablaufverfolgung für Anforderungsfehler, auch bekannt als Protokollierung der Anforderungsfehler-Ereignispufferung (FREB), einzuschließen, wählen Sie den Parameter **On** für **Ablaufverfolgung für Anforderungsfehler** aus.
1. Wählen Sie das Blatt **Protokollstream** aus, das direkt unter dem Blatt **Diagnoseprotokolle** im Portal angezeigt wird.
1. Führen Sie eine Anforderung an die App aus.
1. In den Protokollstreamdaten wird die Ursache des Fehlers angegeben.

Achten Sie darauf, die stdout-Protokollierung zu deaktivieren, wenn die Problembehandlung abgeschlossen ist. Weitere Informationen finden Sie im Abschnitt [stdout-Protokoll der ASP.NET Core-Moduls](#aspnet-core-module-stdout-log).

So zeigen Sie die Ablaufverfolgungsprotokolle für Anforderungsfehler (FREB-Protokolle) an:

1. Navigieren Sie zum Blatt **Diagnose und Problembehandlung** im Azure-Portal.
1. Wählen Sie **Failed Request Tracing Logs** (Ablaufverfolgungsprotokolle für Anforderungsfehler) im Bereich **SUPPORTTOOLS** der Seitenleiste aus.

Weitere Informationen finden Sie im [Abschnitt „Failed request traces“ im Thema „Enable diagnostics logging for apps in Azure App Service“](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) und unter [Häufig gestellte Fragen zur Anwendungsleistung von Web-Apps in Azure: Wie schalte ich die Ablaufverfolgung für Anforderungsfehler ein?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)

[Aktivieren der Diagnoseprotokollierung für Apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).

> [!WARNING]
> Wenn das stdout-Protokoll nicht deaktiviert wird, können App- oder Serverfehler auftreten. Für die Protokollgröße oder die Anzahl von erstellten Protokolldateien ist kein Grenzwert festgelegt.
>
> Verwenden Sie für die routinemäßige Protokollierung in einer ASP.NET Core-App eine Protokollierungsbibliothek, die die Protokolldateigröße beschränkt und die Protokolle rotiert. Weitere Informationen finden Sie im Artikel zur [Protokollierung von Drittanbietern](xref:fundamentals/logging/index#third-party-logging-providers).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [Problembehandlung in einer Web-App in Azure App Service mithilfe von Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Problembehandlung bei HTTP-Fehler „502 Ungültiges Gateway“ und „503 Dienst nicht verfügbar“ in Ihren Azure-Web-Apps](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [Problembehandlung von Leistungsproblemen in Azure App Service](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [FAQ zur Anwendungsleistung von Web-Apps in Azure](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [Einschränkungen der App Service-Laufzeiteinschränkung](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (Azure Friday: Azure App Service-Diagnose und -Fehlerbehebung; zwölfminütiges Video)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

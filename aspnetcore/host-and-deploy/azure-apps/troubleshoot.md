---
title: Problembehandlung bei ASP.NET Core in Azure App Service
author: guardrex
description: Erfahren Sie, wie Sie Probleme mit ASP.NET Core Azure App Service-Bereitstellungen diagnostizieren können.
ms.author: riande
ms.custom: mvc
ms.date: 01/11/2019
uid: host-and-deploy/azure-apps/troubleshoot
ms.openlocfilehash: 65a5e355bc15db6de9060331395c441160c8b62d
ms.sourcegitcommit: 42a8164b8aba21f322ffefacb92301bdfb4d3c2d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2019
ms.locfileid: "54341640"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service"></a>Problembehandlung bei ASP.NET Core in Azure App Service

Von [Luke Latham](https://github.com/guardrex)

[!INCLUDE [Azure App Service Preview Notice](../../includes/azure-apps-preview-notice.md)]

Dieser Artikel enthält Anweisungen für die Diagnose eines Startproblems einer ASP.NET Core-App unter Verwendung von Azure App Service-Diagnosetools. Weitere Hinweise zur Problembehandlung finden Sie unter [Übersicht: Azure App Service-Diagnose](/azure/app-service/app-service-diagnostics) und [Vorgehensweise: Überwachen von Apps in Azure App Service](/azure/app-service/web-sites-monitor) in der Azure-Dokumentation.

## <a name="app-startup-errors"></a>App-Startfehler

**502.5: Prozessfehler**  
Der Workerprozess schlägt fehl. Die App wird nicht gestartet.

Das [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) kann den Workerprozess nicht starten. Eine Untersuchung des Ereignisprotokolls hilft häufig, diese Art von Problem zu beheben. Der Zugriff auf das Protokoll wird im Abschnitt [Anwendungsereignisprotokoll](#application-event-log) erläutert.

Die Fehlerseite *502.5: Prozessfehler* wird zurückgegeben, wenn eine falsch konfigurierte App bewirkt, dass der Workerprozess fehlschlägt:

![Browserfenster mit der Seite „502.5: Prozessfehler“](troubleshoot/_static/process-failure-page.png)

**500: Interner Serverfehler**  
Die App wird gestartet, aber ein Fehler verhindert, dass der Server auf die Anforderung eingeht.

Dieser Fehler tritt im Code der App während des Starts oder bei der Erstellung einer Antwort auf. Die Antwort enthält möglicherweise keinen Inhalt oder die Antwort wird als *500: Interner Serverfehler* im Browser angezeigt. Das Anwendungsereignisprotokoll gibt normalerweise an, dass die Anwendung normal gestartet wurde. Aus Sicht des Servers ist dies richtig. Die App wurde gestartet, aber sie kann keine gültige Antwort generieren. [Führen Sie die App in der Kudu-Konsole ](#run-the-app-in-the-kudu-console) aus oder [aktivieren Sie das stdout-Protokoll des ASP.NET Core-Moduls](#aspnet-core-module-stdout-log), um das Problem zu beheben.

**Verbindungszurücksetzung**

Falls ein Fehler auftritt, nachdem die Header gesendet wurden, ist es zu spät für den Server, einen **500: Interner Serverfehler** zu senden, wenn ein Fehler auftritt. Dies ist häufig der Fall, wenn während der Serialisierung komplexer Objekte für eine Antwort ein Fehler auftritt. Diese Art von Fehler wird angezeigt, wenn ein *Verbindungszurücksetzungsfehler* auf dem Client auftritt. Mithilfe der [Anwendungsprotokollierung](xref:fundamentals/logging/index) können diese Fehlertypen behoben werden.

## <a name="default-startup-limits"></a>Standardstarteinschränkungen

Das ASP.NET Core-Modul wurde mit dem Standardwert 120 Sekunden für das *StartupTimeLimit* konfiguriert. Wenn der Standardwert beibehalten wird, dauert der Start einer App möglicherweise bis zu zwei Minuten. Erst danach kann das Modul einen Prozessfehler protokollieren. Weitere Informationen zum Konfigurieren des Moduls finden Sie unter [Attribute des aspNetCore-Elements](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

## <a name="troubleshoot-app-startup-errors"></a>Problembehandlung bei App-Startfehlern

### <a name="application-event-log"></a>Anwendungsereignisprotokoll

Verwenden Sie das Blatt **Diagnose und Problembehandlung** im Azure-Portal, um auf das Anwendungsereignisprotokoll zuzugreifen:

1. Öffnen Sie die App im Azure-Portal unter **App Services**.
1. Klicken Sie auf **Diagnose und Problembehandlung**.
1. Wählen Sie die Überschrift **Diagnosetools** aus.
1. Klicken Sie unter **Supporttools** auf die Schaltfläche **Anwendungsereignisse**.
1. Überprüfen Sie den letzten vom Eintrag *IIS AspNetCoreModule* oder *IIS AspNetCoreModule V2* in der Spalte **Quelle** angegebenen Fehler.

Anstatt das Blatt **Diagnose und Problembehandlung** zu verwenden, können Sie die Anwendungsereignisprotokoll-Datei auch direkt mit [Kudu](https://github.com/projectkudu/kudu/wiki) untersuchen:

1. Öffnen Sie **Erweiterte Tools** im Bereich **Entwicklungstools**. Klicken Sie auf **Los&rarr;**. Die Kudu-Konsole wird in einer neuen Browserregisterkarte oder in einem neuen Fenster geöffnet.
1. Öffnen Sie mithilfe der Navigationsleiste am oberen Rand der Seite die **Debugging-Konsole**, und wählen Sie **CMD** aus.
1. Öffnen Sie den Ordner **LogFiles**.
1. Wählen Sie den Bleistift neben der Datei *eventlog.xml* aus.
1. Untersuchen Sie das Protokoll. Scrollen Sie zum Ende des Protokolls, um die aktuellsten Ereignisse anzuzeigen.

### <a name="run-the-app-in-the-kudu-console"></a>Führen Sie die App in der Kudu-Konsole aus

Viele Startfehler erzeugen keine nützlichen Informationen im Anwendungsereignisprotokoll. Sie können die App in der [Kudu](https://github.com/projectkudu/kudu/wiki)-Remote-Ausführungskonsole ausführen, um den Fehler zu ermitteln:

1. Öffnen Sie **Erweiterte Tools** im Bereich **Entwicklungstools**. Klicken Sie auf **Los&rarr;**. Die Kudu-Konsole wird in einer neuen Browserregisterkarte oder in einem neuen Fenster geöffnet.
1. Öffnen Sie mithilfe der Navigationsleiste am oberen Rand der Seite die **Debugging-Konsole**, und wählen Sie **CMD** aus.
1. Öffnen Sie die Ordner unter dem Pfad **site** > **wwwroot**.
1. Führen Sie die App in der Konsole aus, indem Sie die Assembly der App ausführen.
   * Wenn die App eine [Framework-abhängige Bereitstellung](/dotnet/core/deploying/#framework-dependent-deployments-fdd) ist, führen Sie die Assembly der App mit *dotnet.exe* aus. Ersetzen Sie im folgenden Befehl den Namen der Assembly der App durch `<assembly_name>`: `dotnet .\<assembly_name>.dll`
   * Wenn die App eine [eigenständige Bereitstellung](/dotnet/core/deploying/#self-contained-deployments-scd) ist, führen Sie die ausführbare Datei der App aus. Ersetzen Sie im folgenden Befehl den Namen der Assembly der App durch `<assembly_name>`: `<assembly_name>.exe`
1. Die Konsolenausgabe der App, die Fehler anzeigt, wird an die Kudu-Konsole weitergeleitet.

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
1. Kehren Sie zum Azure-Portal zurück. Wählen Sie das Blatt **Erweiterte Tools** im Bereich **ENTWICKLUNGSTOOLS** aus. Klicken Sie auf **Los&rarr;**. Die Kudu-Konsole wird in einer neuen Browserregisterkarte oder in einem neuen Fenster geöffnet.
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
     1. Öffnen Sie **Erweiterte Tools** im Bereich **Entwicklungstools**. Klicken Sie auf **Los&rarr;**. Die Kudu-Konsole wird in einer neuen Browserregisterkarte oder in einem neuen Fenster geöffnet.
     1. Öffnen Sie mithilfe der Navigationsleiste am oberen Rand der Seite die **Debugging-Konsole**, und wählen Sie **CMD** aus.
     1. Öffnen Sie die Ordner unter dem Pfad **site** > **wwwroot**. Bearbeiten Sie die Datei *web.config*, indem Sie auf die Bleistiftschaltfläche klicken. Fügen Sie den Abschnitt `<handlerSettings>` wie in den [erweiterten Diagnoseprotokollen](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) dargestellt hinzu. Klicken Sie auf die Schaltfläche **Speichern**.
1. Öffnen Sie **Erweiterte Tools** im Bereich **Entwicklungstools**. Klicken Sie auf **Los&rarr;**. Die Kudu-Konsole wird in einer neuen Browserregisterkarte oder in einem neuen Fenster geöffnet.
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

Wenn eine App langsam reagiert oder hängt, finden Sie unter [Troubleshoot slow web app performance issues in Azure App Service (Problembehandlung von Leistungsproblemen in Azure App Service)](/azure/app-service/app-service-web-troubleshoot-performance-degradation) Debugging-Anweisungen.

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

1. Wählen Sie im Azure-Portal das Blatt **Erweiterte Tools** im Bereich **ENTWICKLUNGSTOOLS** aus. Klicken Sie auf **Los&rarr;**. Die Kudu-Konsole wird in einer neuen Browserregisterkarte oder in einem neuen Fenster geöffnet.
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

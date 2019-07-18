---
title: 'Überwachen Sie und Debuggen Sie: DevOps mit ASP.NET Core und Azure'
author: CamSoper
description: Überwachen und Debuggen Ihren Code als Teil einer DevOps-Lösung mit ASP.NET Core und Azure
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 07/10/2019
uid: azure/devops/monitor
ms.openlocfilehash: 1d8ed99f4387dbc99929164c558cc2ce14bd9ea0
ms.sourcegitcommit: b40613c603d6f0cc71f3232c16df61550907f550
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68307960"
---
# <a name="monitor-and-debug"></a>Überwachen und Debuggen

Die app bereitgestellt haben und erstellt eine DevOps-Pipeline, es ist wichtig zu verstehen, wie Sie Überwachung und Problembehandlung für die app.

In diesem Abschnitt müssen Sie die folgenden Aufgaben ausführen:

* Finden Sie grundlegende Überwachung und Problembehandlung für Daten im Azure-portal
* Hier erfahren Sie, wie Azure Monitor einen tieferen Einblick in die Metriken für alle Azure-Dienste
* Verbinden Sie die Web-app mit Application Insights für die profilerstellung der app
* Aktivieren Sie der Protokollierung, und erfahren Sie, wo Sie Protokolle herunterladen
* Stream-Protokolle in Echtzeit
* Erfahren Sie, wo Sie Warnungen einrichten
* Informationen Sie zu remote Debuggen Azure App Service-Web-apps.

## <a name="basic-monitoring-and-troubleshooting"></a>Grundlegende Überwachung und Problembehandlung

App Service-Web-apps sind einfach in Echtzeit überwacht. Das Azure-Portal rendert Metriken in leicht verständlichen und Diagrammen.

1. Öffnen der [Azure-Portal](https://portal.azure.com), und navigieren Sie zu der *Mywebapp\<Unique_number\>*  App Service.

1. Die **Übersicht** Registerkarte zeigt nützliche "auf einen Blick"-Informationen, darunter Diagramme, die zuletzt verwendete Metrik anzeigen.

    ![Screenshot mit Übersicht über panel](./media/monitoring/overview.png)

    * **Http 5xx**: Anzahl serverseitiger Fehler, normalerweise Ausnahmen in ASP.net Core Code.
    * **Daten in**: Eingehende Daten in Ihre Web-App.
    * Ausgeh folgende **Daten**: Datenausgang von Ihrer Web-App an Clients.
    * **Anforderungen**: Anzahl der HTTP-Anforderungen.
    * **Durchschnittliche Antwortzeit**: Die durchschnittliche Zeit, die die Web-App auf HTTP-Anforderungen antwortet.

    Auf dieser Seite befinden sich auch mehrere Self-service-Tools für die Problembehandlung und Optimierung.

    ![Screenshot mit Self-service-tools](./media/monitoring/wizards.png)

    * **Diagnose und Problembehandlung** ist ein Self-service-Ratgebers.
    * **Application Insights** ist für die profilerstellung der Leistung und Verhalten der app und wird weiter unten in diesem Abschnitt erläutert.
    * **App Service-Ratgebers** enthält Empfehlungen zum optimieren Ihre app-Erfahrung.

## <a name="advanced-monitoring"></a>Erweiterte Überwachung

[Azure Monitor](/azure/monitoring-and-diagnostics/) ist der zentrale für alle überwachungsmetriken und Festlegen von Warnungen für Azure-Dienste. Administratoren können in Azure Monitor können präzise Leistung nachzuverfolgen und Trends zu erkennen. Jeder Azure-Dienst bietet eine eigene [Satz von Metriken](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) an Azure Monitor.

## <a name="profile-with-application-insights"></a>Profil mit Application Insights

[Application Insights](/azure/application-insights/app-insights-overview) ist ein Azure-Dienst zum Analysieren der Leistung und Stabilität von Web-apps und wie Benutzer sie verwenden. Die Daten aus Application Insights ist besseres, als die von Azure Monitor. Die Daten können Entwickler und Administratoren mit wichtigen Informationen zur Verbesserung der apps bereitstellen. Application Insights können mit einer Azure App Service-Ressource ohne Änderungen am Code hinzugefügt werden.

1. Öffnen der [Azure-Portal](https://portal.azure.com), und navigieren Sie zu der *Mywebapp\<Unique_number\>*  App Service.
1. Von der **Übersicht** Registerkarte, klicken Sie auf die **Application Insights** Kachel.

    ![Application Insights-Kachel](./media/monitoring/app-insights.png)

1. Wählen Sie die **erstellen Sie neue Ressource** Optionsfeld. Verwenden Sie den Standardnamen für die Ressource, und wählen Sie den Speicherort für die Application Insights-Ressource. Der Speicherort muss nicht mit der Ihre Web-app übereinstimmen.

    ![Application Insights-setup](./media/monitoring/new-app-insights.png)

1. Für **-Runtime-Framework**Option **ASP.NET Core**. Übernehmen Sie die Standardeinstellungen.
1. Klicken Sie auf **OK**. Wenn Sie dazu aufgefordert werden, um zu bestätigen, wählen Sie **Weiter**.
1. Nachdem die Ressource erstellt wurde, klicken Sie auf den Namen des Application Insights-Ressource direkt zu der Application Insights-Seite navigieren.

    ![Neue Application Insights-Ressource ist bereit](./media/monitoring/new-app-insights-done.png)

Da die app verwendet wird, sammelt Daten. Wählen Sie **aktualisieren** , die auf dem Blatt mit neuen Daten neu zu laden.

![Application Insights-Registerkarte "Übersicht"](./media/monitoring/app-insights-overview.png)

Application Insights bietet nützliche Informationen ohne zusätzliche Konfiguration. Um den größtmöglichen aus Application Insights nutzen [Instrumentieren Ihrer app mit Application Insights SDK](/azure/application-insights/app-insights-asp-net-core). Wenn ordnungsgemäß konfiguriert ist, stellt der Dienst mit dem End-to-End-Überwachung auf den Webserver und Browser, einschließlich der clientseitigen Leistung. Weitere Informationen finden Sie unter den [Application Insights-Dokumentation](/azure/application-insights/app-insights-overview).

## <a name="logging"></a>Protokollierung

Web-Server und app-Protokolle werden standardmäßig in Azure App Service deaktiviert. Aktivieren Sie die Protokolle mit den folgenden Schritten:

1. Öffnen der [Azure-Portal](https://portal.azure.com), und navigieren Sie zu der *Mywebapp\<Unique_number\>*  App Service.
1. Klicken Sie im Menü auf der linken Seite einen Bildlauf nach unten, um die **Überwachung** Abschnitt. Wählen Sie **Diagnoseprotokolle**.

    ![Diagnoseprotokolle link](./media/monitoring/logging.png)

1. Aktivieren Sie **Anwendungsprotokollierung (Dateisystem)** . Wenn Sie dazu aufgefordert werden, klicken Sie auf das Kontrollkästchen, um die Erweiterungen zum Aktivieren der app, die Protokollierung in der Web-app zu installieren.
1. Legen Sie **webserverprotokollierung** zu **Dateisystem**.
1. Geben Sie die **Beibehaltungsdauer** in Tagen. Beispiel: 30.
1. Klicken Sie auf **Speichern**.

ASP.NET Core und Web Server (App Service)-Protokolle werden für die Web-app generiert. Sie können heruntergeladen werden, mithilfe der FTP-/FTPS-Informationen angezeigt. Das Kennwort ist identisch mit den Anmeldeinformationen für die Bereitstellung erstellt, die weiter oben in diesem Handbuch. Die Protokolle möglich [Streaming direkt auf Ihren lokalen Computer mithilfe von PowerShell oder Azure-Befehlszeilenschnittstelle](/azure/app-service/web-sites-enable-diagnostic-log#download). Protokolle können auch sein, [in Application Insights angezeigt](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).

## <a name="log-streaming"></a>Protokollstreaming

App und die Web-Server-Protokolle können in Echtzeit über das Portal gestreamt werden.

1. Öffnen der [Azure-Portal](https://portal.azure.com), und navigieren Sie zu der *Mywebapp\<Unique_number\>*  App Service.
1. Klicken Sie im Menü auf der linken Seite einen Bildlauf nach unten, um die **Überwachung** aus, und wählen Sie **Protokollstream**.

    ![Screenshot mit Log Stream link](./media/monitoring/log-stream.png)

Protokolle können auch sein, [gestreamt, die über Azure-Befehlszeilenschnittstelle oder Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), einschließlich über Cloud Shell.

## <a name="alerts"></a>Benachrichtigungen

Azure Monitor bietet Ihnen auch [Benachrichtigungen in Echtzeit](/azure/monitoring-and-diagnostics/insights-alerts-portal) basierend auf Metriken, administrative Ereignisse und anderen Kriterien.

> *Hinweis: Zurzeit ist eine Warnung zu Web-App-Metriken nur im Dienst "Warnungen (klassisch)" verfügbar.*

Die [Warnungen (klassisch) Dienst](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) finden Sie in Azure Monitor oder unter dem **Überwachung** Teil der App Service-Einstellungen.

![Link "projektwarnungen (klassisch)"](./media/monitoring/alerts.png)

## <a name="live-debugging"></a>Live-Debuggen

Azure App Service möglich [Remote mit Visual Studio debuggt](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) Wenn Protokolle nicht genügend Informationen bereitstellt. Remotedebuggen erfordert jedoch die app mit Debugsymbolen kompiliert werden. Debuggen sollte nicht in der Produktion, außer als letzten Ausweg erfolgen.

## <a name="conclusion"></a>Schlussbemerkung

In diesem Abschnitt haben Sie die folgenden Aufgaben aus:

* Finden Sie grundlegende Überwachung und Problembehandlung für Daten im Azure-portal
* Hier erfahren Sie, wie Azure Monitor einen tieferen Einblick in die Metriken für alle Azure-Dienste
* Verbinden Sie die Web-app mit Application Insights für die profilerstellung der app
* Aktivieren Sie der Protokollierung, und erfahren Sie, wo Sie Protokolle herunterladen
* Stream-Protokolle in Echtzeit
* Erfahren Sie, wo Sie Warnungen einrichten
* Informationen Sie zu remote Debuggen Azure App Service-Web-apps.

## <a name="additional-reading"></a>Weiterführende Literatur

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [Überwachen der Leistung von Azure-Web-app mit Application Insights](/azure/application-insights/app-insights-azure-web-apps)
* [Aktivieren der Diagnoseprotokollierung für Apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log)
* [Problembehandlung in einer Web-App in Azure App Service mithilfe von Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Erstellen von klassischen metrikwarnungen in Azure Monitor für Azure-Dienste – Azure-Portal](/azure/monitoring-and-diagnostics/insights-alerts-portal)

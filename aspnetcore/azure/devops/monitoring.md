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
# <a name="monitor-and-debug"></a><span data-ttu-id="b0e7b-103">Überwachen und Debuggen</span><span class="sxs-lookup"><span data-stu-id="b0e7b-103">Monitor and debug</span></span>

<span data-ttu-id="b0e7b-104">Die app bereitgestellt haben und erstellt eine DevOps-Pipeline, es ist wichtig zu verstehen, wie Sie Überwachung und Problembehandlung für die app.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-104">Having deployed the app and built a DevOps pipeline, it's important to understand how to monitor and troubleshoot the app.</span></span>

<span data-ttu-id="b0e7b-105">In diesem Abschnitt müssen Sie die folgenden Aufgaben ausführen:</span><span class="sxs-lookup"><span data-stu-id="b0e7b-105">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="b0e7b-106">Finden Sie grundlegende Überwachung und Problembehandlung für Daten im Azure-portal</span><span class="sxs-lookup"><span data-stu-id="b0e7b-106">Find basic monitoring and troubleshooting data in the Azure portal</span></span>
* <span data-ttu-id="b0e7b-107">Hier erfahren Sie, wie Azure Monitor einen tieferen Einblick in die Metriken für alle Azure-Dienste</span><span class="sxs-lookup"><span data-stu-id="b0e7b-107">Learn how Azure Monitor provides a deeper look at metrics across all Azure services</span></span>
* <span data-ttu-id="b0e7b-108">Verbinden Sie die Web-app mit Application Insights für die profilerstellung der app</span><span class="sxs-lookup"><span data-stu-id="b0e7b-108">Connect the web app with Application Insights for app profiling</span></span>
* <span data-ttu-id="b0e7b-109">Aktivieren Sie der Protokollierung, und erfahren Sie, wo Sie Protokolle herunterladen</span><span class="sxs-lookup"><span data-stu-id="b0e7b-109">Turn on logging and learn where to download logs</span></span>
* <span data-ttu-id="b0e7b-110">Stream-Protokolle in Echtzeit</span><span class="sxs-lookup"><span data-stu-id="b0e7b-110">Stream logs in real time</span></span>
* <span data-ttu-id="b0e7b-111">Erfahren Sie, wo Sie Warnungen einrichten</span><span class="sxs-lookup"><span data-stu-id="b0e7b-111">Learn where to set up alerts</span></span>
* <span data-ttu-id="b0e7b-112">Informationen Sie zu remote Debuggen Azure App Service-Web-apps.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-112">Learn about remote debugging Azure App Service web apps.</span></span>

## <a name="basic-monitoring-and-troubleshooting"></a><span data-ttu-id="b0e7b-113">Grundlegende Überwachung und Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="b0e7b-113">Basic monitoring and troubleshooting</span></span>

<span data-ttu-id="b0e7b-114">App Service-Web-apps sind einfach in Echtzeit überwacht.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-114">App Service web apps are easily monitored in real time.</span></span> <span data-ttu-id="b0e7b-115">Das Azure-Portal rendert Metriken in leicht verständlichen und Diagrammen.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-115">The Azure portal renders metrics in easy-to-understand charts and graphs.</span></span>

1. <span data-ttu-id="b0e7b-116">Öffnen der [Azure-Portal](https://portal.azure.com), und navigieren Sie zu der *Mywebapp\<Unique_number\>*  App Service.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-116">Open the [Azure portal](https://portal.azure.com), and then navigate to the *mywebapp\<unique_number\>* App Service.</span></span>

1. <span data-ttu-id="b0e7b-117">Die **Übersicht** Registerkarte zeigt nützliche "auf einen Blick"-Informationen, darunter Diagramme, die zuletzt verwendete Metrik anzeigen.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-117">The **Overview** tab displays useful "at-a-glance" information, including graphs displaying recent metrics.</span></span>

    ![Screenshot mit Übersicht über panel](./media/monitoring/overview.png)

    * <span data-ttu-id="b0e7b-119">**Http 5xx**: Anzahl serverseitiger Fehler, normalerweise Ausnahmen in ASP.net Core Code.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-119">**Http 5xx**: Count of server-side errors, usually exceptions in ASP.NET Core code.</span></span>
    * <span data-ttu-id="b0e7b-120">**Daten in**: Eingehende Daten in Ihre Web-App.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-120">**Data In**: Data ingress coming into your web app.</span></span>
    * <span data-ttu-id="b0e7b-121">Ausgeh folgende **Daten**: Datenausgang von Ihrer Web-App an Clients.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-121">**Data Out**: Data egress from your web app to clients.</span></span>
    * <span data-ttu-id="b0e7b-122">**Anforderungen**: Anzahl der HTTP-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-122">**Requests**: Count of HTTP requests.</span></span>
    * <span data-ttu-id="b0e7b-123">**Durchschnittliche Antwortzeit**: Die durchschnittliche Zeit, die die Web-App auf HTTP-Anforderungen antwortet.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-123">**Average Response Time**: Average time for the web app to respond to HTTP requests.</span></span>

    <span data-ttu-id="b0e7b-124">Auf dieser Seite befinden sich auch mehrere Self-service-Tools für die Problembehandlung und Optimierung.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-124">Several self-service tools for troubleshooting and optimization are also found on this page.</span></span>

    ![Screenshot mit Self-service-tools](./media/monitoring/wizards.png)

    * <span data-ttu-id="b0e7b-126">**Diagnose und Problembehandlung** ist ein Self-service-Ratgebers.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-126">**Diagnose and solve problems** is a self-service troubleshooter.</span></span>
    * <span data-ttu-id="b0e7b-127">**Application Insights** ist für die profilerstellung der Leistung und Verhalten der app und wird weiter unten in diesem Abschnitt erläutert.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-127">**Application Insights** is for profiling performance and app behavior, and is discussed later in this section.</span></span>
    * <span data-ttu-id="b0e7b-128">**App Service-Ratgebers** enthält Empfehlungen zum optimieren Ihre app-Erfahrung.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-128">**App Service Advisor** makes recommendations to tune your app experience.</span></span>

## <a name="advanced-monitoring"></a><span data-ttu-id="b0e7b-129">Erweiterte Überwachung</span><span class="sxs-lookup"><span data-stu-id="b0e7b-129">Advanced monitoring</span></span>

<span data-ttu-id="b0e7b-130">[Azure Monitor](/azure/monitoring-and-diagnostics/) ist der zentrale für alle überwachungsmetriken und Festlegen von Warnungen für Azure-Dienste.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-130">[Azure Monitor](/azure/monitoring-and-diagnostics/) is the centralized service for monitoring all metrics and setting alerts across Azure services.</span></span> <span data-ttu-id="b0e7b-131">Administratoren können in Azure Monitor können präzise Leistung nachzuverfolgen und Trends zu erkennen.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-131">Within Azure Monitor, administrators can granularly track performance and identify trends.</span></span> <span data-ttu-id="b0e7b-132">Jeder Azure-Dienst bietet eine eigene [Satz von Metriken](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) an Azure Monitor.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-132">Each Azure service offers its own [set of metrics](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) to Azure Monitor.</span></span>

## <a name="profile-with-application-insights"></a><span data-ttu-id="b0e7b-133">Profil mit Application Insights</span><span class="sxs-lookup"><span data-stu-id="b0e7b-133">Profile with Application Insights</span></span>

<span data-ttu-id="b0e7b-134">[Application Insights](/azure/application-insights/app-insights-overview) ist ein Azure-Dienst zum Analysieren der Leistung und Stabilität von Web-apps und wie Benutzer sie verwenden.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-134">[Application Insights](/azure/application-insights/app-insights-overview) is an Azure service for analyzing the performance and stability of web apps and how users use them.</span></span> <span data-ttu-id="b0e7b-135">Die Daten aus Application Insights ist besseres, als die von Azure Monitor.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-135">The data from Application Insights is broader and deeper than that of Azure Monitor.</span></span> <span data-ttu-id="b0e7b-136">Die Daten können Entwickler und Administratoren mit wichtigen Informationen zur Verbesserung der apps bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-136">The data can provide developers and administrators with key information for improving apps.</span></span> <span data-ttu-id="b0e7b-137">Application Insights können mit einer Azure App Service-Ressource ohne Änderungen am Code hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-137">Application Insights can be added to an Azure App Service resource without code changes.</span></span>

1. <span data-ttu-id="b0e7b-138">Öffnen der [Azure-Portal](https://portal.azure.com), und navigieren Sie zu der *Mywebapp\<Unique_number\>*  App Service.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-138">Open the [Azure portal](https://portal.azure.com), and then navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="b0e7b-139">Von der **Übersicht** Registerkarte, klicken Sie auf die **Application Insights** Kachel.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-139">From the **Overview** tab, click the **Application Insights** tile.</span></span>

    ![Application Insights-Kachel](./media/monitoring/app-insights.png)

1. <span data-ttu-id="b0e7b-141">Wählen Sie die **erstellen Sie neue Ressource** Optionsfeld.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-141">Select the **Create new resource** radio button.</span></span> <span data-ttu-id="b0e7b-142">Verwenden Sie den Standardnamen für die Ressource, und wählen Sie den Speicherort für die Application Insights-Ressource.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-142">Use the default resource name, and select the location for the Application Insights resource.</span></span> <span data-ttu-id="b0e7b-143">Der Speicherort muss nicht mit der Ihre Web-app übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-143">The location doesn't need to match that of your web app.</span></span>

    ![Application Insights-setup](./media/monitoring/new-app-insights.png)

1. <span data-ttu-id="b0e7b-145">Für **-Runtime-Framework**Option **ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-145">For **Runtime/Framework**, select **ASP.NET Core**.</span></span> <span data-ttu-id="b0e7b-146">Übernehmen Sie die Standardeinstellungen.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-146">Accept the default settings.</span></span>
1. <span data-ttu-id="b0e7b-147">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-147">Select **OK**.</span></span> <span data-ttu-id="b0e7b-148">Wenn Sie dazu aufgefordert werden, um zu bestätigen, wählen Sie **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-148">If prompted to confirm, select **Continue**.</span></span>
1. <span data-ttu-id="b0e7b-149">Nachdem die Ressource erstellt wurde, klicken Sie auf den Namen des Application Insights-Ressource direkt zu der Application Insights-Seite navigieren.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-149">After the resource has been created, click the name of Application Insights resource to navigate directly to the Application Insights page.</span></span>

    ![Neue Application Insights-Ressource ist bereit](./media/monitoring/new-app-insights-done.png)

<span data-ttu-id="b0e7b-151">Da die app verwendet wird, sammelt Daten.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-151">As the app is used, data accumulates.</span></span> <span data-ttu-id="b0e7b-152">Wählen Sie **aktualisieren** , die auf dem Blatt mit neuen Daten neu zu laden.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-152">Select **Refresh** to reload the blade with new data.</span></span>

![Application Insights-Registerkarte "Übersicht"](./media/monitoring/app-insights-overview.png)

<span data-ttu-id="b0e7b-154">Application Insights bietet nützliche Informationen ohne zusätzliche Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-154">Application Insights provides useful server-side information with no additional configuration.</span></span> <span data-ttu-id="b0e7b-155">Um den größtmöglichen aus Application Insights nutzen [Instrumentieren Ihrer app mit Application Insights SDK](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="b0e7b-155">To get the most value from Application Insights, [instrument your app with the Application Insights SDK](/azure/application-insights/app-insights-asp-net-core).</span></span> <span data-ttu-id="b0e7b-156">Wenn ordnungsgemäß konfiguriert ist, stellt der Dienst mit dem End-to-End-Überwachung auf den Webserver und Browser, einschließlich der clientseitigen Leistung.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-156">When properly configured, the service provides end-to-end monitoring across the web server and browser, including client-side performance.</span></span> <span data-ttu-id="b0e7b-157">Weitere Informationen finden Sie unter den [Application Insights-Dokumentation](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="b0e7b-157">For more information, see the [Application Insights documentation](/azure/application-insights/app-insights-overview).</span></span>

## <a name="logging"></a><span data-ttu-id="b0e7b-158">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="b0e7b-158">Logging</span></span>

<span data-ttu-id="b0e7b-159">Web-Server und app-Protokolle werden standardmäßig in Azure App Service deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-159">Web server and app logs are disabled by default in Azure App Service.</span></span> <span data-ttu-id="b0e7b-160">Aktivieren Sie die Protokolle mit den folgenden Schritten:</span><span class="sxs-lookup"><span data-stu-id="b0e7b-160">Enable the logs with the following steps:</span></span>

1. <span data-ttu-id="b0e7b-161">Öffnen der [Azure-Portal](https://portal.azure.com), und navigieren Sie zu der *Mywebapp\<Unique_number\>*  App Service.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-161">Open the [Azure portal](https://portal.azure.com), and navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="b0e7b-162">Klicken Sie im Menü auf der linken Seite einen Bildlauf nach unten, um die **Überwachung** Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-162">In the menu to the left, scroll down to the **Monitoring** section.</span></span> <span data-ttu-id="b0e7b-163">Wählen Sie **Diagnoseprotokolle**.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-163">Select **Diagnostics logs**.</span></span>

    ![Diagnoseprotokolle link](./media/monitoring/logging.png)

1. <span data-ttu-id="b0e7b-165">Aktivieren Sie **Anwendungsprotokollierung (Dateisystem)** .</span><span class="sxs-lookup"><span data-stu-id="b0e7b-165">Turn on **Application Logging (Filesystem)**.</span></span> <span data-ttu-id="b0e7b-166">Wenn Sie dazu aufgefordert werden, klicken Sie auf das Kontrollkästchen, um die Erweiterungen zum Aktivieren der app, die Protokollierung in der Web-app zu installieren.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-166">If prompted, click the box to install the extensions to enable app logging in the web app.</span></span>
1. <span data-ttu-id="b0e7b-167">Legen Sie **webserverprotokollierung** zu **Dateisystem**.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-167">Set **Web server logging** to **File System**.</span></span>
1. <span data-ttu-id="b0e7b-168">Geben Sie die **Beibehaltungsdauer** in Tagen.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-168">Enter the **Retention Period** in days.</span></span> <span data-ttu-id="b0e7b-169">Beispiel: 30.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-169">For example, 30.</span></span>
1. <span data-ttu-id="b0e7b-170">Klicken Sie auf **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-170">Click **Save**.</span></span>

<span data-ttu-id="b0e7b-171">ASP.NET Core und Web Server (App Service)-Protokolle werden für die Web-app generiert.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-171">ASP.NET Core and web server (App Service) logs are generated for the web app.</span></span> <span data-ttu-id="b0e7b-172">Sie können heruntergeladen werden, mithilfe der FTP-/FTPS-Informationen angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-172">They can be downloaded using the FTP/FTPS information displayed.</span></span> <span data-ttu-id="b0e7b-173">Das Kennwort ist identisch mit den Anmeldeinformationen für die Bereitstellung erstellt, die weiter oben in diesem Handbuch.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-173">The password is the same as the deployment credentials created earlier in this guide.</span></span> <span data-ttu-id="b0e7b-174">Die Protokolle möglich [Streaming direkt auf Ihren lokalen Computer mithilfe von PowerShell oder Azure-Befehlszeilenschnittstelle](/azure/app-service/web-sites-enable-diagnostic-log#download).</span><span class="sxs-lookup"><span data-stu-id="b0e7b-174">The logs can be [streamed directly to your local machine with PowerShell or Azure CLI](/azure/app-service/web-sites-enable-diagnostic-log#download).</span></span> <span data-ttu-id="b0e7b-175">Protokolle können auch sein, [in Application Insights angezeigt](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).</span><span class="sxs-lookup"><span data-stu-id="b0e7b-175">Logs can also be [viewed in Application Insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).</span></span>

## <a name="log-streaming"></a><span data-ttu-id="b0e7b-176">Protokollstreaming</span><span class="sxs-lookup"><span data-stu-id="b0e7b-176">Log streaming</span></span>

<span data-ttu-id="b0e7b-177">App und die Web-Server-Protokolle können in Echtzeit über das Portal gestreamt werden.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-177">App and web server logs can be streamed in real time through the portal.</span></span>

1. <span data-ttu-id="b0e7b-178">Öffnen der [Azure-Portal](https://portal.azure.com), und navigieren Sie zu der *Mywebapp\<Unique_number\>*  App Service.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-178">Open the [Azure portal](https://portal.azure.com), and navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="b0e7b-179">Klicken Sie im Menü auf der linken Seite einen Bildlauf nach unten, um die **Überwachung** aus, und wählen Sie **Protokollstream**.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-179">In the menu to the left, scroll down to the **Monitoring** section and select **Log stream**.</span></span>

    ![Screenshot mit Log Stream link](./media/monitoring/log-stream.png)

<span data-ttu-id="b0e7b-181">Protokolle können auch sein, [gestreamt, die über Azure-Befehlszeilenschnittstelle oder Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), einschließlich über Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-181">Logs can also be [streamed via Azure CLI or Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), including through the Cloud Shell.</span></span>

## <a name="alerts"></a><span data-ttu-id="b0e7b-182">Benachrichtigungen</span><span class="sxs-lookup"><span data-stu-id="b0e7b-182">Alerts</span></span>

<span data-ttu-id="b0e7b-183">Azure Monitor bietet Ihnen auch [Benachrichtigungen in Echtzeit](/azure/monitoring-and-diagnostics/insights-alerts-portal) basierend auf Metriken, administrative Ereignisse und anderen Kriterien.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-183">Azure Monitor also provides [real time alerts](/azure/monitoring-and-diagnostics/insights-alerts-portal) based on metrics, administrative events, and other criteria.</span></span>

> <span data-ttu-id="b0e7b-184">*Hinweis: Zurzeit ist eine Warnung zu Web-App-Metriken nur im Dienst "Warnungen (klassisch)" verfügbar.*</span><span class="sxs-lookup"><span data-stu-id="b0e7b-184">*Note: Currently alerting on web app metrics is only available in the Alerts (classic) service.*</span></span>

<span data-ttu-id="b0e7b-185">Die [Warnungen (klassisch) Dienst](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) finden Sie in Azure Monitor oder unter dem **Überwachung** Teil der App Service-Einstellungen.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-185">The [Alerts (classic) service](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) can be found in Azure Monitor or under the **Monitoring** section of the App Service settings.</span></span>

![Link "projektwarnungen (klassisch)"](./media/monitoring/alerts.png)

## <a name="live-debugging"></a><span data-ttu-id="b0e7b-187">Live-Debuggen</span><span class="sxs-lookup"><span data-stu-id="b0e7b-187">Live debugging</span></span>

<span data-ttu-id="b0e7b-188">Azure App Service möglich [Remote mit Visual Studio debuggt](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) Wenn Protokolle nicht genügend Informationen bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-188">Azure App Service can be [debugged remotely with Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) when logs don't provide enough information.</span></span> <span data-ttu-id="b0e7b-189">Remotedebuggen erfordert jedoch die app mit Debugsymbolen kompiliert werden.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-189">However, remote debugging requires the app to be compiled with debug symbols.</span></span> <span data-ttu-id="b0e7b-190">Debuggen sollte nicht in der Produktion, außer als letzten Ausweg erfolgen.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-190">Debugging shouldn't be done in production, except as a last resort.</span></span>

## <a name="conclusion"></a><span data-ttu-id="b0e7b-191">Schlussbemerkung</span><span class="sxs-lookup"><span data-stu-id="b0e7b-191">Conclusion</span></span>

<span data-ttu-id="b0e7b-192">In diesem Abschnitt haben Sie die folgenden Aufgaben aus:</span><span class="sxs-lookup"><span data-stu-id="b0e7b-192">In this section, you completed the following tasks:</span></span>

* <span data-ttu-id="b0e7b-193">Finden Sie grundlegende Überwachung und Problembehandlung für Daten im Azure-portal</span><span class="sxs-lookup"><span data-stu-id="b0e7b-193">Find basic monitoring and troubleshooting data in the Azure portal</span></span>
* <span data-ttu-id="b0e7b-194">Hier erfahren Sie, wie Azure Monitor einen tieferen Einblick in die Metriken für alle Azure-Dienste</span><span class="sxs-lookup"><span data-stu-id="b0e7b-194">Learn how Azure Monitor provides a deeper look at metrics across all Azure services</span></span>
* <span data-ttu-id="b0e7b-195">Verbinden Sie die Web-app mit Application Insights für die profilerstellung der app</span><span class="sxs-lookup"><span data-stu-id="b0e7b-195">Connect the web app with Application Insights for app profiling</span></span>
* <span data-ttu-id="b0e7b-196">Aktivieren Sie der Protokollierung, und erfahren Sie, wo Sie Protokolle herunterladen</span><span class="sxs-lookup"><span data-stu-id="b0e7b-196">Turn on logging and learn where to download logs</span></span>
* <span data-ttu-id="b0e7b-197">Stream-Protokolle in Echtzeit</span><span class="sxs-lookup"><span data-stu-id="b0e7b-197">Stream logs in real time</span></span>
* <span data-ttu-id="b0e7b-198">Erfahren Sie, wo Sie Warnungen einrichten</span><span class="sxs-lookup"><span data-stu-id="b0e7b-198">Learn where to set up alerts</span></span>
* <span data-ttu-id="b0e7b-199">Informationen Sie zu remote Debuggen Azure App Service-Web-apps.</span><span class="sxs-lookup"><span data-stu-id="b0e7b-199">Learn about remote debugging Azure App Service web apps.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="b0e7b-200">Weiterführende Literatur</span><span class="sxs-lookup"><span data-stu-id="b0e7b-200">Additional reading</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="b0e7b-201">Überwachen der Leistung von Azure-Web-app mit Application Insights</span><span class="sxs-lookup"><span data-stu-id="b0e7b-201">Monitor Azure web app performance with Application Insights</span></span>](/azure/application-insights/app-insights-azure-web-apps)
* [<span data-ttu-id="b0e7b-202">Aktivieren der Diagnoseprotokollierung für Apps in Azure App Service</span><span class="sxs-lookup"><span data-stu-id="b0e7b-202">Enable diagnostics logging for web apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)
* [<span data-ttu-id="b0e7b-203">Problembehandlung in einer Web-App in Azure App Service mithilfe von Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b0e7b-203">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="b0e7b-204">Erstellen von klassischen metrikwarnungen in Azure Monitor für Azure-Dienste – Azure-Portal</span><span class="sxs-lookup"><span data-stu-id="b0e7b-204">Create classic metric alerts in Azure Monitor for Azure services - Azure portal</span></span>](/azure/monitoring-and-diagnostics/insights-alerts-portal)

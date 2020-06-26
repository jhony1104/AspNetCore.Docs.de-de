---
title: Bereitstellen von ASP.NET Core-Apps in Azure App Service
author: bradygaster
description: Dieser Artikel enthält Links zu Azure-Host- und Bereitstellungsressourcen.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/16/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/azure-apps/index
ms.openlocfilehash: 9ffeebbf8125ddac5d6e621e411c4e86c5bd34b1
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399308"
---
# <a name="deploy-aspnet-core-apps-to-azure-app-service"></a><span data-ttu-id="e8766-103">Bereitstellen von ASP.NET Core-Apps in Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e8766-103">Deploy ASP.NET Core apps to Azure App Service</span></span>

<span data-ttu-id="e8766-104">[Azure App Service](https://azure.microsoft.com/services/app-service/) ist ein [Microsoft Cloud Computing-Plattformdienst](https://azure.microsoft.com/) zum Hosten von Web-Apps. Dazu gehört auch ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e8766-104">[Azure App Service](https://azure.microsoft.com/services/app-service/) is a [Microsoft cloud computing platform service](https://azure.microsoft.com/) for hosting web apps, including ASP.NET Core.</span></span>

## <a name="useful-resources"></a><span data-ttu-id="e8766-105">Nützliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="e8766-105">Useful resources</span></span>

<span data-ttu-id="e8766-106">In der [App Service-Dokumentation](/azure/app-service/) finden Sie die Azure-Apps-Dokumentation, Tutorials, Beispiele und Leitfäden sowie andere Ressourcen.</span><span class="sxs-lookup"><span data-stu-id="e8766-106">[App Service Documentation](/azure/app-service/) is the home for Azure Apps documentation, tutorials, samples, how-to guides, and other resources.</span></span> <span data-ttu-id="e8766-107">Dies sind zwei erwähnenswerte Tutorials, die auf das Hosten von ASP.NET Core-Apps eingehen:</span><span class="sxs-lookup"><span data-stu-id="e8766-107">Two notable tutorials that pertain to hosting ASP.NET Core apps are:</span></span>

[<span data-ttu-id="e8766-108">Erstellen einer ASP.NET Core-Web-App in Azure</span><span class="sxs-lookup"><span data-stu-id="e8766-108">Create an ASP.NET Core web app in Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet)  
<span data-ttu-id="e8766-109">Verwenden Sie Visual Studio, um ASP.NET Core-Web-Apps zu erstellen und in Azure App Service unter Windows bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="e8766-109">Use Visual Studio to create and deploy an ASP.NET Core web app to Azure App Service on Windows.</span></span>

[<span data-ttu-id="e8766-110">Erstellen einer .NET Core-App in App Service für Linux</span><span class="sxs-lookup"><span data-stu-id="e8766-110">Create an ASP.NET Core app in App Service on Linux</span></span>](/azure/app-service/containers/quickstart-dotnetcore)  
<span data-ttu-id="e8766-111">Verwenden Sie die Befehlszeile, um ASP.NET Core-Web-Apps zu erstellen und in Azure App Service unter Linux bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="e8766-111">Use the command line to create and deploy an ASP.NET Core web app to Azure App Service on Linux.</span></span>

<span data-ttu-id="e8766-112">Die ASP.NET Core-Version, die unter Azure App Service verfügbar ist, finden Sie unter [ASP.NET Core im App Service-Dashboard](https://aspnetcoreon.azurewebsites.net/).</span><span class="sxs-lookup"><span data-stu-id="e8766-112">See the [ASP.NET Core on App Service Dashboard](https://aspnetcoreon.azurewebsites.net/) for the version of ASP.NET Core available on Azure App service.</span></span>

<span data-ttu-id="e8766-113">Abonnieren Sie das Repository für [App Service-Ankündigungen](https://github.com/Azure/app-service-announcements/), und überwachen Sie die Issues.</span><span class="sxs-lookup"><span data-stu-id="e8766-113">Subscribe to the [App Service Announcements](https://github.com/Azure/app-service-announcements/) repository and monitor the issues.</span></span> <span data-ttu-id="e8766-114">Das App Service-Team postet regelmäßig Ankündigungen und neue Szenarien in App Service.</span><span class="sxs-lookup"><span data-stu-id="e8766-114">The App Service team regularly posts announcements and scenarios arriving in App Service.</span></span>

<span data-ttu-id="e8766-115">Die folgenden Artikel sind in der ASP.NET Core-Dokumentation verfügbar:</span><span class="sxs-lookup"><span data-stu-id="e8766-115">The following articles are available in ASP.NET Core documentation:</span></span>

<xref:tutorials/publish-to-azure-webapp-using-vs>  
<span data-ttu-id="e8766-116">Erfahren Sie, wie eine ASP.NET Core-App in Azure App Service mit Visual Studio veröffentlicht wird.</span><span class="sxs-lookup"><span data-stu-id="e8766-116">Learn how to publish an ASP.NET Core app to Azure App Service using Visual Studio.</span></span>

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>  
<span data-ttu-id="e8766-117">Erfahren Sie, wie Sie mit Visual Studio eine ASP.NET Core-Web-App erstellen und sie unter Verwendung von Git für Continuous Deployment in Azure App Service bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="e8766-117">Learn how to create an ASP.NET Core web app using Visual Studio and deploy it to Azure App Service using Git for continuous deployment.</span></span>

[<span data-ttu-id="e8766-118">Erstellen Sie Ihre erste Pipeline</span><span class="sxs-lookup"><span data-stu-id="e8766-118">Create your first pipeline</span></span>](/azure/devops/pipelines/get-started-yaml)  
<span data-ttu-id="e8766-119">Richten Sie ein CI-Build für eine ASP.NET Core-App ein, und erstellen Sie dann ein Continuous Deployment-Release für Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="e8766-119">Set up a CI build for an ASP.NET Core app, then create a continuous deployment release to Azure App Service.</span></span>

[<span data-ttu-id="e8766-120">Azure Web App-Sandbox</span><span class="sxs-lookup"><span data-stu-id="e8766-120">Azure Web App sandbox</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)  
<span data-ttu-id="e8766-121">Entdecken Sie die Einschränkungen der Azure App Service-Laufzeitausführung, die durch die Azure Apps-Plattform erzwungen werden.</span><span class="sxs-lookup"><span data-stu-id="e8766-121">Discover Azure App Service runtime execution limitations enforced by the Azure Apps platform.</span></span>

<xref:test/troubleshoot>  
<span data-ttu-id="e8766-122">In diesem Artikel werden Warnungen und Fehler erläutert. Außerdem erfahren Sie, wie die Problembehandlung in ASP.NET Core-Projekten funktioniert.</span><span class="sxs-lookup"><span data-stu-id="e8766-122">Understand and troubleshoot warnings and errors with ASP.NET Core projects.</span></span>

## <a name="application-configuration"></a><span data-ttu-id="e8766-123">Anwendungskonfiguration</span><span class="sxs-lookup"><span data-stu-id="e8766-123">Application configuration</span></span>

### <a name="platform"></a><span data-ttu-id="e8766-124">Plattform</span><span class="sxs-lookup"><span data-stu-id="e8766-124">Platform</span></span>

<span data-ttu-id="e8766-125">Die Plattformarchitektur (x86/x64) einer App Services-App wird in den Einstellungen der App im Azure-Portal festgelegt. Dies gilt für Apps, die in einem A-Series-Computetarif (Basic) oder einem höheren Hostingtarif gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="e8766-125">The platform architecture (x86/x64) of an App Services app is set in the app's settings in the Azure Portal for apps that are hosted on an A-series compute (Basic) or higher hosting tier.</span></span> <span data-ttu-id="e8766-126">Überprüfen Sie, ob die Veröffentlichungseinstellungen der App (z. B im [Veröffentlichungsprofil (PUBXML)](xref:host-and-deploy/visual-studio-publish-profiles) von Visual Studio) mit der Einstellung in der Dienstkonfiguration der App im Azure-Portal übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="e8766-126">Confirm that the app's publish settings (for example, in the Visual Studio [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) match the setting in the app's service configuration in the Azure Portal.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="e8766-127">In Azure App Service sind Runtimes für 64-Bit- und 32-Bit-Apps (x64 und x86) vorhanden.</span><span class="sxs-lookup"><span data-stu-id="e8766-127">Runtimes for 64-bit (x64) and 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="e8766-128">Das [.NET Core SDK](/dotnet/core/sdk), das in Azure App Service zur Verfügung steht, ist eine 32-Bit-Version. Sie können jedoch auch lokal erstellte 64-Bit-Apps mit der [Kudu](https://github.com/projectkudu/kudu/wiki)-Konsole oder dem Veröffentlichungsprozess in Visual Studio bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="e8766-128">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit, but you can deploy 64-bit apps built locally using the [Kudu](https://github.com/projectkudu/kudu/wiki) console or the publish process in Visual Studio.</span></span> <span data-ttu-id="e8766-129">Weitere Informationen finden Sie im Abschnitt [Publish and deploy the app (Veröffentlichen und Bereitstellen der App)](#publish-and-deploy-the-app).</span><span class="sxs-lookup"><span data-stu-id="e8766-129">For more information, see the [Publish and deploy the app](#publish-and-deploy-the-app) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="e8766-130">Für Apps mit nativen Abhängigkeiten stehen in Azure App Service Runtimes für 32-Bit- und 32-Bit-Apps (x86 und x86) zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="e8766-130">For apps with native dependencies, runtimes for 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="e8766-131">Das [.NET Core SDK](/dotnet/core/sdk) in Azure App Service ist eine 32-Bit-Version.</span><span class="sxs-lookup"><span data-stu-id="e8766-131">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit.</span></span>

::: moniker-end

<span data-ttu-id="e8766-132">Weitere Informationen zu .NET Core-Frameworkkomponenten und Verteilungsmethoden (z. B. .NET Core-Runtime und .NET Core SDK) finden Sie unter [Weitere Informationen zu .NET Core: Aufbau](/dotnet/core/about#composition).</span><span class="sxs-lookup"><span data-stu-id="e8766-132">For more information on .NET Core framework components and distribution methods, such as information on the .NET Core runtime and the .NET Core SDK, see [About .NET Core: Composition](/dotnet/core/about#composition).</span></span>

### <a name="packages"></a><span data-ttu-id="e8766-133">Pakete</span><span class="sxs-lookup"><span data-stu-id="e8766-133">Packages</span></span>

<span data-ttu-id="e8766-134">Beziehen Sie die folgenden NuGet-Pakete ein, die automatische Protokollierungsfeatures für Apps bieten, die für Azure App Service bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="e8766-134">Include the following NuGet packages to provide automatic logging features for apps deployed to Azure App Service:</span></span>

* <span data-ttu-id="e8766-135">[Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) verwendet [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration), um die ASP.NET Core-Lightup-Integration mit Azure App Service bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="e8766-135">[Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) uses [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) to provide ASP.NET Core light-up integration with Azure App Service.</span></span> <span data-ttu-id="e8766-136">Die hinzugefügten Protokollierungsfeatures werden vom `Microsoft.AspNetCore.AzureAppServicesIntegration`-Paket bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="e8766-136">The added logging features are provided by the `Microsoft.AspNetCore.AzureAppServicesIntegration` package.</span></span>
* <span data-ttu-id="e8766-137">[Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) führt [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) aus, um Anbieter für die Azure App Service-Diagnoseprotokollierung zum Paket `Microsoft.Extensions.Logging.AzureAppServices` hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="e8766-137">[Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) executes [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) to add Azure App Service diagnostics logging providers in the `Microsoft.Extensions.Logging.AzureAppServices` package.</span></span>
* <span data-ttu-id="e8766-138">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) stellt Protokollierungsimplementierungen bereit, um die Azure App Service-Features für Diagnoseprotokolle und Protokollstreaming zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="e8766-138">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) provides logger implementations to support Azure App Service diagnostics logs and log streaming features.</span></span>

<span data-ttu-id="e8766-139">Die Metapakete [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) sind nicht in den vorherigen Paketen enthalten.</span><span class="sxs-lookup"><span data-stu-id="e8766-139">The preceding packages aren't available from the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="e8766-140">Apps, deren Ziel das .NET Framework ist oder die auf das `Microsoft.AspNetCore.App`-Metapaket verweisen, müssen explizit auf die einzelnen Pakete in der Projektdatei der App verweisen.</span><span class="sxs-lookup"><span data-stu-id="e8766-140">Apps that target .NET Framework or reference the `Microsoft.AspNetCore.App` metapackage must explicitly reference the individual packages in the app's project file.</span></span>

## <a name="override-app-configuration-using-the-azure-portal"></a><span data-ttu-id="e8766-141">Überschreiben der App-Konfiguration im Azure-Portal</span><span class="sxs-lookup"><span data-stu-id="e8766-141">Override app configuration using the Azure Portal</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e8766-142">Über die App-Einstellungen im Azure-Portal können Sie Umgebungsvariablen für die App festlegen.</span><span class="sxs-lookup"><span data-stu-id="e8766-142">App settings in the Azure Portal permit you to set environment variables for the app.</span></span> <span data-ttu-id="e8766-143">Umgebungsvariablen können von [Umgebungsvariablen-Konfigurationsanbietern](xref:fundamentals/configuration/index#environment-variables) verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="e8766-143">Environment variables can be consumed by the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables).</span></span>

<span data-ttu-id="e8766-144">Wenn eine App-Einstellung im Azure-Portal erstellt oder geändert und die Schaltfläche **Speichern** ausgewählt wird, wird die Azure-App neu gestartet.</span><span class="sxs-lookup"><span data-stu-id="e8766-144">When an app setting is created or modified in the Azure Portal and the **Save** button is selected, the Azure App is restarted.</span></span> <span data-ttu-id="e8766-145">Die Umgebungsvariable steht der App nach dem Neustart des Diensts zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="e8766-145">The environment variable is available to the app after the service restarts.</span></span>

<span data-ttu-id="e8766-146">Wenn eine App einen [generischen Host](xref:fundamentals/host/generic-host) verwendet, werden beim Abruf von <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> zum Erstellen des Hosts Umgebungsvariablen in die Konfiguration der App geladen.</span><span class="sxs-lookup"><span data-stu-id="e8766-146">When an app uses the [Generic Host](xref:fundamentals/host/generic-host), environment variables are loaded into the app's configuration when <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> is called to build the host.</span></span> <span data-ttu-id="e8766-147">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host> und unter [Umgebungsvariablen-Konfigurationsanbieter](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="e8766-147">For more information, see <xref:fundamentals/host/generic-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables).</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e8766-148">Über die App-Einstellungen im Azure-Portal können Sie Umgebungsvariablen für die App festlegen.</span><span class="sxs-lookup"><span data-stu-id="e8766-148">App settings in the Azure Portal permit you to set environment variables for the app.</span></span> <span data-ttu-id="e8766-149">Umgebungsvariablen können von [Umgebungsvariablen-Konfigurationsanbietern](xref:fundamentals/configuration/index#environment-variables-configuration-provider) verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="e8766-149">Environment variables can be consumed by the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

<span data-ttu-id="e8766-150">Wenn eine App-Einstellung im Azure-Portal erstellt oder geändert und die Schaltfläche **Speichern** ausgewählt wird, wird die Azure-App neu gestartet.</span><span class="sxs-lookup"><span data-stu-id="e8766-150">When an app setting is created or modified in the Azure Portal and the **Save** button is selected, the Azure App is restarted.</span></span> <span data-ttu-id="e8766-151">Die Umgebungsvariable steht der App nach dem Neustart des Diensts zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="e8766-151">The environment variable is available to the app after the service restarts.</span></span>

<span data-ttu-id="e8766-152">Wenn eine App einen [Webhost](xref:fundamentals/host/web-host) verwendet, werden beim Abruf von <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> zum Erstellen des Hosts Umgebungsvariablen in die Konfiguration der App geladen.</span><span class="sxs-lookup"><span data-stu-id="e8766-152">When an app uses the [Web Host](xref:fundamentals/host/web-host), environment variables are loaded into the app's configuration when <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> is called to build the host.</span></span> <span data-ttu-id="e8766-153">Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host> und unter [Umgebungsvariablen-Konfigurationsanbieter](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="e8766-153">For more information, see <xref:fundamentals/host/web-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="e8766-154">Proxyserver und Lastenausgleichsszenarien</span><span class="sxs-lookup"><span data-stu-id="e8766-154">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="e8766-155">Die Middleware für die [Integration von IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), die beim [Out-of-Process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)-Hosting die Middleware für weitergeleitete Header konfiguriert, und das ASP.NET Core-Modul sind so konfiguriert, dass sie das Schema (HTTP/HTTPS) und die Remote-IP-Adresse an die Stelle weiterleiten, von der die Anforderung stammte.</span><span class="sxs-lookup"><span data-stu-id="e8766-155">The [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), which configures Forwarded Headers Middleware when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="e8766-156">Möglicherweise ist zusätzliche Konfiguration für Apps erforderlich, die hinter weiteren Proxyservern und Lastenausgleichsmodulen (Load Balancer) gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="e8766-156">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="e8766-157">Weitere Informationen hierzu feinden Sie unter [Konfigurieren von ASP.NET Core zur Verwendung mit Proxyservern und Lastenausgleich](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="e8766-157">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="monitoring-and-logging"></a><span data-ttu-id="e8766-158">Überwachung und Protokollierung</span><span class="sxs-lookup"><span data-stu-id="e8766-158">Monitoring and logging</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e8766-159">In App Service bereitgestellte ASP.NET Core-Apps erhalten automatisch die App Service-Erweiterung **ASP.NET Core Logging Integration** (ASP.NET Core-Protokollierungsintegration).</span><span class="sxs-lookup"><span data-stu-id="e8766-159">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Integration**.</span></span> <span data-ttu-id="e8766-160">Durch diese Erweiterung ist die Protokollierungsintegration für ASP.NET Core-Apps in Azure App Service möglich.</span><span class="sxs-lookup"><span data-stu-id="e8766-160">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e8766-161">In App Service bereitgestellte ASP.NET Core-Apps erhalten automatisch die App Service-Erweiterung **ASP.NET Core-Protokollierungserweiterungen**.</span><span class="sxs-lookup"><span data-stu-id="e8766-161">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Extensions**.</span></span> <span data-ttu-id="e8766-162">Durch diese Erweiterung ist die Protokollierungsintegration für ASP.NET Core-Apps in Azure App Service möglich.</span><span class="sxs-lookup"><span data-stu-id="e8766-162">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span></span>

::: moniker-end

<span data-ttu-id="e8766-163">In den folgenden Artikeln finden Sie Informationen zum Überwachen, Protokollieren und zur Problembehandlung:</span><span class="sxs-lookup"><span data-stu-id="e8766-163">For monitoring, logging, and troubleshooting information, see the following articles:</span></span>

[<span data-ttu-id="e8766-164">Überwachen von Apps in Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e8766-164">Monitor apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)  
<span data-ttu-id="e8766-165">Erfahren Sie, wie Sie Kontingente und Metrik für Apps und App Service-Pläne prüfen.</span><span class="sxs-lookup"><span data-stu-id="e8766-165">Learn how to review quotas and metrics for apps and App Service plans.</span></span>

[<span data-ttu-id="e8766-166">Aktivieren der Diagnoseprotokollierung für Apps in Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e8766-166">Enable diagnostics logging for apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)  
<span data-ttu-id="e8766-167">Erfahren Sie, wie Sie die Diagnosesprotokollierung für HTTP-Statuscodes, fehlgeschlagene Anforderungen und Webserveraktivitäten aktivieren und auf die Protokolle zugreifen.</span><span class="sxs-lookup"><span data-stu-id="e8766-167">Discover how to enable and access diagnostic logging for HTTP status codes, failed requests, and web server activity.</span></span>

<xref:fundamentals/error-handling>  
<span data-ttu-id="e8766-168">Lernen Sie gängige Methoden zur Fehlerbehandlung in ASP.NET Core-Apps kennen.</span><span class="sxs-lookup"><span data-stu-id="e8766-168">Understand common approaches to handling errors in ASP.NET Core apps.</span></span>

<xref:test/troubleshoot-azure-iis>  
<span data-ttu-id="e8766-169">Erfahren Sie, wie Sie Probleme mit Azure App Service-Bereitstellungen mit ASP.NET Core-Apps diagnostizieren können.</span><span class="sxs-lookup"><span data-stu-id="e8766-169">Learn how to diagnose issues with Azure App Service deployments with ASP.NET Core apps.</span></span>

<xref:host-and-deploy/azure-iis-errors-reference>  
<span data-ttu-id="e8766-170">Sehen Sie sich häufig auftretende Fehler und entsprechende Hinweise zur Fehlerbehebung bei der Bereitstellungskonfiguration für von Azure App Service/IIS gehosteten Apps an.</span><span class="sxs-lookup"><span data-stu-id="e8766-170">See the common deployment configuration errors for apps hosted by Azure App Service/IIS with troubleshooting advice.</span></span>

## <a name="data-protection-key-ring-and-deployment-slots"></a><span data-ttu-id="e8766-171">Data Protection-Schlüsselbund und -Bereitstellungsslots</span><span class="sxs-lookup"><span data-stu-id="e8766-171">Data Protection key ring and deployment slots</span></span>

<span data-ttu-id="e8766-172">[Data Protection-Schlüssel](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) werden im Ordner *%HOME%\ASP.NET\DataProtection-Keys* gespeichert.</span><span class="sxs-lookup"><span data-stu-id="e8766-172">[Data Protection keys](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) are persisted to the *%HOME%\ASP.NET\DataProtection-Keys* folder.</span></span> <span data-ttu-id="e8766-173">Dieser Ordner wird von einem Netzwerkspeicher unterstützt und mit allen Computern, auf denen die App gehostet wird, synchronisiert.</span><span class="sxs-lookup"><span data-stu-id="e8766-173">This folder is backed by network storage and is synchronized across all machines hosting the app.</span></span> <span data-ttu-id="e8766-174">Ruhende Schlüssel werden nicht geschützt.</span><span class="sxs-lookup"><span data-stu-id="e8766-174">Keys aren't protected at rest.</span></span> <span data-ttu-id="e8766-175">Dieser Ordner stellt den Schlüsselbund für alle Instanzen einer App in einem einzelnen Bereitstellungsslot bereit.</span><span class="sxs-lookup"><span data-stu-id="e8766-175">This folder supplies the key ring to all instances of an app in a single deployment slot.</span></span> <span data-ttu-id="e8766-176">Separate Bereitstellungsslots, wie Staging und Produktion, verwenden keinen gemeinsamen Schlüsselbund.</span><span class="sxs-lookup"><span data-stu-id="e8766-176">Separate deployment slots, such as Staging and Production, don't share a key ring.</span></span>

<span data-ttu-id="e8766-177">Wenn Sie zwischen Bereitstellungsslots wechseln, können Systeme, die Data Protection verwenden, gespeicherte Daten nicht mit dem Schlüsselbund des vorherigen Slots entschlüsseln.</span><span class="sxs-lookup"><span data-stu-id="e8766-177">When swapping between deployment slots, any system using data protection won't be able to decrypt stored data using the key ring inside the previous slot.</span></span> <span data-ttu-id="e8766-178">ASP.NET-Cookiemiddleware verwendet Data Protection zum Schutz ihrer Cookies.</span><span class="sxs-lookup"><span data-stu-id="e8766-178">ASP.NET Cookie Middleware uses data protection to protect its cookies.</span></span> <span data-ttu-id="e8766-179">Dies führt dazu, dass Benutzer in einer App abgemeldet werden, die Standard-ASP.NET-Cookiemiddleware verwendet.</span><span class="sxs-lookup"><span data-stu-id="e8766-179">This leads to users being signed out of an app that uses the standard ASP.NET Cookie Middleware.</span></span> <span data-ttu-id="e8766-180">Verwende Sie einen externen Schlüsselbundanbieter für eine slotunabhängige Schlüsselbundlösung wie z.B.:</span><span class="sxs-lookup"><span data-stu-id="e8766-180">For a slot-independent key ring solution, use an external key ring provider, such as:</span></span>

* <span data-ttu-id="e8766-181">Azure Blob Storage</span><span class="sxs-lookup"><span data-stu-id="e8766-181">Azure Blob Storage</span></span>
* <span data-ttu-id="e8766-182">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="e8766-182">Azure Key Vault</span></span>
* <span data-ttu-id="e8766-183">SQL-Speicher</span><span class="sxs-lookup"><span data-stu-id="e8766-183">SQL store</span></span>
* <span data-ttu-id="e8766-184">Redis Cache</span><span class="sxs-lookup"><span data-stu-id="e8766-184">Redis cache</span></span>

<span data-ttu-id="e8766-185">Weitere Informationen finden Sie unter <xref:security/data-protection/implementation/key-storage-providers>.</span><span class="sxs-lookup"><span data-stu-id="e8766-185">For more information, see <xref:security/data-protection/implementation/key-storage-providers>.</span></span>
<a name="deploy-aspnet-core-preview-release-to-azure-app-service"></a>

## <a name="deploy-an-aspnet-core-app-that-uses-a-net-core-preview"></a><span data-ttu-id="e8766-186">Bereitstellen einer ASP.NET Core-App, die eine Vorschauversion von .NET Core verwendet</span><span class="sxs-lookup"><span data-stu-id="e8766-186">Deploy an ASP.NET Core app that uses a .NET Core preview</span></span>

<span data-ttu-id="e8766-187">In den folgenden Ressourcen finden Sie Informationen zum Bereitstellen einer App, die eine Vorschauversion von .NET Core verwendet.</span><span class="sxs-lookup"><span data-stu-id="e8766-187">To deploy an app that uses a preview release of .NET Core, see the following resources.</span></span> <span data-ttu-id="e8766-188">Diese Verfahren werden auch verwendet, wenn die Runtime verfügbar ist, aber das SDK nicht in Azure App Service installiert wurde.</span><span class="sxs-lookup"><span data-stu-id="e8766-188">These approaches are also used when the runtime is available but the SDK hasn't been installed on Azure App Service.</span></span>

* [<span data-ttu-id="e8766-189">Angeben der .NET Core SDK-Version mithilfe von Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="e8766-189">Specify the .NET Core SDK Version using Azure Pipelines</span></span>](#specify-the-net-core-sdk-version-using-azure-pipelines)
* [<span data-ttu-id="e8766-190">Bereitstellen einer eigenständigen Vorschauversions-App</span><span class="sxs-lookup"><span data-stu-id="e8766-190">Deploy a self-contained preview app</span></span>](#deploy-a-self-contained-preview-app)
* [<span data-ttu-id="e8766-191">Verwenden von Docker mit Web-Apps für Container</span><span class="sxs-lookup"><span data-stu-id="e8766-191">Use Docker with Web Apps for containers</span></span>](#use-docker-with-web-apps-for-containers)
* [<span data-ttu-id="e8766-192">Installieren der Websiteerweiterung (Vorschau)</span><span class="sxs-lookup"><span data-stu-id="e8766-192">Install the preview site extension</span></span>](#install-the-preview-site-extension)

<span data-ttu-id="e8766-193">Die ASP.NET Core-Version, die unter Azure App Service verfügbar ist, finden Sie unter [ASP.NET Core im App Service-Dashboard](https://aspnetcoreon.azurewebsites.net/).</span><span class="sxs-lookup"><span data-stu-id="e8766-193">See the [ASP.NET Core on App Service Dashboard](https://aspnetcoreon.azurewebsites.net/) for the version of ASP.NET Core available on Azure App service.</span></span>

### <a name="specify-the-net-core-sdk-version-using-azure-pipelines"></a><span data-ttu-id="e8766-194">Angeben der .NET Core SDK-Version mithilfe von Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="e8766-194">Specify the .NET Core SDK Version using Azure Pipelines</span></span>

<span data-ttu-id="e8766-195">Verwenden Sie die Szenarien zum [Entwerfen einer CI/CD-Pipeline mithilfe von Azure DevOps](/azure/app-service/deploy-continuous-deployment), um einen Continuous Integration-Build mit Azure DevOps einzurichten.</span><span class="sxs-lookup"><span data-stu-id="e8766-195">Use [Azure App Service CI/CD scenarios](/azure/app-service/deploy-continuous-deployment) to set up a continuous integration build with Azure DevOps.</span></span> <span data-ttu-id="e8766-196">Nach dem Erstellen des Azure DevOps-Builds können Sie diesen optional für die Verwendung einer bestimmten SDK-Version konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="e8766-196">After the Azure DevOps build is created, optionally configure the build to use a specific SDK version.</span></span> 

#### <a name="specify-the-net-core-sdk-version"></a><span data-ttu-id="e8766-197">Angeben der .NET Core SDK-Version</span><span class="sxs-lookup"><span data-stu-id="e8766-197">Specify the .NET Core SDK version</span></span>

<span data-ttu-id="e8766-198">Wenn Sie das App Service-Bereitstellungscenter zum Erstellen eines Azure DevOps-Builds verwenden, enthält die Standardbuildpipeline die Schritte für `Restore`, `Build`, `Test` und `Publish`.</span><span class="sxs-lookup"><span data-stu-id="e8766-198">When using the App Service deployment center to create an Azure DevOps build, the default build pipeline includes steps for `Restore`, `Build`, `Test`, and `Publish`.</span></span> <span data-ttu-id="e8766-199">Zum Angeben der SDK-Version klicken Sie auf die Schaltfläche **Hinzufügen (+)** in der Liste mit Agent-Aufträgen, um einen neuen Schritt hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="e8766-199">To specify the SDK version, select the **Add (+)** button in the Agent job list to add a new step.</span></span> <span data-ttu-id="e8766-200">Suchen Sie in der Suchleiste nach **.NET Core SDK**.</span><span class="sxs-lookup"><span data-stu-id="e8766-200">Search for **.NET Core SDK** in the search bar.</span></span> 

![Hinzufügen des .NET Core SDK-Schritts](index/add-sdk-step.png)

<span data-ttu-id="e8766-202">Verschieben Sie den Schritt an die erste Stelle im Build, sodass die nachfolgenden Schritte die angegebene Version des .NET Core SDK verwenden.</span><span class="sxs-lookup"><span data-stu-id="e8766-202">Move the step into the first position in the build so that the steps following it use the specified version of the .NET Core SDK.</span></span> <span data-ttu-id="e8766-203">Geben Sie die Version des .NET Core SDK an.</span><span class="sxs-lookup"><span data-stu-id="e8766-203">Specify the version of the .NET Core SDK.</span></span> <span data-ttu-id="e8766-204">In diesem Beispiel ist das SDK auf `3.0.100` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="e8766-204">In this example, the SDK is set to `3.0.100`.</span></span>

![Abgeschlossener SDK-Schritt](index/sdk-step-first-place.png)

<span data-ttu-id="e8766-206">Um eine [eigenständige Bereitstellung](/dotnet/core/deploying/#self-contained-deployments-scd) (Self-Contained Deployment, SCD) zu veröffentlichen, konfigurieren Sie SCD im Schritt `Publish`, und geben Sie den [Runtimebezeichner](/dotnet/core/rid-catalog) (Runtime Identifier, RID) an.</span><span class="sxs-lookup"><span data-stu-id="e8766-206">To publish a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd), configure SCD in the `Publish` step and provide the [Runtime Identifier (RID)](/dotnet/core/rid-catalog).</span></span>

![Veröffentlichung einer eigenständigen Bereitstellung](index/self-contained.png)

### <a name="deploy-a-self-contained-preview-app"></a><span data-ttu-id="e8766-208">Bereitstellen einer eigenständigen Vorschau-App</span><span class="sxs-lookup"><span data-stu-id="e8766-208">Deploy a self-contained preview app</span></span>

<span data-ttu-id="e8766-209">Eine [eigenständige Bereitstellung](/dotnet/core/deploying/#self-contained-deployments-scd), die auf eine Vorschauruntime abzielt, enthält die Vorschauruntime in der Bereitstellung.</span><span class="sxs-lookup"><span data-stu-id="e8766-209">A [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) that targets a preview runtime carries the preview runtime in the deployment.</span></span>

<span data-ttu-id="e8766-210">Wenn Sie eine eigenständige App bereitstellen, gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="e8766-210">When deploying a self-contained app:</span></span>

* <span data-ttu-id="e8766-211">Die Site in Azure App Service erfordert nicht die [Vorschau-Websiteerweiterung](#install-the-preview-site-extension).</span><span class="sxs-lookup"><span data-stu-id="e8766-211">The site in Azure App Service doesn't require the [preview site extension](#install-the-preview-site-extension).</span></span>
* <span data-ttu-id="e8766-212">Die App muss gemäß einem anderen Ansatz veröffentlicht werden, als beim Veröffentlichen für eine [frameworkabhängige Bereitstellung](/dotnet/core/deploying#framework-dependent-deployments-fdd).</span><span class="sxs-lookup"><span data-stu-id="e8766-212">The app must be published following a different approach than when publishing for a [framework-dependent deployment (FDD)](/dotnet/core/deploying#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="e8766-213">Befolgen Sie die Anweisungen im Abschnitt [Bereitstellen einer eigenständigen Vorschau-App](#deploy-the-app-self-contained).</span><span class="sxs-lookup"><span data-stu-id="e8766-213">Follow the guidance in the [Deploy the app self-contained](#deploy-the-app-self-contained) section.</span></span>

### <a name="use-docker-with-web-apps-for-containers"></a><span data-ttu-id="e8766-214">Verwenden von Docker mit Web-Apps für Container</span><span class="sxs-lookup"><span data-stu-id="e8766-214">Use Docker with Web Apps for containers</span></span>

<span data-ttu-id="e8766-215">Der [Docker-Hub](https://hub.docker.com/r/microsoft/aspnetcore/) enthält die aktuellen Images für die Docker-Vorschauversion.</span><span class="sxs-lookup"><span data-stu-id="e8766-215">The [Docker Hub](https://hub.docker.com/r/microsoft/aspnetcore/) contains the latest preview Docker images.</span></span> <span data-ttu-id="e8766-216">Die Images können als Basisimage verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="e8766-216">The images can be used as a base image.</span></span> <span data-ttu-id="e8766-217">Verwenden Sie das Image, und führen Sie wie gewohnt eine Bereitstellung für Web-Apps für Container durch.</span><span class="sxs-lookup"><span data-stu-id="e8766-217">Use the image and deploy to Web Apps for Containers normally.</span></span>

### <a name="install-the-preview-site-extension"></a><span data-ttu-id="e8766-218">Installieren der Websiteerweiterung (Vorschau)</span><span class="sxs-lookup"><span data-stu-id="e8766-218">Install the preview site extension</span></span>

<span data-ttu-id="e8766-219">Sollte ein Problem mit dem Verwenden der Vorschau der Websiteerweiterung auftreten, öffnen Sie ein [dotnet/AspNetCore-Issue](https://github.com/dotnet/AspNetCore/issues).</span><span class="sxs-lookup"><span data-stu-id="e8766-219">If a problem occurs using the preview site extension, open an [dotnet/AspNetCore issue](https://github.com/dotnet/AspNetCore/issues).</span></span>

1. <span data-ttu-id="e8766-220">Navigieren Sie im Azure-Portal zu „App Service“.</span><span class="sxs-lookup"><span data-stu-id="e8766-220">From the Azure Portal, navigate to the App Service.</span></span>
1. <span data-ttu-id="e8766-221">Wählen Sie die Web-App aus.</span><span class="sxs-lookup"><span data-stu-id="e8766-221">Select the web app.</span></span>
1. <span data-ttu-id="e8766-222">Geben Sie „ex“ in das Suchfeld ein, um nach „Extensions“ (Erweiterungen) zu filtern, oder durchsuchen Sie die Liste der Verwaltungstools.</span><span class="sxs-lookup"><span data-stu-id="e8766-222">Type "ex" in the search box to filter for "Extensions" or scroll down the list of management tools.</span></span>
1. <span data-ttu-id="e8766-223">Wählen Sie **Erweiterungen** aus.</span><span class="sxs-lookup"><span data-stu-id="e8766-223">Select **Extensions**.</span></span>
1. <span data-ttu-id="e8766-224">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="e8766-224">Select **Add**.</span></span>
1. <span data-ttu-id="e8766-225">Wählen Sie die Erweiterung **ASP.NET Core {X.Y} ({x64|x86}) Runtime** aus der Liste aus. Dabei ist `{X.Y}` die ASP.NET Core-Vorschauversion, und `{x64|x86}` gibt die Plattform an.</span><span class="sxs-lookup"><span data-stu-id="e8766-225">Select the **ASP.NET Core {X.Y} ({x64|x86}) Runtime** extension from the list, where `{X.Y}` is the ASP.NET Core preview version and `{x64|x86}` specifies the platform.</span></span>
1. <span data-ttu-id="e8766-226">Klicken Sie auf **OK**, um die rechtlichen Bedingungen zu akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="e8766-226">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="e8766-227">Wählen Sie **OK** aus, um die Erweiterung zu installieren.</span><span class="sxs-lookup"><span data-stu-id="e8766-227">Select **OK** to install the extension.</span></span>

<span data-ttu-id="e8766-228">Nach Abschluss dieses Vorgangs wird die neueste .NET Core-Vorschauversion installiert.</span><span class="sxs-lookup"><span data-stu-id="e8766-228">When the operation completes, the latest .NET Core preview is installed.</span></span> <span data-ttu-id="e8766-229">Überprüfen Sie die Installation:</span><span class="sxs-lookup"><span data-stu-id="e8766-229">Verify the installation:</span></span>

1. <span data-ttu-id="e8766-230">Wählen Sie **Erweiterte Tools** aus.</span><span class="sxs-lookup"><span data-stu-id="e8766-230">Select **Advanced Tools**.</span></span>
1. <span data-ttu-id="e8766-231">Wählen Sie **Go** unter **Erweiterte Tools** aus.</span><span class="sxs-lookup"><span data-stu-id="e8766-231">Select **Go** in **Advanced Tools**.</span></span>
1. <span data-ttu-id="e8766-232">Wählen Sie das Menüelement **Debugkonsole** > **PowerShell** aus.</span><span class="sxs-lookup"><span data-stu-id="e8766-232">Select the **Debug console** > **PowerShell** menu item.</span></span>
1. <span data-ttu-id="e8766-233">Führen Sie in der PowerShell-Eingabeaufforderung den folgenden Befehl aus.</span><span class="sxs-lookup"><span data-stu-id="e8766-233">At the PowerShell prompt, execute the following command.</span></span> <span data-ttu-id="e8766-234">Ersetzen Sie im Befehl die ASP.NET Core-Runtimeversion durch `{X.Y}` und die Plattform durch `{PLATFORM}`:</span><span class="sxs-lookup"><span data-stu-id="e8766-234">Substitute the ASP.NET Core runtime version for `{X.Y}` and the platform for `{PLATFORM}` in the command:</span></span>

   ```powershell
   Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.{PLATFORM}\
   ```

   <span data-ttu-id="e8766-235">Der Befehl gibt `True` zurück, wenn die x64-Vorschauruntime installiert ist.</span><span class="sxs-lookup"><span data-stu-id="e8766-235">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="e8766-236">Die Plattformarchitektur (x86/x64) einer App Services-App wird in den Einstellungen der App im Azure-Portal festgelegt. Dies gilt für Apps, die in einem A-Series-Computetarif (Basic) oder einem höheren Hostingtarif gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="e8766-236">The platform architecture (x86/x64) of an App Services app is set in the app's settings in the Azure Portal for apps that are hosted on an A-series compute (Basic) or higher hosting tier.</span></span> <span data-ttu-id="e8766-237">Überprüfen Sie, ob die Veröffentlichungseinstellungen der App (z. B im [Veröffentlichungsprofil (PUBXML)](xref:host-and-deploy/visual-studio-publish-profiles) von Visual Studio) mit der Einstellung in der Dienstkonfiguration der App im Azure-Portal übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="e8766-237">Confirm that the app's publish settings (for example, in the Visual Studio [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) match the setting in the app's service configuration in the Azure portal.</span></span>
>
> <span data-ttu-id="e8766-238">Wenn die Anwendung im In-Process-Modus ausgeführt wird und die Plattformarchitektur für 64-Bit (x64) konfiguriert ist, verwendet das ASP.NET Core-Modul die 64-Bit-Vorschauruntime, falls vorhanden.</span><span class="sxs-lookup"><span data-stu-id="e8766-238">If the app is run in in-process mode and the platform architecture is configured for 64-bit (x64), the ASP.NET Core Module uses the 64-bit preview runtime, if present.</span></span> <span data-ttu-id="e8766-239">Installieren Sie die Erweiterung **ASP.NET Core {X.Y} (x64) Runtime** über das Azure-Portal.</span><span class="sxs-lookup"><span data-stu-id="e8766-239">Install the **ASP.NET Core {X.Y} (x64) Runtime** extension using the Azure Portal.</span></span>
>
> <span data-ttu-id="e8766-240">Nach der Installation der x64-Vorschauruntime führen Sie den folgenden Befehl im Azure Kudu PowerShell-Befehlsfenster aus, um die Installation zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="e8766-240">After installing the x64 preview runtime, run the following command in the Azure Kudu PowerShell command window to verify the installation.</span></span> <span data-ttu-id="e8766-241">Ersetzen Sie im folgenden Befehl die ASP.NET Core-Runtimeversion für `{X.Y}`:</span><span class="sxs-lookup"><span data-stu-id="e8766-241">Substitute the ASP.NET Core runtime version for `{X.Y}` in the following command:</span></span>
>
> ```powershell
> Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64\
> ```
>
> <span data-ttu-id="e8766-242">Der Befehl gibt `True` zurück, wenn die x64-Vorschauruntime installiert ist.</span><span class="sxs-lookup"><span data-stu-id="e8766-242">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="e8766-243">**ASP.NET Core-Erweiterungen** aktivieren zusätzliche Funktionen für ASP.NET Core in Azure App Services, z.B. Azure-Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="e8766-243">**ASP.NET Core Extensions** enables additional functionality for ASP.NET Core on Azure App Services, such as enabling Azure logging.</span></span> <span data-ttu-id="e8766-244">Die Erweiterung wird automatisch installiert, wenn die Bereitstellung aus Visual Studio erfolgt.</span><span class="sxs-lookup"><span data-stu-id="e8766-244">The extension is installed automatically when deploying from Visual Studio.</span></span> <span data-ttu-id="e8766-245">Wenn die Erweiterung nicht installiert ist, installieren Sie sie für die App.</span><span class="sxs-lookup"><span data-stu-id="e8766-245">If the extension isn't installed, install it for the app.</span></span>

<span data-ttu-id="e8766-246">**Verwenden der Vorschau-Websiteerweiterung mit einer ARM-Vorlage**</span><span class="sxs-lookup"><span data-stu-id="e8766-246">**Use the preview site extension with an ARM template**</span></span>

<span data-ttu-id="e8766-247">Wenn Sie eine ARM-Vorlage zum Erstellen und Bereitstellen von Anwendungen verwenden, können Sie den Ressourcentyp `siteextensions` verwenden, um die Websiteerweiterung zu einer Web-App hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="e8766-247">If an ARM template is used to create and deploy apps, the `siteextensions` resource type can be used to add the site extension to a web app.</span></span> <span data-ttu-id="e8766-248">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e8766-248">For example:</span></span>

[!code-json[](index/sample/arm.json?highlight=2)]

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="e8766-249">Veröffentlichen und Bereitstellen der App</span><span class="sxs-lookup"><span data-stu-id="e8766-249">Publish and deploy the app</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="e8766-250">Für eine 64-Bit-Bereitstellung:</span><span class="sxs-lookup"><span data-stu-id="e8766-250">For a 64-bit deployment:</span></span>

* <span data-ttu-id="e8766-251">Verwenden Sie ein .NET Core SDK mit 64 Bit, um eine 64-Bit-App zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="e8766-251">Use a 64-bit .NET Core SDK to build a 64-bit app.</span></span>
* <span data-ttu-id="e8766-252">Legen Sie die **Plattform** in App Service unter **Konfiguration** > **Allgemeine Einstellungen** auf **64 Bit** fest.</span><span class="sxs-lookup"><span data-stu-id="e8766-252">Set the **Platform** to **64 Bit** in the App Service's **Configuration** > **General settings**.</span></span> <span data-ttu-id="e8766-253">Die App muss den Serviceplan „Basic“ oder höher verwenden, damit die Bitanzahl der Plattform festgelegt werden kann.</span><span class="sxs-lookup"><span data-stu-id="e8766-253">The app must use a Basic or higher service plan to enable the choice of platform bitness.</span></span>

::: moniker-end

### <a name="deploy-the-app-framework-dependent"></a><span data-ttu-id="e8766-254">Frameworkabhängige Bereitstellung einer App</span><span class="sxs-lookup"><span data-stu-id="e8766-254">Deploy the app framework-dependent</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e8766-255">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e8766-255">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="e8766-256">Klicken Sie in der Visual Studio-Symbolleiste auf **Erstellen** >  **{Anwendungsname} veröffentlichen**, oder klicken Sie mit der rechten Maustaste auf den **Projektmappen-Explorer**, und klicken Sie dann auf **Veröffentlichen**.</span><span class="sxs-lookup"><span data-stu-id="e8766-256">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar or right-click the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="e8766-257">Vergewissern Sie sich im Dialogfeld **Veröffentlichungsziel auswählen**, dass **App Service** ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="e8766-257">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="e8766-258">Wählen Sie **Erweitert** aus.</span><span class="sxs-lookup"><span data-stu-id="e8766-258">Select **Advanced**.</span></span> <span data-ttu-id="e8766-259">Das Dialogfeld **Veröffentlichen** wird geöffnet.</span><span class="sxs-lookup"><span data-stu-id="e8766-259">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="e8766-260">Führen Sie im Dialogfeld **Veröffentlichen** folgende Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="e8766-260">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="e8766-261">Überprüfen Sie, ob die Konfiguration **Release** ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="e8766-261">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="e8766-262">Öffnen Sie die Dropdownliste **Bereitstellungsmodus**, und wählen Sie **Frameworkabhängig** aus.</span><span class="sxs-lookup"><span data-stu-id="e8766-262">Open the **Deployment Mode** drop-down list and select **Framework-Dependent**.</span></span>
   * <span data-ttu-id="e8766-263">Wählen Sie **Portierbar** für **Zielruntime** aus.</span><span class="sxs-lookup"><span data-stu-id="e8766-263">Select **Portable** as the **Target Runtime**.</span></span>
   * <span data-ttu-id="e8766-264">Wenn Sie zusätzliche Dateien bei der Bereitstellung entfernen müssen, öffnen Sie **Dateiveröffentlichungsoptionen**, und aktivieren Sie das Kontrollkästchen, um zusätzliche Dateien am Ziel zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="e8766-264">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="e8766-265">Klicken Sie auf **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="e8766-265">Select **Save**.</span></span>
1. <span data-ttu-id="e8766-266">Sie können eine neue Website erstellen oder eine vorhandene aktualisieren, indem Sie die folgenden Anweisungen des Veröffentlichungs-Assistenten befolgen.</span><span class="sxs-lookup"><span data-stu-id="e8766-266">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="e8766-267">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="e8766-267">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="e8766-268">Geben Sie in der Projektdatei keine [RID (relativer Bezeichner)](/dotnet/core/rid-catalog) an.</span><span class="sxs-lookup"><span data-stu-id="e8766-268">In the project file, don't specify a [Runtime Identifier (RID)](/dotnet/core/rid-catalog).</span></span>

1. <span data-ttu-id="e8766-269">Nutzen Sie die Befehlsshell, um die App mit der Konfiguration „Release“ mit dem Befehl [dotnet publish](/dotnet/core/tools/dotnet-publish) zu veröffentlichen.</span><span class="sxs-lookup"><span data-stu-id="e8766-269">From a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="e8766-270">Im folgenden Beispiel wird die App als frameworkabhängige App veröffentlicht:</span><span class="sxs-lookup"><span data-stu-id="e8766-270">In the following example, the app is published as a framework-dependent app:</span></span>

   ```console
   dotnet publish --configuration Release
   ```

1. <span data-ttu-id="e8766-271">Verschieben Sie den Inhalt des Verzeichnisses *bin/Release/{ZIELFRAMEWORK}/publish* in den Speicherort in App Service.</span><span class="sxs-lookup"><span data-stu-id="e8766-271">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* directory to the site in App Service.</span></span> <span data-ttu-id="e8766-272">Wenn Sie die Inhalte des Ordners *publish* von Ihrer lokalen Festplatte oder Netzwerkfreigabe direkt über die [Kudu](https://github.com/projectkudu/kudu/wiki)-Konsole in App Service verschieben möchten, ziehen Sie die Dateien in den Ordner `D:\home\site\wwwroot` in der Kudu-Konsole.</span><span class="sxs-lookup"><span data-stu-id="e8766-272">If dragging the *publish* folder contents from your local hard drive or network share directly to App Service in the [Kudu](https://github.com/projectkudu/kudu/wiki) console, drag the files to the `D:\home\site\wwwroot` folder in the Kudu console.</span></span>

---

### <a name="deploy-the-app-self-contained"></a><span data-ttu-id="e8766-273">Bereitstellen der App als eigenständige App</span><span class="sxs-lookup"><span data-stu-id="e8766-273">Deploy the app self-contained</span></span>

<span data-ttu-id="e8766-274">Nutzen Sie Visual Studio oder die .NET Core-CLI für eine [eigenständige Bereitstellung](/dotnet/core/deploying/#self-contained-deployments-scd) (Self-Contained Deployment, SCD).</span><span class="sxs-lookup"><span data-stu-id="e8766-274">Use Visual Studio or the .NET Core CLI for a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e8766-275">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e8766-275">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="e8766-276">Klicken Sie in der Visual Studio-Symbolleiste auf **Erstellen** >  **{Anwendungsname} veröffentlichen**, oder klicken Sie mit der rechten Maustaste auf den **Projektmappen-Explorer**, und klicken Sie dann auf **Veröffentlichen**.</span><span class="sxs-lookup"><span data-stu-id="e8766-276">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar or right-click the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="e8766-277">Vergewissern Sie sich im Dialogfeld **Veröffentlichungsziel auswählen**, dass **App Service** ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="e8766-277">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="e8766-278">Wählen Sie **Erweitert** aus.</span><span class="sxs-lookup"><span data-stu-id="e8766-278">Select **Advanced**.</span></span> <span data-ttu-id="e8766-279">Das Dialogfeld **Veröffentlichen** wird geöffnet.</span><span class="sxs-lookup"><span data-stu-id="e8766-279">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="e8766-280">Führen Sie im Dialogfeld **Veröffentlichen** folgende Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="e8766-280">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="e8766-281">Überprüfen Sie, ob die Konfiguration **Release** ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="e8766-281">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="e8766-282">Öffnen Sie die Dropdownliste **Bereitstellungsmodus**, und wählen Sie **Eigenständig** aus.</span><span class="sxs-lookup"><span data-stu-id="e8766-282">Open the **Deployment Mode** drop-down list and select **Self-Contained**.</span></span>
   * <span data-ttu-id="e8766-283">Wählen Sie die Zielruntime in der Dropdownliste **Zielruntime** aus.</span><span class="sxs-lookup"><span data-stu-id="e8766-283">Select the target runtime from the **Target Runtime** drop-down list.</span></span> <span data-ttu-id="e8766-284">Der Standardwert ist `win-x86`.</span><span class="sxs-lookup"><span data-stu-id="e8766-284">The default is `win-x86`.</span></span>
   * <span data-ttu-id="e8766-285">Wenn Sie zusätzliche Dateien bei der Bereitstellung entfernen müssen, öffnen Sie **Dateiveröffentlichungsoptionen**, und aktivieren Sie das Kontrollkästchen, um zusätzliche Dateien am Ziel zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="e8766-285">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="e8766-286">Klicken Sie auf **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="e8766-286">Select **Save**.</span></span>
1. <span data-ttu-id="e8766-287">Sie können eine neue Website erstellen oder eine vorhandene aktualisieren, indem Sie die folgenden Anweisungen des Veröffentlichungs-Assistenten befolgen.</span><span class="sxs-lookup"><span data-stu-id="e8766-287">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="e8766-288">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="e8766-288">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="e8766-289">Geben Sie in der Projektdatei eine oder mehrere [Runtimebezeichner (RIDs)](/dotnet/core/rid-catalog) an.</span><span class="sxs-lookup"><span data-stu-id="e8766-289">In the project file, specify one or more [Runtime Identifiers (RIDs)](/dotnet/core/rid-catalog).</span></span> <span data-ttu-id="e8766-290">Verwenden Sie die Singularform `<RuntimeIdentifier>` für eine einzelne RID oder die Pluralform `<RuntimeIdentifiers>`, um eine durch Semikolons getrennte Liste von RIDs bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="e8766-290">Use `<RuntimeIdentifier>` (singular) for a single RID, or use `<RuntimeIdentifiers>` (plural) to provide a semicolon-delimited list of RIDs.</span></span> <span data-ttu-id="e8766-291">Im folgenden Beispiel wird die RID `win-x86` angegeben:</span><span class="sxs-lookup"><span data-stu-id="e8766-291">In the following example, the `win-x86` RID is specified:</span></span>

   ```xml
   <PropertyGroup>
     <TargetFramework>{TARGET FRAMEWORK}</TargetFramework>
     <RuntimeIdentifier>win-x86</RuntimeIdentifier>
   </PropertyGroup>
   ```

1. <span data-ttu-id="e8766-292">Veröffentlichen Sie aus einer Befehlsshell die App in der Releasekonfiguration für die Runtime des Hosts mit dem Befehl [dotnet publish](/dotnet/core/tools/dotnet-publish).</span><span class="sxs-lookup"><span data-stu-id="e8766-292">From a command shell, publish the app in Release configuration for the host's runtime with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="e8766-293">Im folgenden Beispiel wird die App für die RID `win-x86` veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="e8766-293">In the following example, the app is published for the `win-x86` RID.</span></span> <span data-ttu-id="e8766-294">Die für die Option `--runtime` angegebene RID muss in der Eigenschaft `<RuntimeIdentifier>` (oder `<RuntimeIdentifiers>`) in der Projektdatei angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="e8766-294">The RID supplied to the `--runtime` option must be provided in the `<RuntimeIdentifier>` (or `<RuntimeIdentifiers>`) property in the project file.</span></span>

   ```console
   dotnet publish --configuration Release --runtime win-x86 --self-contained
   ```

1. <span data-ttu-id="e8766-295">Verschieben Sie den Inhalt des Verzeichnisses *bin/Release/{ZIELFRAMEWORK}/{RUNTIME-ID}/publish* auf die Site in App Service.</span><span class="sxs-lookup"><span data-stu-id="e8766-295">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* directory to the site in App Service.</span></span> <span data-ttu-id="e8766-296">Wenn Sie die Inhalte des Ordners *publish* von Ihrer lokalen Festplatte oder Netzwerkfreigabe direkt über die Kudu-Konsole in App Service verschieben möchten, ziehen Sie die Dateien in den Ordner `D:\home\site\wwwroot` in der Kudu-Konsole.</span><span class="sxs-lookup"><span data-stu-id="e8766-296">If dragging the *publish* folder contents from your local hard drive or network share directly to App Service in the Kudu console, drag the files to the `D:\home\site\wwwroot` folder in the Kudu console.</span></span>

---

## <a name="protocol-settings-https"></a><span data-ttu-id="e8766-297">Protokolleinstellungen (HTTPS)</span><span class="sxs-lookup"><span data-stu-id="e8766-297">Protocol settings (HTTPS)</span></span>

<span data-ttu-id="e8766-298">Durch sichere Protokollbindungen können Sie ein Zertifikat festlegen, das verwendet werden soll, wenn über HTTPS auf Anforderungen geantwortet wird.</span><span class="sxs-lookup"><span data-stu-id="e8766-298">Secure protocol bindings allow you specify a certificate to use when responding to requests over HTTPS.</span></span> <span data-ttu-id="e8766-299">Bindungen erfordern gültige private Zertifikate ( *.pfx*), die für den angegebenen Hostnamen ausgestellt wurden.</span><span class="sxs-lookup"><span data-stu-id="e8766-299">Binding requires a valid private certificate (*.pfx*) issued for the specific hostname.</span></span> <span data-ttu-id="e8766-300">Weitere Informationen finden Sie unter [Tutorial: Binden eines vorhandenen benutzerdefinierten SSL-Zertifikats an Azure App Service](/azure/app-service/app-service-web-tutorial-custom-ssl).</span><span class="sxs-lookup"><span data-stu-id="e8766-300">For more information, see [Tutorial: Bind an existing custom SSL certificate to Azure App Service](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

## <a name="transform-webconfig"></a><span data-ttu-id="e8766-301">Transformieren von web.config</span><span class="sxs-lookup"><span data-stu-id="e8766-301">Transform web.config</span></span>

<span data-ttu-id="e8766-302">Wenn Sie *web.config* beim Veröffentlichen transformieren müssen (z.B. Umgebungsvariablen basierend auf der Konfiguration, dem Profil oder der Umgebung festlegen müssen), finden Sie weitere Informationen unter <xref:host-and-deploy/iis/transform-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="e8766-302">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e8766-303">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="e8766-303">Additional resources</span></span>

* [<span data-ttu-id="e8766-304">App Service-Übersicht</span><span class="sxs-lookup"><span data-stu-id="e8766-304">App Service overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="e8766-305">Azure App Service: The Best Place to Host your .NET Apps (Azure App Service: Ideal zum Hosten von .NET-Apps; 55-minütiges Video)</span><span class="sxs-lookup"><span data-stu-id="e8766-305">Azure App Service: The Best Place to Host your .NET Apps (55-minute overview video)</span></span>](https://channel9.msdn.com/events/dotnetConf/2017/T222)
* [<span data-ttu-id="e8766-306">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (Azure Friday: Azure App Service-Diagnose und -Fehlerbehebung; zwölfminütiges Video)</span><span class="sxs-lookup"><span data-stu-id="e8766-306">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
* [<span data-ttu-id="e8766-307">Übersicht: Azure App Service-Diagnose</span><span class="sxs-lookup"><span data-stu-id="e8766-307">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* <xref:host-and-deploy/web-farm>

<span data-ttu-id="e8766-308">Azure App Service auf Windows Server verwendet [Internetinformationsdienste (IIS)](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="e8766-308">Azure App Service on Windows Server uses [Internet Information Services (IIS)](https://www.iis.net/).</span></span> <span data-ttu-id="e8766-309">Die folgenden Artikel gehen auf die zugrunde liegende IIS-Technologie ein:</span><span class="sxs-lookup"><span data-stu-id="e8766-309">The following topics pertain to the underlying IIS technology:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>
* [<span data-ttu-id="e8766-310">Windows Server – IT-Administrator-Inhalt für aktuelle und frühere Versionen</span><span class="sxs-lookup"><span data-stu-id="e8766-310">Windows Server - IT administrator content for current and previous releases</span></span>](/windows-server/windows-server-versions)

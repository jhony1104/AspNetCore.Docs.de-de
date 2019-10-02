---
title: Bereitstellen von ASP.NET Core-Apps in Azure App Service
author: guardrex
description: Dieser Artikel enthält Links zu Azure-Host- und Bereitstellungsressourcen.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/28/2019
uid: host-and-deploy/azure-apps/index
ms.openlocfilehash: 7489868fac513948cbe6f48391e7260a34b2175e
ms.sourcegitcommit: dc96d76f6b231de59586fcbb989a7fb5106d26a8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703753"
---
# <a name="deploy-aspnet-core-apps-to-azure-app-service"></a><span data-ttu-id="9f436-103">Bereitstellen von ASP.NET Core-Apps in Azure App Service</span><span class="sxs-lookup"><span data-stu-id="9f436-103">Deploy ASP.NET Core apps to Azure App Service</span></span>

<span data-ttu-id="9f436-104">[Azure App Service](https://azure.microsoft.com/services/app-service/) ist ein [Microsoft Cloud Computing-Plattformdienst](https://azure.microsoft.com/) zum Hosten von Web-Apps. Dazu gehört auch ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9f436-104">[Azure App Service](https://azure.microsoft.com/services/app-service/) is a [Microsoft cloud computing platform service](https://azure.microsoft.com/) for hosting web apps, including ASP.NET Core.</span></span>

## <a name="useful-resources"></a><span data-ttu-id="9f436-105">Nützliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="9f436-105">Useful resources</span></span>

<span data-ttu-id="9f436-106">In der [App Service-Dokumentation](/azure/app-service/) finden Sie die Azure-Apps-Dokumentation, Tutorials, Beispiele und Leitfäden sowie andere Ressourcen.</span><span class="sxs-lookup"><span data-stu-id="9f436-106">[App Service Documentation](/azure/app-service/) is the home for Azure Apps documentation, tutorials, samples, how-to guides, and other resources.</span></span> <span data-ttu-id="9f436-107">Dies sind zwei erwähnenswerte Tutorials, die auf das Hosten von ASP.NET Core-Apps eingehen:</span><span class="sxs-lookup"><span data-stu-id="9f436-107">Two notable tutorials that pertain to hosting ASP.NET Core apps are:</span></span>

[<span data-ttu-id="9f436-108">Erstellen einer ASP.NET Core-Web-App in Azure</span><span class="sxs-lookup"><span data-stu-id="9f436-108">Create an ASP.NET Core web app in Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet)  
<span data-ttu-id="9f436-109">Verwenden Sie Visual Studio, um ASP.NET Core-Web-Apps zu erstellen und in Azure App Service unter Windows bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="9f436-109">Use Visual Studio to create and deploy an ASP.NET Core web app to Azure App Service on Windows.</span></span>

[<span data-ttu-id="9f436-110">Erstellen einer .NET Core-App in App Service für Linux</span><span class="sxs-lookup"><span data-stu-id="9f436-110">Create an ASP.NET Core app in App Service on Linux</span></span>](/azure/app-service/containers/quickstart-dotnetcore)  
<span data-ttu-id="9f436-111">Verwenden Sie die Befehlszeile, um ASP.NET Core-Web-Apps zu erstellen und in Azure App Service unter Linux bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="9f436-111">Use the command line to create and deploy an ASP.NET Core web app to Azure App Service on Linux.</span></span>

<span data-ttu-id="9f436-112">Die ASP.NET Core-Version, die unter Azure App Service verfügbar ist, finden Sie unter [ASP.NET Core im App Service-Dashboard](https://aspnetcoreon.azurewebsites.net/).</span><span class="sxs-lookup"><span data-stu-id="9f436-112">See the [ASP.NET Core on App Service Dashboard](https://aspnetcoreon.azurewebsites.net/) for the version of ASP.NET Core available on Azure App service.</span></span>

<span data-ttu-id="9f436-113">Die folgenden Artikel sind in der ASP.NET Core-Dokumentation verfügbar:</span><span class="sxs-lookup"><span data-stu-id="9f436-113">The following articles are available in ASP.NET Core documentation:</span></span>

<xref:tutorials/publish-to-azure-webapp-using-vs>  
<span data-ttu-id="9f436-114">Erfahren Sie, wie eine ASP.NET Core-App in Azure App Service mit Visual Studio veröffentlicht wird.</span><span class="sxs-lookup"><span data-stu-id="9f436-114">Learn how to publish an ASP.NET Core app to Azure App Service using Visual Studio.</span></span>

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>  
<span data-ttu-id="9f436-115">Erfahren Sie, wie Sie mit Visual Studio eine ASP.NET Core-Web-App erstellen und sie unter Verwendung von Git für Continuous Deployment in Azure App Service bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="9f436-115">Learn how to create an ASP.NET Core web app using Visual Studio and deploy it to Azure App Service using Git for continuous deployment.</span></span>

[<span data-ttu-id="9f436-116">Erstellen Sie Ihre erste Pipeline</span><span class="sxs-lookup"><span data-stu-id="9f436-116">Create your first pipeline</span></span>](/azure/devops/pipelines/get-started-yaml)  
<span data-ttu-id="9f436-117">Richten Sie ein CI-Build für eine ASP.NET Core-App ein, und erstellen Sie dann ein Continuous Deployment-Release für Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="9f436-117">Set up a CI build for an ASP.NET Core app, then create a continuous deployment release to Azure App Service.</span></span>

[<span data-ttu-id="9f436-118">Azure Web App-Sandbox</span><span class="sxs-lookup"><span data-stu-id="9f436-118">Azure Web App sandbox</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)  
<span data-ttu-id="9f436-119">Entdecken Sie die Einschränkungen der Azure App Service-Laufzeitausführung, die durch die Azure Apps-Plattform erzwungen werden.</span><span class="sxs-lookup"><span data-stu-id="9f436-119">Discover Azure App Service runtime execution limitations enforced by the Azure Apps platform.</span></span>

<xref:test/troubleshoot>  
<span data-ttu-id="9f436-120">In diesem Artikel werden Warnungen und Fehler erläutert. Außerdem erfahren Sie, wie die Problembehandlung in ASP.NET Core-Projekten funktioniert.</span><span class="sxs-lookup"><span data-stu-id="9f436-120">Understand and troubleshoot warnings and errors with ASP.NET Core projects.</span></span>

## <a name="application-configuration"></a><span data-ttu-id="9f436-121">Anwendungskonfiguration</span><span class="sxs-lookup"><span data-stu-id="9f436-121">Application configuration</span></span>

### <a name="platform"></a><span data-ttu-id="9f436-122">Plattform</span><span class="sxs-lookup"><span data-stu-id="9f436-122">Platform</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="9f436-123">In Azure App Service sind Runtimes für 64-Bit- und 32-Bit-Apps (x64 und x86) vorhanden.</span><span class="sxs-lookup"><span data-stu-id="9f436-123">Runtimes for 64-bit (x64) and 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="9f436-124">Das [.NET Core SDK](/dotnet/core/sdk), das in Azure App Service zur Verfügung steht, ist eine 32-Bit-Version. Sie können jedoch auch lokal erstellte 64-Bit-Apps mit der [Kudu](https://github.com/projectkudu/kudu/wiki)-Konsole oder dem Veröffentlichungsprozess in Visual Studio bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="9f436-124">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit, but you can deploy 64-bit apps built locally using the [Kudu](https://github.com/projectkudu/kudu/wiki) console or the publish process in Visual Studio.</span></span> <span data-ttu-id="9f436-125">Weitere Informationen finden Sie im Abschnitt [Publish and deploy the app (Veröffentlichen und Bereitstellen der App)](#publish-and-deploy-the-app).</span><span class="sxs-lookup"><span data-stu-id="9f436-125">For more information, see the [Publish and deploy the app](#publish-and-deploy-the-app) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="9f436-126">Für Apps mit nativen Abhängigkeiten stehen in Azure App Service Runtimes für 32-Bit- und 32-Bit-Apps (x86 und x86) zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="9f436-126">For apps with native dependencies, runtimes for 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="9f436-127">Das [.NET Core SDK](/dotnet/core/sdk) in Azure App Service ist eine 32-Bit-Version.</span><span class="sxs-lookup"><span data-stu-id="9f436-127">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit.</span></span>

::: moniker-end

<span data-ttu-id="9f436-128">Weitere Informationen zu .NET Core-Frameworkkomponenten und Verteilungsmethoden (z. B. .NET Core-Runtime und .NET Core SDK) finden Sie unter [Weitere Informationen zu .NET Core: Aufbau](/dotnet/core/about#composition).</span><span class="sxs-lookup"><span data-stu-id="9f436-128">For more information on .NET Core framework components and distribution methods, such as information on the .NET Core runtime and the .NET Core SDK, see [About .NET Core: Composition](/dotnet/core/about#composition).</span></span>

### <a name="packages"></a><span data-ttu-id="9f436-129">Pakete</span><span class="sxs-lookup"><span data-stu-id="9f436-129">Packages</span></span>

<span data-ttu-id="9f436-130">Beziehen Sie die folgenden NuGet-Pakete ein, die automatische Protokollierungsfeatures für Apps bieten, die für Azure App Service bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="9f436-130">Include the following NuGet packages to provide automatic logging features for apps deployed to Azure App Service:</span></span>

* <span data-ttu-id="9f436-131">[Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) verwendet [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration), um die ASP.NET Core-Lightup-Integration mit Azure App Service bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="9f436-131">[Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) uses [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) to provide ASP.NET Core light-up integration with Azure App Service.</span></span> <span data-ttu-id="9f436-132">Die hinzugefügten Protokollierungsfeatures werden vom `Microsoft.AspNetCore.AzureAppServicesIntegration`-Paket bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="9f436-132">The added logging features are provided by the `Microsoft.AspNetCore.AzureAppServicesIntegration` package.</span></span>
* <span data-ttu-id="9f436-133">[Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) führt [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) aus, um Anbieter für die Azure App Service-Diagnoseprotokollierung zum Paket `Microsoft.Extensions.Logging.AzureAppServices` hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="9f436-133">[Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) executes [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) to add Azure App Service diagnostics logging providers in the `Microsoft.Extensions.Logging.AzureAppServices` package.</span></span>
* <span data-ttu-id="9f436-134">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) stellt Protokollierungsimplementierungen bereit, um die Azure App Service-Features für Diagnoseprotokolle und Protokollstreaming zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="9f436-134">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) provides logger implementations to support Azure App Service diagnostics logs and log streaming features.</span></span>

<span data-ttu-id="9f436-135">Die Metapakete [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) sind nicht in den vorherigen Paketen enthalten.</span><span class="sxs-lookup"><span data-stu-id="9f436-135">The preceding packages aren't available from the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="9f436-136">Apps, deren Ziel das .NET Framework ist oder die auf das `Microsoft.AspNetCore.App`-Metapaket verweisen, müssen explizit auf die einzelnen Pakete in der Projektdatei der App verweisen.</span><span class="sxs-lookup"><span data-stu-id="9f436-136">Apps that target .NET Framework or reference the `Microsoft.AspNetCore.App` metapackage must explicitly reference the individual packages in the app's project file.</span></span>

## <a name="override-app-configuration-using-the-azure-portal"></a><span data-ttu-id="9f436-137">Überschreiben der App-Konfiguration im Azure-Portal</span><span class="sxs-lookup"><span data-stu-id="9f436-137">Override app configuration using the Azure Portal</span></span>

<span data-ttu-id="9f436-138">Über die App-Einstellungen im Azure-Portal können Sie Umgebungsvariablen für die App festlegen.</span><span class="sxs-lookup"><span data-stu-id="9f436-138">App settings in the Azure Portal permit you to set environment variables for the app.</span></span> <span data-ttu-id="9f436-139">Umgebungsvariablen können von [Umgebungsvariablen-Konfigurationsanbietern](xref:fundamentals/configuration/index#environment-variables-configuration-provider) verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="9f436-139">Environment variables can be consumed by the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

<span data-ttu-id="9f436-140">Wenn eine App-Einstellung im Azure-Portal erstellt oder geändert und die Schaltfläche **Speichern** ausgewählt wird, wird die Azure-App neu gestartet.</span><span class="sxs-lookup"><span data-stu-id="9f436-140">When an app setting is created or modified in the Azure Portal and the **Save** button is selected, the Azure App is restarted.</span></span> <span data-ttu-id="9f436-141">Die Umgebungsvariable steht der App nach dem Neustart des Diensts zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="9f436-141">The environment variable is available to the app after the service restarts.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9f436-142">Wenn eine App den [generischen Host](xref:fundamentals/host/generic-host) verwendet, werden Umgebungsvariablen nicht standardmäßig in die App-Konfiguration geladen, und der Konfigurationsanbieter muss vom Entwickler hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="9f436-142">When an app uses the [Generic Host](xref:fundamentals/host/generic-host), environment variables aren't loaded into an app's configuration by default and the configuration provider must be added by the developer.</span></span> <span data-ttu-id="9f436-143">Der Entwickler legt das Präfix der Umgebungsvariablen fest, wenn der Konfigurationsanbieter hinzugefügt wird.</span><span class="sxs-lookup"><span data-stu-id="9f436-143">The developer determines the environment variable prefix when the configuration provider is added.</span></span> <span data-ttu-id="9f436-144">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host> und unter [Umgebungsvariablen-Konfigurationsanbieter](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="9f436-144">For more information, see <xref:fundamentals/host/generic-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9f436-145">Wenn eine App den Host mit [WebHost.CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) erstellt, verwenden Umgebungsvariablen, die den Host konfigurieren, das Präfix `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="9f436-145">When an app builds the host using [WebHost.CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder), environment variables that configure the host use the `ASPNETCORE_` prefix.</span></span> <span data-ttu-id="9f436-146">Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host> und unter [Umgebungsvariablen-Konfigurationsanbieter](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="9f436-146">For more information, see <xref:fundamentals/host/web-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="9f436-147">Proxyserver und Lastenausgleichsszenarien</span><span class="sxs-lookup"><span data-stu-id="9f436-147">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="9f436-148">Die Middleware für die [Integration von IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), die beim [Out-of-Process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)-Hosting die Middleware für weitergeleitete Header konfiguriert, und das ASP.NET Core-Modul sind so konfiguriert, dass sie das Schema (HTTP/HTTPS) und die Remote-IP-Adresse an die Stelle weiterleiten, von der die Anforderung stammte.</span><span class="sxs-lookup"><span data-stu-id="9f436-148">The [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), which configures Forwarded Headers Middleware when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="9f436-149">Möglicherweise ist zusätzliche Konfiguration für Apps erforderlich, die hinter weiteren Proxyservern und Lastenausgleichsmodulen (Load Balancer) gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="9f436-149">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="9f436-150">Weitere Informationen hierzu feinden Sie unter [Konfigurieren von ASP.NET Core zur Verwendung mit Proxyservern und Lastenausgleich](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="9f436-150">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="monitoring-and-logging"></a><span data-ttu-id="9f436-151">Überwachung und Protokollierung</span><span class="sxs-lookup"><span data-stu-id="9f436-151">Monitoring and logging</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9f436-152">In App Service bereitgestellte ASP.NET Core-Apps erhalten automatisch die App Service-Erweiterung **ASP.NET Core Logging Integration** (ASP.NET Core-Protokollierungsintegration).</span><span class="sxs-lookup"><span data-stu-id="9f436-152">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Integration**.</span></span> <span data-ttu-id="9f436-153">Durch diese Erweiterung ist die Protokollierungsintegration für ASP.NET Core-Apps in Azure App Service möglich.</span><span class="sxs-lookup"><span data-stu-id="9f436-153">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9f436-154">In App Service bereitgestellte ASP.NET Core-Apps erhalten automatisch die App Service-Erweiterung **ASP.NET Core-Protokollierungserweiterungen**.</span><span class="sxs-lookup"><span data-stu-id="9f436-154">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Extensions**.</span></span> <span data-ttu-id="9f436-155">Durch diese Erweiterung ist die Protokollierungsintegration für ASP.NET Core-Apps in Azure App Service möglich.</span><span class="sxs-lookup"><span data-stu-id="9f436-155">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span></span>

::: moniker-end

<span data-ttu-id="9f436-156">In den folgenden Artikeln finden Sie Informationen zum Überwachen, Protokollieren und zur Problembehandlung:</span><span class="sxs-lookup"><span data-stu-id="9f436-156">For monitoring, logging, and troubleshooting information, see the following articles:</span></span>

[<span data-ttu-id="9f436-157">Überwachen von Apps in Azure App Service</span><span class="sxs-lookup"><span data-stu-id="9f436-157">Monitor apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)  
<span data-ttu-id="9f436-158">Erfahren Sie, wie Sie Kontingente und Metrik für Apps und App Service-Pläne prüfen.</span><span class="sxs-lookup"><span data-stu-id="9f436-158">Learn how to review quotas and metrics for apps and App Service plans.</span></span>

[<span data-ttu-id="9f436-159">Aktivieren der Diagnoseprotokollierung für Apps in Azure App Service</span><span class="sxs-lookup"><span data-stu-id="9f436-159">Enable diagnostics logging for apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)  
<span data-ttu-id="9f436-160">Erfahren Sie, wie Sie die Diagnosesprotokollierung für HTTP-Statuscodes, fehlgeschlagene Anforderungen und Webserveraktivitäten aktivieren und auf die Protokolle zugreifen.</span><span class="sxs-lookup"><span data-stu-id="9f436-160">Discover how to enable and access diagnostic logging for HTTP status codes, failed requests, and web server activity.</span></span>

<xref:fundamentals/error-handling>  
<span data-ttu-id="9f436-161">Lernen Sie gängige Methoden zur Fehlerbehandlung in ASP.NET Core-Apps kennen.</span><span class="sxs-lookup"><span data-stu-id="9f436-161">Understand common approaches to handling errors in ASP.NET Core apps.</span></span>

<xref:test/troubleshoot-azure-iis>  
<span data-ttu-id="9f436-162">Erfahren Sie, wie Sie Probleme mit Azure App Service-Bereitstellungen mit ASP.NET Core-Apps diagnostizieren können.</span><span class="sxs-lookup"><span data-stu-id="9f436-162">Learn how to diagnose issues with Azure App Service deployments with ASP.NET Core apps.</span></span>

<xref:host-and-deploy/azure-iis-errors-reference>  
<span data-ttu-id="9f436-163">Sehen Sie sich häufig auftretende Fehler und entsprechende Hinweise zur Fehlerbehebung bei der Bereitstellungskonfiguration für von Azure App Service/IIS gehosteten Apps an.</span><span class="sxs-lookup"><span data-stu-id="9f436-163">See the common deployment configuration errors for apps hosted by Azure App Service/IIS with troubleshooting advice.</span></span>

## <a name="data-protection-key-ring-and-deployment-slots"></a><span data-ttu-id="9f436-164">Data Protection-Schlüsselbund und -Bereitstellungsslots</span><span class="sxs-lookup"><span data-stu-id="9f436-164">Data Protection key ring and deployment slots</span></span>

<span data-ttu-id="9f436-165">[Data Protection-Schlüssel](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) werden im Ordner *%HOME%\ASP.NET\DataProtection-Keys* gespeichert.</span><span class="sxs-lookup"><span data-stu-id="9f436-165">[Data Protection keys](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) are persisted to the *%HOME%\ASP.NET\DataProtection-Keys* folder.</span></span> <span data-ttu-id="9f436-166">Dieser Ordner wird von einem Netzwerkspeicher unterstützt und mit allen Computern, auf denen die App gehostet wird, synchronisiert.</span><span class="sxs-lookup"><span data-stu-id="9f436-166">This folder is backed by network storage and is synchronized across all machines hosting the app.</span></span> <span data-ttu-id="9f436-167">Ruhende Schlüssel werden nicht geschützt.</span><span class="sxs-lookup"><span data-stu-id="9f436-167">Keys aren't protected at rest.</span></span> <span data-ttu-id="9f436-168">Dieser Ordner stellt den Schlüsselbund für alle Instanzen einer App in einem einzelnen Bereitstellungsslot bereit.</span><span class="sxs-lookup"><span data-stu-id="9f436-168">This folder supplies the key ring to all instances of an app in a single deployment slot.</span></span> <span data-ttu-id="9f436-169">Separate Bereitstellungsslots, wie Staging und Produktion, verwenden keinen gemeinsamen Schlüsselbund.</span><span class="sxs-lookup"><span data-stu-id="9f436-169">Separate deployment slots, such as Staging and Production, don't share a key ring.</span></span>

<span data-ttu-id="9f436-170">Wenn Sie zwischen Bereitstellungsslots wechseln, können Systeme, die Data Protection verwenden, gespeicherte Daten nicht mit dem Schlüsselbund des vorherigen Slots entschlüsseln.</span><span class="sxs-lookup"><span data-stu-id="9f436-170">When swapping between deployment slots, any system using data protection won't be able to decrypt stored data using the key ring inside the previous slot.</span></span> <span data-ttu-id="9f436-171">ASP.NET-Cookiemiddleware verwendet Data Protection zum Schutz ihrer Cookies.</span><span class="sxs-lookup"><span data-stu-id="9f436-171">ASP.NET Cookie Middleware uses data protection to protect its cookies.</span></span> <span data-ttu-id="9f436-172">Dies führt dazu, dass Benutzer in einer App abgemeldet werden, die Standard-ASP.NET-Cookiemiddleware verwendet.</span><span class="sxs-lookup"><span data-stu-id="9f436-172">This leads to users being signed out of an app that uses the standard ASP.NET Cookie Middleware.</span></span> <span data-ttu-id="9f436-173">Verwende Sie einen externen Schlüsselbundanbieter für eine slotunabhängige Schlüsselbundlösung wie z.B.:</span><span class="sxs-lookup"><span data-stu-id="9f436-173">For a slot-independent key ring solution, use an external key ring provider, such as:</span></span>

* <span data-ttu-id="9f436-174">Azure Blob Storage</span><span class="sxs-lookup"><span data-stu-id="9f436-174">Azure Blob Storage</span></span>
* <span data-ttu-id="9f436-175">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="9f436-175">Azure Key Vault</span></span>
* <span data-ttu-id="9f436-176">SQL-Speicher</span><span class="sxs-lookup"><span data-stu-id="9f436-176">SQL store</span></span>
* <span data-ttu-id="9f436-177">Redis Cache</span><span class="sxs-lookup"><span data-stu-id="9f436-177">Redis cache</span></span>

<span data-ttu-id="9f436-178">Weitere Informationen finden Sie unter <xref:security/data-protection/implementation/key-storage-providers>.</span><span class="sxs-lookup"><span data-stu-id="9f436-178">For more information, see <xref:security/data-protection/implementation/key-storage-providers>.</span></span>
<a name="deploy-aspnet-core-preview-release-to-azure-app-service"></a>
<!-- revert this after 3.0 supported
## Deploy ASP.NET Core preview release to Azure App Service

Use one of the following approaches if the app relies on a preview release of .NET Core:

* [Install the preview site extension](#install-the-preview-site-extension).
* [Deploy a self-contained preview app](#deploy-a-self-contained-preview-app).
* [Use Docker with Web Apps for containers](#use-docker-with-web-apps-for-containers).
-->
## <a name="deploy-aspnet-core-30-to-azure-app-service"></a><span data-ttu-id="9f436-179">Bereitstellen von ASP.NET Core 3.0 in Azure App Service</span><span class="sxs-lookup"><span data-stu-id="9f436-179">Deploy ASP.NET Core 3.0 to Azure App Service</span></span>

<span data-ttu-id="9f436-180">Wir hoffen, dass wir ASP.NET Core 3.0 bald in Azure App Service anbieten können.</span><span class="sxs-lookup"><span data-stu-id="9f436-180">We hope to have ASP.NET Core 3.0 available on Azure App Service soon.</span></span>

<span data-ttu-id="9f436-181">Wenden Sie einen der folgenden Ansätze an, wenn die App von .NET Core 3.0 abhängig ist:</span><span class="sxs-lookup"><span data-stu-id="9f436-181">Use one of the following approaches if the app relies on .NET Core 3.0:</span></span>

* <span data-ttu-id="9f436-182">[Installieren der Websiteerweiterung (Vorschau)](#install-the-preview-site-extension).</span><span class="sxs-lookup"><span data-stu-id="9f436-182">[Install the preview site extension](#install-the-preview-site-extension).</span></span>
* <span data-ttu-id="9f436-183">[Bereitstellen einer eigenständigen Vorschau-App](#deploy-a-self-contained-preview-app)</span><span class="sxs-lookup"><span data-stu-id="9f436-183">[Deploy a self-contained preview app](#deploy-a-self-contained-preview-app).</span></span>
* <span data-ttu-id="9f436-184">[Verwenden von Docker mit Web-Apps für Container](#use-docker-with-web-apps-for-containers).</span><span class="sxs-lookup"><span data-stu-id="9f436-184">[Use Docker with Web Apps for containers](#use-docker-with-web-apps-for-containers).</span></span>

### <a name="install-the-preview-site-extension"></a><span data-ttu-id="9f436-185">Installieren der Websiteerweiterung (Vorschau)</span><span class="sxs-lookup"><span data-stu-id="9f436-185">Install the preview site extension</span></span>

<span data-ttu-id="9f436-186">Sollte ein Problem mit dem Verwenden der Vorschau der Websiteerweiterung auftreten, öffnen Sie ein [aspnet/AspNetCore-Issue](https://github.com/aspnet/AspNetCore/issues).</span><span class="sxs-lookup"><span data-stu-id="9f436-186">If a problem occurs using the preview site extension, open an [aspnet/AspNetCore issue](https://github.com/aspnet/AspNetCore/issues).</span></span>

1. <span data-ttu-id="9f436-187">Navigieren Sie im Azure-Portal zu „App Service“.</span><span class="sxs-lookup"><span data-stu-id="9f436-187">From the Azure Portal, navigate to the App Service.</span></span>
1. <span data-ttu-id="9f436-188">Wählen Sie die Web-App aus.</span><span class="sxs-lookup"><span data-stu-id="9f436-188">Select the web app.</span></span>
1. <span data-ttu-id="9f436-189">Geben Sie „ex“ in das Suchfeld ein, um nach „Extensions“ (Erweiterungen) zu filtern, oder durchsuchen Sie die Liste der Verwaltungstools.</span><span class="sxs-lookup"><span data-stu-id="9f436-189">Type "ex" in the search box to filter for "Extensions" or scroll down the list of management tools.</span></span>
1. <span data-ttu-id="9f436-190">Wählen Sie **Erweiterungen** aus.</span><span class="sxs-lookup"><span data-stu-id="9f436-190">Select **Extensions**.</span></span>
1. <span data-ttu-id="9f436-191">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="9f436-191">Select **Add**.</span></span>
1. <span data-ttu-id="9f436-192">Wählen Sie die Erweiterung **ASP.NET Core {X.Y} ({x64|x86}) Runtime** aus der Liste aus. Dabei ist `{X.Y}` die ASP.NET Core-Vorschauversion, und `{x64|x86}` gibt die Plattform an.</span><span class="sxs-lookup"><span data-stu-id="9f436-192">Select the **ASP.NET Core {X.Y} ({x64|x86}) Runtime** extension from the list, where `{X.Y}` is the ASP.NET Core preview version and `{x64|x86}` specifies the platform.</span></span>
1. <span data-ttu-id="9f436-193">Klicken Sie auf **OK**, um die rechtlichen Bedingungen zu akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="9f436-193">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="9f436-194">Wählen Sie **OK** aus, um die Erweiterung zu installieren.</span><span class="sxs-lookup"><span data-stu-id="9f436-194">Select **OK** to install the extension.</span></span>

<span data-ttu-id="9f436-195">Nach Abschluss dieses Vorgangs wird die neueste .NET Core-Vorschauversion installiert.</span><span class="sxs-lookup"><span data-stu-id="9f436-195">When the operation completes, the latest .NET Core preview is installed.</span></span> <span data-ttu-id="9f436-196">Überprüfen Sie die Installation:</span><span class="sxs-lookup"><span data-stu-id="9f436-196">Verify the installation:</span></span>

1. <span data-ttu-id="9f436-197">Wählen Sie **Erweiterte Tools** aus.</span><span class="sxs-lookup"><span data-stu-id="9f436-197">Select **Advanced Tools**.</span></span>
1. <span data-ttu-id="9f436-198">Wählen Sie **Go** unter **Erweiterte Tools** aus.</span><span class="sxs-lookup"><span data-stu-id="9f436-198">Select **Go** in **Advanced Tools**.</span></span>
1. <span data-ttu-id="9f436-199">Wählen Sie das Menüelement **Debugkonsole** > **PowerShell** aus.</span><span class="sxs-lookup"><span data-stu-id="9f436-199">Select the **Debug console** > **PowerShell** menu item.</span></span>
1. <span data-ttu-id="9f436-200">Führen Sie in der PowerShell-Eingabeaufforderung den folgenden Befehl aus.</span><span class="sxs-lookup"><span data-stu-id="9f436-200">At the PowerShell prompt, execute the following command.</span></span> <span data-ttu-id="9f436-201">Ersetzen Sie im Befehl die ASP.NET Core-Runtimeversion durch `{X.Y}` und die Plattform durch `{PLATFORM}`:</span><span class="sxs-lookup"><span data-stu-id="9f436-201">Substitute the ASP.NET Core runtime version for `{X.Y}` and the platform for `{PLATFORM}` in the command:</span></span>

   ```powershell
   Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.{PLATFORM}\
   ```

   <span data-ttu-id="9f436-202">Der Befehl gibt `True` zurück, wenn die x64-Vorschauruntime installiert ist.</span><span class="sxs-lookup"><span data-stu-id="9f436-202">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="9f436-203">Die Plattformarchitektur (x86/x64) einer App Services-App wird in den Einstellungen der App im Azure-Portal festgelegt. Dies gilt für Apps, die in einem A-Series-Computetarif oder einem höheren Hostingtarif gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="9f436-203">The platform architecture (x86/x64) of an App Services app is set in the app's settings in the Azure Portal for apps that are hosted on an A-series compute or better hosting tier.</span></span> <span data-ttu-id="9f436-204">Wenn die Anwendung im In-Process-Modus ausgeführt wird und die Plattformarchitektur für 64-Bit (x64) konfiguriert ist, verwendet das ASP.NET Core-Modul die 64-Bit-Vorschauruntime, falls vorhanden.</span><span class="sxs-lookup"><span data-stu-id="9f436-204">If the app is run in in-process mode and the platform architecture is configured for 64-bit (x64), the ASP.NET Core Module uses the 64-bit preview runtime, if present.</span></span> <span data-ttu-id="9f436-205">Installieren Sie die Erweiterung **ASP.NET Core {X.Y} (x64) Runtime**.</span><span class="sxs-lookup"><span data-stu-id="9f436-205">Install the **ASP.NET Core {X.Y} (x64) Runtime** extension.</span></span>
>
> <span data-ttu-id="9f436-206">Nach der Installation der x64-Vorschauruntime führen Sie den folgenden Befehl im Kudu PowerShell-Befehlsfenster aus, um die Installation zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="9f436-206">After installing the x64 preview runtime, run the following command in the Kudu PowerShell command window to verify the installation.</span></span> <span data-ttu-id="9f436-207">Ersetzen Sie im Befehl die ASP.NET Core-Runtimeversion für `{X.Y}`:</span><span class="sxs-lookup"><span data-stu-id="9f436-207">Substitute the ASP.NET Core runtime version for `{X.Y}` in the command:</span></span>
>
> ```powershell
> Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64\
> ```
>
> <span data-ttu-id="9f436-208">Der Befehl gibt `True` zurück, wenn die x64-Vorschauruntime installiert ist.</span><span class="sxs-lookup"><span data-stu-id="9f436-208">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="9f436-209">**ASP.NET Core-Erweiterungen** aktivieren zusätzliche Funktionen für ASP.NET Core in Azure App Services, z.B. Azure-Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="9f436-209">**ASP.NET Core Extensions** enables additional functionality for ASP.NET Core on Azure App Services, such as enabling Azure logging.</span></span> <span data-ttu-id="9f436-210">Die Erweiterung wird automatisch installiert, wenn die Bereitstellung aus Visual Studio erfolgt.</span><span class="sxs-lookup"><span data-stu-id="9f436-210">The extension is installed automatically when deploying from Visual Studio.</span></span> <span data-ttu-id="9f436-211">Wenn die Erweiterung nicht installiert ist, installieren Sie sie für die App.</span><span class="sxs-lookup"><span data-stu-id="9f436-211">If the extension isn't installed, install it for the app.</span></span>

<span data-ttu-id="9f436-212">**Verwenden der Vorschau-Websiteerweiterung mit einer ARM-Vorlage**</span><span class="sxs-lookup"><span data-stu-id="9f436-212">**Use the preview site extension with an ARM template**</span></span>

<span data-ttu-id="9f436-213">Wenn Sie eine ARM-Vorlage zum Erstellen und Bereitstellen von Anwendungen verwenden, können Sie den Ressourcentyp `siteextensions` verwenden, um die Websiteerweiterung zu einer Web-App hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="9f436-213">If an ARM template is used to create and deploy apps, the `siteextensions` resource type can be used to add the site extension to a web app.</span></span> <span data-ttu-id="9f436-214">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9f436-214">For example:</span></span>

[!code-json[](index/sample/arm.json?highlight=2)]

### <a name="deploy-a-self-contained-preview-app"></a><span data-ttu-id="9f436-215">Bereitstellen einer eigenständigen Vorschau-App</span><span class="sxs-lookup"><span data-stu-id="9f436-215">Deploy a self-contained preview app</span></span>

<span data-ttu-id="9f436-216">Eine [eigenständige Bereitstellung](/dotnet/core/deploying/#self-contained-deployments-scd), die auf eine Vorschauruntime abzielt, enthält die Vorschauruntime in der Bereitstellung.</span><span class="sxs-lookup"><span data-stu-id="9f436-216">A [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) that targets a preview runtime carries the preview runtime in the deployment.</span></span>

<span data-ttu-id="9f436-217">Wenn Sie eine eigenständige App bereitstellen, gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="9f436-217">When deploying a self-contained app:</span></span>

* <span data-ttu-id="9f436-218">Die Site in Azure App Service erfordert nicht die [Vorschau-Websiteerweiterung](#install-the-preview-site-extension).</span><span class="sxs-lookup"><span data-stu-id="9f436-218">The site in Azure App Service doesn't require the [preview site extension](#install-the-preview-site-extension).</span></span>
* <span data-ttu-id="9f436-219">Die App muss gemäß einem anderen Ansatz veröffentlicht werden, als beim Veröffentlichen für eine [frameworkabhängige Bereitstellung](/dotnet/core/deploying#framework-dependent-deployments-fdd).</span><span class="sxs-lookup"><span data-stu-id="9f436-219">The app must be published following a different approach than when publishing for a [framework-dependent deployment (FDD)](/dotnet/core/deploying#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="9f436-220">Befolgen Sie die Anweisungen im Abschnitt [Bereitstellen einer eigenständigen Vorschau-App](#deploy-the-app-self-contained).</span><span class="sxs-lookup"><span data-stu-id="9f436-220">Follow the guidance in the [Deploy the app self-contained](#deploy-the-app-self-contained) section.</span></span>

### <a name="use-docker-with-web-apps-for-containers"></a><span data-ttu-id="9f436-221">Verwenden von Docker mit Web-Apps für Container</span><span class="sxs-lookup"><span data-stu-id="9f436-221">Use Docker with Web Apps for containers</span></span>

<span data-ttu-id="9f436-222">Der [Docker-Hub](https://hub.docker.com/r/microsoft/aspnetcore/) enthält die aktuellen Images für die Docker-Vorschauversion.</span><span class="sxs-lookup"><span data-stu-id="9f436-222">The [Docker Hub](https://hub.docker.com/r/microsoft/aspnetcore/) contains the latest preview Docker images.</span></span> <span data-ttu-id="9f436-223">Die Images können als Basisimage verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="9f436-223">The images can be used as a base image.</span></span> <span data-ttu-id="9f436-224">Verwenden Sie das Image, und führen Sie wie gewohnt eine Bereitstellung für Web-Apps für Container durch.</span><span class="sxs-lookup"><span data-stu-id="9f436-224">Use the image and deploy to Web Apps for Containers normally.</span></span>

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="9f436-225">Veröffentlichen und Bereitstellen der App</span><span class="sxs-lookup"><span data-stu-id="9f436-225">Publish and deploy the app</span></span>

### <a name="deploy-the-app-framework-dependent"></a><span data-ttu-id="9f436-226">Frameworkabhängige Bereitstellung einer App</span><span class="sxs-lookup"><span data-stu-id="9f436-226">Deploy the app framework-dependent</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="9f436-227">Von [64-Bit-Frameworks abhängige Bereitstellungen](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="9f436-227">For a 64-bit [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

* <span data-ttu-id="9f436-228">Verwenden Sie ein .NET Core SDK mit 64 Bit, um eine 64-Bit-App zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="9f436-228">Use a 64-bit .NET Core SDK to build a 64-bit app.</span></span>
* <span data-ttu-id="9f436-229">Legen Sie die **Plattform** in App Service unter **Konfiguration** > **Allgemeine Einstellungen** auf **64 Bit** fest.</span><span class="sxs-lookup"><span data-stu-id="9f436-229">Set the **Platform** to **64 Bit** in the App Service's **Configuration** > **General settings**.</span></span> <span data-ttu-id="9f436-230">Die App muss den Serviceplan „Basic“ oder höher verwenden, damit die Bitanzahl der Plattform festgelegt werden kann.</span><span class="sxs-lookup"><span data-stu-id="9f436-230">The app must use a Basic or higher service plan to enable the choice of platform bitness.</span></span>

::: moniker-end

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9f436-231">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9f436-231">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="9f436-232">Klicken Sie in der Visual Studio-Symbolleiste auf **Erstellen** >  **{Anwendungsname} veröffentlichen**, oder klicken Sie mit der rechten Maustaste auf den **Projektmappen-Explorer**, und klicken Sie dann auf **Veröffentlichen**.</span><span class="sxs-lookup"><span data-stu-id="9f436-232">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar or right-click the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="9f436-233">Vergewissern Sie sich im Dialogfeld **Veröffentlichungsziel auswählen**, dass **App Service** ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="9f436-233">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="9f436-234">Wählen Sie **Erweitert** aus.</span><span class="sxs-lookup"><span data-stu-id="9f436-234">Select **Advanced**.</span></span> <span data-ttu-id="9f436-235">Das Dialogfeld **Veröffentlichen** wird geöffnet.</span><span class="sxs-lookup"><span data-stu-id="9f436-235">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="9f436-236">Führen Sie im Dialogfeld **Veröffentlichen** folgende Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="9f436-236">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="9f436-237">Überprüfen Sie, ob die Konfiguration **Release** ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="9f436-237">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="9f436-238">Öffnen Sie die Dropdownliste **Bereitstellungsmodus**, und wählen Sie **Frameworkabhängig** aus.</span><span class="sxs-lookup"><span data-stu-id="9f436-238">Open the **Deployment Mode** drop-down list and select **Framework-Dependent**.</span></span>
   * <span data-ttu-id="9f436-239">Wählen Sie **Portierbar** für **Zielruntime** aus.</span><span class="sxs-lookup"><span data-stu-id="9f436-239">Select **Portable** as the **Target Runtime**.</span></span>
   * <span data-ttu-id="9f436-240">Wenn Sie zusätzliche Dateien bei der Bereitstellung entfernen müssen, öffnen Sie **Dateiveröffentlichungsoptionen**, und aktivieren Sie das Kontrollkästchen, um zusätzliche Dateien am Ziel zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="9f436-240">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="9f436-241">Klicken Sie auf **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="9f436-241">Select **Save**.</span></span>
1. <span data-ttu-id="9f436-242">Sie können eine neue Website erstellen oder eine vorhandene aktualisieren, indem Sie die folgenden Anweisungen des Veröffentlichungs-Assistenten befolgen.</span><span class="sxs-lookup"><span data-stu-id="9f436-242">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="9f436-243">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="9f436-243">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="9f436-244">Geben Sie in der Projektdatei keine [RID (relativer Bezeichner)](/dotnet/core/rid-catalog) an.</span><span class="sxs-lookup"><span data-stu-id="9f436-244">In the project file, don't specify a [Runtime Identifier (RID)](/dotnet/core/rid-catalog).</span></span>

1. <span data-ttu-id="9f436-245">Nutzen Sie die Befehlsshell, um die App mit der Konfiguration „Release“ mit dem Befehl [dotnet publish](/dotnet/core/tools/dotnet-publish) zu veröffentlichen.</span><span class="sxs-lookup"><span data-stu-id="9f436-245">From a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="9f436-246">Im folgenden Beispiel wird die App als frameworkabhängige App veröffentlicht:</span><span class="sxs-lookup"><span data-stu-id="9f436-246">In the following example, the app is published as a framework-dependent app:</span></span>

   ```console
   dotnet publish --configuration Release
   ```

1. <span data-ttu-id="9f436-247">Verschieben Sie den Inhalt des Verzeichnisses *bin/Release/{ZIELFRAMEWORK}/publish* in den Speicherort in App Service.</span><span class="sxs-lookup"><span data-stu-id="9f436-247">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* directory to the site in App Service.</span></span> <span data-ttu-id="9f436-248">Wenn Sie die Inhalte des Ordners *publish* von Ihrer lokalen Festplatte oder Netzwerkfreigabe direkt über die [Kudu](https://github.com/projectkudu/kudu/wiki)-Konsole in App Service verschieben möchten, ziehen Sie die Dateien in den Ordner `D:\home\site\wwwroot` in der Kudu-Konsole.</span><span class="sxs-lookup"><span data-stu-id="9f436-248">If dragging the *publish* folder contents from your local hard drive or network share directly to App Service in the [Kudu](https://github.com/projectkudu/kudu/wiki) console, drag the files to the `D:\home\site\wwwroot` folder in the Kudu console.</span></span>

---

### <a name="deploy-the-app-self-contained"></a><span data-ttu-id="9f436-249">Bereitstellen der App als eigenständige App</span><span class="sxs-lookup"><span data-stu-id="9f436-249">Deploy the app self-contained</span></span>

<span data-ttu-id="9f436-250">Nutzen Sie für [eigenständige Bereitstellungen](/dotnet/core/deploying/#self-contained-deployments-scd) Visual Studio oder die CLI-Tools.</span><span class="sxs-lookup"><span data-stu-id="9f436-250">Use Visual Studio or the command-line interface (CLI) tools for a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9f436-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9f436-251">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="9f436-252">Klicken Sie in der Visual Studio-Symbolleiste auf **Erstellen** >  **{Anwendungsname} veröffentlichen**, oder klicken Sie mit der rechten Maustaste auf den **Projektmappen-Explorer**, und klicken Sie dann auf **Veröffentlichen**.</span><span class="sxs-lookup"><span data-stu-id="9f436-252">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar or right-click the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="9f436-253">Vergewissern Sie sich im Dialogfeld **Veröffentlichungsziel auswählen**, dass **App Service** ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="9f436-253">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="9f436-254">Wählen Sie **Erweitert** aus.</span><span class="sxs-lookup"><span data-stu-id="9f436-254">Select **Advanced**.</span></span> <span data-ttu-id="9f436-255">Das Dialogfeld **Veröffentlichen** wird geöffnet.</span><span class="sxs-lookup"><span data-stu-id="9f436-255">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="9f436-256">Führen Sie im Dialogfeld **Veröffentlichen** folgende Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="9f436-256">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="9f436-257">Überprüfen Sie, ob die Konfiguration **Release** ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="9f436-257">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="9f436-258">Öffnen Sie die Dropdownliste **Bereitstellungsmodus**, und wählen Sie **Eigenständig** aus.</span><span class="sxs-lookup"><span data-stu-id="9f436-258">Open the **Deployment Mode** drop-down list and select **Self-Contained**.</span></span>
   * <span data-ttu-id="9f436-259">Wählen Sie die Zielruntime in der Dropdownliste **Zielruntime** aus.</span><span class="sxs-lookup"><span data-stu-id="9f436-259">Select the target runtime from the **Target Runtime** drop-down list.</span></span> <span data-ttu-id="9f436-260">Die Standardeinstellung ist `win-x86`.</span><span class="sxs-lookup"><span data-stu-id="9f436-260">The default is `win-x86`.</span></span>
   * <span data-ttu-id="9f436-261">Wenn Sie zusätzliche Dateien bei der Bereitstellung entfernen müssen, öffnen Sie **Dateiveröffentlichungsoptionen**, und aktivieren Sie das Kontrollkästchen, um zusätzliche Dateien am Ziel zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="9f436-261">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="9f436-262">Klicken Sie auf **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="9f436-262">Select **Save**.</span></span>
1. <span data-ttu-id="9f436-263">Sie können eine neue Website erstellen oder eine vorhandene aktualisieren, indem Sie die folgenden Anweisungen des Veröffentlichungs-Assistenten befolgen.</span><span class="sxs-lookup"><span data-stu-id="9f436-263">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="9f436-264">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="9f436-264">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="9f436-265">Geben Sie in der Projektdatei eine oder mehrere [Runtimebezeichner (RIDs)](/dotnet/core/rid-catalog) an.</span><span class="sxs-lookup"><span data-stu-id="9f436-265">In the project file, specify one or more [Runtime Identifiers (RIDs)](/dotnet/core/rid-catalog).</span></span> <span data-ttu-id="9f436-266">Verwenden Sie die Singularform `<RuntimeIdentifier>` für eine einzelne RID oder die Pluralform `<RuntimeIdentifiers>`, um eine durch Semikolons getrennte Liste von RIDs bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="9f436-266">Use `<RuntimeIdentifier>` (singular) for a single RID, or use `<RuntimeIdentifiers>` (plural) to provide a semicolon-delimited list of RIDs.</span></span> <span data-ttu-id="9f436-267">Im folgenden Beispiel wird die RID `win-x86` angegeben:</span><span class="sxs-lookup"><span data-stu-id="9f436-267">In the following example, the `win-x86` RID is specified:</span></span>

   ```xml
   <PropertyGroup>
     <TargetFramework>{TARGET FRAMEWORK}</TargetFramework>
     <RuntimeIdentifier>win-x86</RuntimeIdentifier>
   </PropertyGroup>
   ```

1. <span data-ttu-id="9f436-268">Veröffentlichen Sie aus einer Befehlsshell die App in der Releasekonfiguration für die Runtime des Hosts mit dem Befehl [dotnet publish](/dotnet/core/tools/dotnet-publish).</span><span class="sxs-lookup"><span data-stu-id="9f436-268">From a command shell, publish the app in Release configuration for the host's runtime with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="9f436-269">Im folgenden Beispiel wird die App für die RID `win-x86` veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="9f436-269">In the following example, the app is published for the `win-x86` RID.</span></span> <span data-ttu-id="9f436-270">Die für die Option `--runtime` angegebene RID muss in der Eigenschaft `<RuntimeIdentifier>` (oder `<RuntimeIdentifiers>`) in der Projektdatei angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="9f436-270">The RID supplied to the `--runtime` option must be provided in the `<RuntimeIdentifier>` (or `<RuntimeIdentifiers>`) property in the project file.</span></span>

   ```console
   dotnet publish --configuration Release --runtime win-x86
   ```

1. <span data-ttu-id="9f436-271">Verschieben Sie den Inhalt des Verzeichnisses *bin/Release/{ZIELFRAMEWORK}/{RUNTIME-ID}/publish* auf die Site in App Service.</span><span class="sxs-lookup"><span data-stu-id="9f436-271">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* directory to the site in App Service.</span></span> <span data-ttu-id="9f436-272">Wenn Sie die Inhalte des Ordners *publish* von Ihrer lokalen Festplatte oder Netzwerkfreigabe direkt über die Kudu-Konsole in App Service verschieben möchten, ziehen Sie die Dateien in den Ordner `D:\home\site\wwwroot` in der Kudu-Konsole.</span><span class="sxs-lookup"><span data-stu-id="9f436-272">If dragging the *publish* folder contents from your local hard drive or network share directly to App Service in the Kudu console, drag the files to the `D:\home\site\wwwroot` folder in the Kudu console.</span></span>

---

## <a name="protocol-settings-https"></a><span data-ttu-id="9f436-273">Protokolleinstellungen (HTTPS)</span><span class="sxs-lookup"><span data-stu-id="9f436-273">Protocol settings (HTTPS)</span></span>

<span data-ttu-id="9f436-274">Durch sichere Protokollbindungen können Sie ein Zertifikat festlegen, das verwendet werden soll, wenn über HTTPS auf Anforderungen geantwortet wird.</span><span class="sxs-lookup"><span data-stu-id="9f436-274">Secure protocol bindings allow you specify a certificate to use when responding to requests over HTTPS.</span></span> <span data-ttu-id="9f436-275">Bindungen erfordern gültige private Zertifikate ( *.pfx*), die für den angegebenen Hostnamen ausgestellt wurden.</span><span class="sxs-lookup"><span data-stu-id="9f436-275">Binding requires a valid private certificate (*.pfx*) issued for the specific hostname.</span></span> <span data-ttu-id="9f436-276">Weitere Informationen finden Sie unter [Tutorial: Binden eines vorhandenen benutzerdefinierten SSL-Zertifikats an Azure App Service](/azure/app-service/app-service-web-tutorial-custom-ssl).</span><span class="sxs-lookup"><span data-stu-id="9f436-276">For more information, see [Tutorial: Bind an existing custom SSL certificate to Azure App Service](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

## <a name="transform-webconfig"></a><span data-ttu-id="9f436-277">Transformieren von web.config</span><span class="sxs-lookup"><span data-stu-id="9f436-277">Transform web.config</span></span>

<span data-ttu-id="9f436-278">Wenn Sie *web.config* beim Veröffentlichen transformieren müssen (z.B. Umgebungsvariablen basierend auf der Konfiguration, dem Profil oder der Umgebung festlegen müssen), finden Sie weitere Informationen unter <xref:host-and-deploy/iis/transform-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="9f436-278">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9f436-279">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="9f436-279">Additional resources</span></span>

* [<span data-ttu-id="9f436-280">App Service-Übersicht</span><span class="sxs-lookup"><span data-stu-id="9f436-280">App Service overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="9f436-281">Azure App Service: The Best Place to Host your .NET Apps (Azure App Service: Ideal zum Hosten von .NET-Apps; 55-minütiges Video)</span><span class="sxs-lookup"><span data-stu-id="9f436-281">Azure App Service: The Best Place to Host your .NET Apps (55-minute overview video)</span></span>](https://channel9.msdn.com/events/dotnetConf/2017/T222)
* [<span data-ttu-id="9f436-282">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (Azure Friday: Azure App Service-Diagnose und -Fehlerbehebung; zwölfminütiges Video)</span><span class="sxs-lookup"><span data-stu-id="9f436-282">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
* [<span data-ttu-id="9f436-283">Übersicht: Azure App Service-Diagnose</span><span class="sxs-lookup"><span data-stu-id="9f436-283">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* <xref:host-and-deploy/web-farm>

<span data-ttu-id="9f436-284">Azure App Service auf Windows Server verwendet [Internetinformationsdienste (IIS)](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="9f436-284">Azure App Service on Windows Server uses [Internet Information Services (IIS)](https://www.iis.net/).</span></span> <span data-ttu-id="9f436-285">Die folgenden Artikel gehen auf die zugrunde liegende IIS-Technologie ein:</span><span class="sxs-lookup"><span data-stu-id="9f436-285">The following topics pertain to the underlying IIS technology:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>
* [<span data-ttu-id="9f436-286">Windows Server – IT-Administrator-Inhalt für aktuelle und frühere Versionen</span><span class="sxs-lookup"><span data-stu-id="9f436-286">Windows Server - IT administrator content for current and previous releases</span></span>](/windows-server/windows-server-versions)

---
title: Bereitstellen von ASP.NET Core-Apps in Azure App Service
author: guardrex
description: Dieser Artikel enthält Links zu Azure-Host- und Bereitstellungsressourcen.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2019
uid: host-and-deploy/azure-apps/index
ms.openlocfilehash: 009ee97d954a21f5fca1713b2b45218cac235e33
ms.sourcegitcommit: 1a7000630e55da90da19b284e1b2f2f13a393d74
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/04/2019
ms.locfileid: "59012837"
---
# <a name="deploy-aspnet-core-apps-to-azure-app-service"></a><span data-ttu-id="52159-103">Bereitstellen von ASP.NET Core-Apps in Azure App Service</span><span class="sxs-lookup"><span data-stu-id="52159-103">Deploy ASP.NET Core apps to Azure App Service</span></span>

<span data-ttu-id="52159-104">[Azure App Service](https://azure.microsoft.com/services/app-service/) ist ein [Microsoft Cloud Computing-Plattformdienst](https://azure.microsoft.com/) zum Hosten von Web-Apps. Dazu gehört auch ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="52159-104">[Azure App Service](https://azure.microsoft.com/services/app-service/) is a [Microsoft cloud computing platform service](https://azure.microsoft.com/) for hosting web apps, including ASP.NET Core.</span></span>

## <a name="useful-resources"></a><span data-ttu-id="52159-105">Nützliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="52159-105">Useful resources</span></span>

<span data-ttu-id="52159-106">In der [App Service-Dokumentation](/azure/app-service/) finden Sie die Azure-Apps-Dokumentation, Tutorials, Beispiele und Leitfäden sowie andere Ressourcen.</span><span class="sxs-lookup"><span data-stu-id="52159-106">[App Service Documentation](/azure/app-service/) is the home for Azure Apps documentation, tutorials, samples, how-to guides, and other resources.</span></span> <span data-ttu-id="52159-107">Dies sind zwei erwähnenswerte Tutorials, die auf das Hosten von ASP.NET Core-Apps eingehen:</span><span class="sxs-lookup"><span data-stu-id="52159-107">Two notable tutorials that pertain to hosting ASP.NET Core apps are:</span></span>

[<span data-ttu-id="52159-108">Erstellen von ASP.NET Core-Web-Apps in Azure</span><span class="sxs-lookup"><span data-stu-id="52159-108">Create an ASP.NET Core web app in Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet)  
<span data-ttu-id="52159-109">Verwenden Sie Visual Studio, um ASP.NET Core-Web-Apps zu erstellen und in Azure App Service unter Windows bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="52159-109">Use Visual Studio to create and deploy an ASP.NET Core web app to Azure App Service on Windows.</span></span>

[<span data-ttu-id="52159-110">Erstellen einer .NET Core-App in App Service für Linux</span><span class="sxs-lookup"><span data-stu-id="52159-110">Create an ASP.NET Core app in App Service on Linux</span></span>](/azure/app-service/containers/quickstart-dotnetcore)  
<span data-ttu-id="52159-111">Verwenden Sie die Befehlszeile, um ASP.NET Core-Web-Apps zu erstellen und in Azure App Service unter Linux bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="52159-111">Use the command line to create and deploy an ASP.NET Core web app to Azure App Service on Linux.</span></span>

<span data-ttu-id="52159-112">Die folgenden Artikel sind in der ASP.NET Core-Dokumentation verfügbar:</span><span class="sxs-lookup"><span data-stu-id="52159-112">The following articles are available in ASP.NET Core documentation:</span></span>

<xref:tutorials/publish-to-azure-webapp-using-vs>  
<span data-ttu-id="52159-113">Erfahren Sie, wie eine ASP.NET Core-App in Azure App Service mit Visual Studio veröffentlicht wird.</span><span class="sxs-lookup"><span data-stu-id="52159-113">Learn how to publish an ASP.NET Core app to Azure App Service using Visual Studio.</span></span>

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>  
<span data-ttu-id="52159-114">Erfahren Sie, wie Sie mit Visual Studio eine ASP.NET Core-Web-App erstellen und sie unter Verwendung von Git für Continuous Deployment in Azure App Service bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="52159-114">Learn how to create an ASP.NET Core web app using Visual Studio and deploy it to Azure App Service using Git for continuous deployment.</span></span>

[<span data-ttu-id="52159-115">Erstellen Ihrer ersten Pipeline</span><span class="sxs-lookup"><span data-stu-id="52159-115">Create your first pipeline</span></span>](/azure/devops/pipelines/get-started-yaml)  
<span data-ttu-id="52159-116">Richten Sie ein CI-Build für eine ASP.NET Core-App ein, und erstellen Sie dann ein Continuous Deployment-Release für Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="52159-116">Set up a CI build for an ASP.NET Core app, then create a continuous deployment release to Azure App Service.</span></span>

[<span data-ttu-id="52159-117">Azure Web App-Sandbox</span><span class="sxs-lookup"><span data-stu-id="52159-117">Azure Web App sandbox</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)  
<span data-ttu-id="52159-118">Entdecken Sie die Einschränkungen der Azure App Service-Laufzeitausführung, die durch die Azure Apps-Plattform erzwungen werden.</span><span class="sxs-lookup"><span data-stu-id="52159-118">Discover Azure App Service runtime execution limitations enforced by the Azure Apps platform.</span></span>

## <a name="application-configuration"></a><span data-ttu-id="52159-119">Anwendungskonfiguration</span><span class="sxs-lookup"><span data-stu-id="52159-119">Application configuration</span></span>

### <a name="platform"></a><span data-ttu-id="52159-120">Plattform</span><span class="sxs-lookup"><span data-stu-id="52159-120">Platform</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="52159-121">In Azure App Service sind Runtimes für 64-Bit- und 32-Bit-Apps (x64 und x86) vorhanden.</span><span class="sxs-lookup"><span data-stu-id="52159-121">Runtimes for 64-bit (x64) and 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="52159-122">Das [.NET Core SDK](/dotnet/core/sdk), das in Azure App Service zur Verfügung steht, ist eine 32-Bit-Version, aber Sie können auch 64-Bit-Apps mit der [Kudu](https://github.com/projectkudu/kudu/wiki)-Konsole oder über [MSDeploy mit einem Visual Studio-Veröffentlichungsprofil oder über einen CLI-Befehl](xref:host-and-deploy/visual-studio-publish-profiles) erstellen.</span><span class="sxs-lookup"><span data-stu-id="52159-122">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit, but you can deploy 64-bit apps using the [Kudu](https://github.com/projectkudu/kudu/wiki) console or via [MSDeploy with a Visual Studio publish profile or CLI command](xref:host-and-deploy/visual-studio-publish-profiles).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="52159-123">Für Apps mit nativen Abhängigkeiten stehen in Azure App Service Runtimes für 32-Bit- und 32-Bit-Apps (x86 und x86) zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="52159-123">For apps with native dependencies, runtimes for 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="52159-124">Das [.NET Core SDK](/dotnet/core/sdk) in Azure App Service ist eine 32-Bit-Version.</span><span class="sxs-lookup"><span data-stu-id="52159-124">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit.</span></span>

::: moniker-end

### <a name="packages"></a><span data-ttu-id="52159-125">Pakete</span><span class="sxs-lookup"><span data-stu-id="52159-125">Packages</span></span>

<span data-ttu-id="52159-126">Beziehen Sie die folgenden NuGet-Pakete ein, die automatische Protokollierungsfeatures für Apps bieten, die für Azure App Service bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="52159-126">Include the following NuGet packages to provide automatic logging features for apps deployed to Azure App Service:</span></span>

* <span data-ttu-id="52159-127">[Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) verwendet [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration), um die ASP.NET Core-Lightup-Integration mit Azure App Service bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="52159-127">[Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) uses [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) to provide ASP.NET Core light-up integration with Azure App Service.</span></span> <span data-ttu-id="52159-128">Die hinzugefügten Protokollierungsfeatures werden vom `Microsoft.AspNetCore.AzureAppServicesIntegration`-Paket bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="52159-128">The added logging features are provided by the `Microsoft.AspNetCore.AzureAppServicesIntegration` package.</span></span>
* <span data-ttu-id="52159-129">[Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) führt [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) aus, um Anbieter für die Azure App Service-Diagnoseprotokollierung zum Paket `Microsoft.Extensions.Logging.AzureAppServices` hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="52159-129">[Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) executes [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) to add Azure App Service diagnostics logging providers in the `Microsoft.Extensions.Logging.AzureAppServices` package.</span></span>
* <span data-ttu-id="52159-130">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) stellt Protokollierungsimplementierungen bereit, um die Azure App Service-Features für Diagnoseprotokolle und Protokollstreaming zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="52159-130">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) provides logger implementations to support Azure App Service diagnostics logs and log streaming features.</span></span>

<span data-ttu-id="52159-131">Die Metapakete [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) sind nicht in den vorherigen Paketen enthalten.</span><span class="sxs-lookup"><span data-stu-id="52159-131">The preceding packages aren't available from the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="52159-132">Apps, deren Ziel das .NET Framework ist oder die auf das `Microsoft.AspNetCore.App`-Metapaket verweisen, müssen explizit auf die einzelnen Pakete in der Projektdatei der App verweisen.</span><span class="sxs-lookup"><span data-stu-id="52159-132">Apps that target .NET Framework or reference the `Microsoft.AspNetCore.App` metapackage must explicitly reference the individual packages in the app's project file.</span></span>

## <a name="override-app-configuration-using-the-azure-portal"></a><span data-ttu-id="52159-133">Überschreiben der App-Konfiguration im Azure-Portal</span><span class="sxs-lookup"><span data-stu-id="52159-133">Override app configuration using the Azure Portal</span></span>

<span data-ttu-id="52159-134">Über die App-Einstellungen im Azure-Portal können Sie Umgebungsvariablen für die App festlegen.</span><span class="sxs-lookup"><span data-stu-id="52159-134">App settings in the Azure Portal permit you to set environment variables for the app.</span></span> <span data-ttu-id="52159-135">Umgebungsvariablen können von [Umgebungsvariablen-Konfigurationsanbietern](xref:fundamentals/configuration/index#environment-variables-configuration-provider) verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="52159-135">Environment variables can be consumed by the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

<span data-ttu-id="52159-136">Wenn eine App-Einstellung im Azure-Portal erstellt oder geändert und die Schaltfläche **Speichern** ausgewählt wird, wird die Azure-App neu gestartet.</span><span class="sxs-lookup"><span data-stu-id="52159-136">When an app setting is created or modified in the Azure Portal and the **Save** button is selected, the Azure App is restarted.</span></span> <span data-ttu-id="52159-137">Die Umgebungsvariable steht der App nach dem Neustart des Diensts zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="52159-137">The environment variable is available to the app after the service restarts.</span></span>

<span data-ttu-id="52159-138">Wenn eine App den [Webhost](xref:fundamentals/host/web-host) verwendet und den Host mit [WebHost.CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) erstellt, verwenden Umgebungsvariablen, die den Host konfigurieren, das Präfix `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="52159-138">When an app uses the [Web Host](xref:fundamentals/host/web-host) and builds the host using [WebHost.CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder), environment variables that configure the host use the `ASPNETCORE_` prefix.</span></span> <span data-ttu-id="52159-139">Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host> und unter [Umgebungsvariablen-Konfigurationsanbieter](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="52159-139">For more information, see <xref:fundamentals/host/web-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

<span data-ttu-id="52159-140">Wenn eine App den [generischen Host](xref:fundamentals/host/generic-host) verwendet, werden Umgebungsvariablen nicht standardmäßig in die App-Konfiguration geladen, und der Konfigurationsanbieter muss vom Entwickler hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="52159-140">When an app uses the [Generic Host](xref:fundamentals/host/generic-host), environment variables aren't loaded into an app's configuration by default and the configuration provider must be added by the developer.</span></span> <span data-ttu-id="52159-141">Der Entwickler legt das Präfix der Umgebungsvariablen fest, wenn der Konfigurationsanbieter hinzugefügt wird.</span><span class="sxs-lookup"><span data-stu-id="52159-141">The developer determines the environment variable prefix when the configuration provider is added.</span></span> <span data-ttu-id="52159-142">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host> und unter [Umgebungsvariablen-Konfigurationsanbieter](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="52159-142">For more information, see <xref:fundamentals/host/generic-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="52159-143">Proxyserver und Lastenausgleichsszenarien</span><span class="sxs-lookup"><span data-stu-id="52159-143">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="52159-144">Die Middleware für die Integration von IIS, die ForwardedHeadersMiddleware konfiguriert, und das ASP.NET Core-Modul sind so konfiguriert, dass sie das Schema (HTTP/HTTPS) und die Remote-IP-Adresse an die Stelle weiterleiten, von der die Anforderung stammte.</span><span class="sxs-lookup"><span data-stu-id="52159-144">The IIS Integration Middleware, which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="52159-145">Möglicherweise ist zusätzliche Konfiguration für Apps erforderlich, die hinter weiteren Proxyservern und Lastenausgleichsmodulen (Load Balancer) gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="52159-145">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="52159-146">Weitere Informationen hierzu feinden Sie unter [Konfigurieren von ASP.NET Core zur Verwendung mit Proxyservern und Lastenausgleich](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="52159-146">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="monitoring-and-logging"></a><span data-ttu-id="52159-147">Überwachung und Protokollierung</span><span class="sxs-lookup"><span data-stu-id="52159-147">Monitoring and logging</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="52159-148">In App Service bereitgestellte ASP.NET Core-Apps erhalten automatisch die App Service-Erweiterung **ASP.NET Core Logging Integration** (ASP.NET Core-Protokollierungsintegration).</span><span class="sxs-lookup"><span data-stu-id="52159-148">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Integration**.</span></span> <span data-ttu-id="52159-149">Durch diese Erweiterung ist die Protokollierungsintegration für ASP.NET Core-Apps in Azure App Service möglich.</span><span class="sxs-lookup"><span data-stu-id="52159-149">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="52159-150">In App Service bereitgestellte ASP.NET Core-Apps erhalten automatisch die App Service-Erweiterung **ASP.NET Core-Protokollierungserweiterungen**.</span><span class="sxs-lookup"><span data-stu-id="52159-150">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Extensions**.</span></span> <span data-ttu-id="52159-151">Durch diese Erweiterung ist die Protokollierungsintegration für ASP.NET Core-Apps in Azure App Service möglich.</span><span class="sxs-lookup"><span data-stu-id="52159-151">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span></span>

::: moniker-end

<span data-ttu-id="52159-152">In den folgenden Artikeln finden Sie Informationen zum Überwachen, Protokollieren und zur Problembehandlung:</span><span class="sxs-lookup"><span data-stu-id="52159-152">For monitoring, logging, and troubleshooting information, see the following articles:</span></span>

[<span data-ttu-id="52159-153">Überwachen von Apps in Azure App Service</span><span class="sxs-lookup"><span data-stu-id="52159-153">Monitor apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)  
<span data-ttu-id="52159-154">Erfahren Sie, wie Sie Kontingente und Metrik für Apps und App Service-Pläne prüfen.</span><span class="sxs-lookup"><span data-stu-id="52159-154">Learn how to review quotas and metrics for apps and App Service plans.</span></span>

[<span data-ttu-id="52159-155">Aktivieren der Diagnoseprotokollierung für Apps in Azure App Service</span><span class="sxs-lookup"><span data-stu-id="52159-155">Enable diagnostics logging for apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)  
<span data-ttu-id="52159-156">Erfahren Sie, wie Sie die Diagnosesprotokollierung für HTTP-Statuscodes, fehlgeschlagene Anforderungen und Webserveraktivitäten aktivieren und auf die Protokolle zugreifen.</span><span class="sxs-lookup"><span data-stu-id="52159-156">Discover how to enable and access diagnostic logging for HTTP status codes, failed requests, and web server activity.</span></span>

<xref:fundamentals/error-handling>  
<span data-ttu-id="52159-157">Lernen Sie gängige Methoden zur Fehlerbehandlung in ASP.NET Core-Apps kennen.</span><span class="sxs-lookup"><span data-stu-id="52159-157">Understand common approaches to handling errors in ASP.NET Core apps.</span></span>

<xref:host-and-deploy/azure-apps/troubleshoot>  
<span data-ttu-id="52159-158">Erfahren Sie, wie Sie Probleme mit Azure App Service-Bereitstellungen mit ASP.NET Core-Apps diagnostizieren können.</span><span class="sxs-lookup"><span data-stu-id="52159-158">Learn how to diagnose issues with Azure App Service deployments with ASP.NET Core apps.</span></span>

<xref:host-and-deploy/azure-iis-errors-reference>  
<span data-ttu-id="52159-159">Sehen Sie sich häufig auftretende Fehler und entsprechende Hinweise zur Fehlerbehebung bei der Bereitstellungskonfiguration für von Azure App Service/IIS gehosteten Apps an.</span><span class="sxs-lookup"><span data-stu-id="52159-159">See the common deployment configuration errors for apps hosted by Azure App Service/IIS with troubleshooting advice.</span></span>

## <a name="data-protection-key-ring-and-deployment-slots"></a><span data-ttu-id="52159-160">Data Protection-Schlüsselbund und -Bereitstellungsslots</span><span class="sxs-lookup"><span data-stu-id="52159-160">Data Protection key ring and deployment slots</span></span>

<span data-ttu-id="52159-161">[Data Protection-Schlüssel](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) werden im Ordner *%HOME%\ASP.NET\DataProtection-Keys* gespeichert.</span><span class="sxs-lookup"><span data-stu-id="52159-161">[Data Protection keys](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) are persisted to the *%HOME%\ASP.NET\DataProtection-Keys* folder.</span></span> <span data-ttu-id="52159-162">Dieser Ordner wird von einem Netzwerkspeicher unterstützt und mit allen Computern, auf denen die App gehostet wird, synchronisiert.</span><span class="sxs-lookup"><span data-stu-id="52159-162">This folder is backed by network storage and is synchronized across all machines hosting the app.</span></span> <span data-ttu-id="52159-163">Ruhende Schlüssel werden nicht geschützt.</span><span class="sxs-lookup"><span data-stu-id="52159-163">Keys aren't protected at rest.</span></span> <span data-ttu-id="52159-164">Dieser Ordner stellt den Schlüsselbund für alle Instanzen einer App in einem einzelnen Bereitstellungsslot bereit.</span><span class="sxs-lookup"><span data-stu-id="52159-164">This folder supplies the key ring to all instances of an app in a single deployment slot.</span></span> <span data-ttu-id="52159-165">Separate Bereitstellungsslots, wie Staging und Produktion, verwenden keinen gemeinsamen Schlüsselbund.</span><span class="sxs-lookup"><span data-stu-id="52159-165">Separate deployment slots, such as Staging and Production, don't share a key ring.</span></span>

<span data-ttu-id="52159-166">Wenn Sie zwischen Bereitstellungsslots wechseln, können Systeme, die Data Protection verwenden, gespeicherte Daten nicht mit dem Schlüsselbund des vorherigen Slots entschlüsseln.</span><span class="sxs-lookup"><span data-stu-id="52159-166">When swapping between deployment slots, any system using data protection won't be able to decrypt stored data using the key ring inside the previous slot.</span></span> <span data-ttu-id="52159-167">ASP.NET-Cookiemiddleware verwendet Data Protection zum Schutz ihrer Cookies.</span><span class="sxs-lookup"><span data-stu-id="52159-167">ASP.NET Cookie Middleware uses data protection to protect its cookies.</span></span> <span data-ttu-id="52159-168">Dies führt dazu, dass Benutzer in einer App abgemeldet werden, die Standard-ASP.NET-Cookiemiddleware verwendet.</span><span class="sxs-lookup"><span data-stu-id="52159-168">This leads to users being signed out of an app that uses the standard ASP.NET Cookie Middleware.</span></span> <span data-ttu-id="52159-169">Verwende Sie einen externen Schlüsselbundanbieter für eine slotunabhängige Schlüsselbundlösung wie z.B.:</span><span class="sxs-lookup"><span data-stu-id="52159-169">For a slot-independent key ring solution, use an external key ring provider, such as:</span></span>

* <span data-ttu-id="52159-170">Azure Blob Storage</span><span class="sxs-lookup"><span data-stu-id="52159-170">Azure Blob Storage</span></span>
* <span data-ttu-id="52159-171">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="52159-171">Azure Key Vault</span></span>
* <span data-ttu-id="52159-172">SQL-Speicher</span><span class="sxs-lookup"><span data-stu-id="52159-172">SQL store</span></span>
* <span data-ttu-id="52159-173">Redis Cache</span><span class="sxs-lookup"><span data-stu-id="52159-173">Redis cache</span></span>

<span data-ttu-id="52159-174">Weitere Informationen finden Sie unter <xref:security/data-protection/implementation/key-storage-providers>.</span><span class="sxs-lookup"><span data-stu-id="52159-174">For more information, see <xref:security/data-protection/implementation/key-storage-providers>.</span></span>

## <a name="deploy-aspnet-core-preview-release-to-azure-app-service"></a><span data-ttu-id="52159-175">Bereitstellen der ASP.NET Core Vorschauversion für Azure App Service</span><span class="sxs-lookup"><span data-stu-id="52159-175">Deploy ASP.NET Core preview release to Azure App Service</span></span>

<span data-ttu-id="52159-176">Verwenden Sie einen der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="52159-176">Use one of the following approaches:</span></span>

* <span data-ttu-id="52159-177">[Installieren der Websiteerweiterung (Vorschau)](#install-the-preview-site-extension).</span><span class="sxs-lookup"><span data-stu-id="52159-177">[Install the preview site extension](#install-the-preview-site-extension).</span></span>
* <span data-ttu-id="52159-178">[Bereitstellen der App als eigenständige App](#deploy-the-app-self-contained).</span><span class="sxs-lookup"><span data-stu-id="52159-178">[Deploy the app self-contained](#deploy-the-app-self-contained).</span></span>
* <span data-ttu-id="52159-179">[Verwenden von Docker mit Web-Apps für Container](#use-docker-with-web-apps-for-containers).</span><span class="sxs-lookup"><span data-stu-id="52159-179">[Use Docker with Web Apps for containers](#use-docker-with-web-apps-for-containers).</span></span>

### <a name="install-the-preview-site-extension"></a><span data-ttu-id="52159-180">Installieren der Websiteerweiterung (Vorschau)</span><span class="sxs-lookup"><span data-stu-id="52159-180">Install the preview site extension</span></span>

<span data-ttu-id="52159-181">Sollte ein Problem mit dem Verwenden der Vorschau der Websiteerweiterung auftreten, öffnen Sie ein [aspnet/AspNetCore-Issue](https://github.com/aspnet/AspNetCore/issues).</span><span class="sxs-lookup"><span data-stu-id="52159-181">If a problem occurs using the preview site extension, open an [aspnet/AspNetCore issue](https://github.com/aspnet/AspNetCore/issues).</span></span>

1. <span data-ttu-id="52159-182">Navigieren Sie im Azure-Portal zu „App Service“.</span><span class="sxs-lookup"><span data-stu-id="52159-182">From the Azure Portal, navigate to the App Service.</span></span>
1. <span data-ttu-id="52159-183">Wählen Sie die Web-App aus.</span><span class="sxs-lookup"><span data-stu-id="52159-183">Select the web app.</span></span>
1. <span data-ttu-id="52159-184">Geben Sie „ex“ in das Suchfeld ein, um nach „Extensions“ (Erweiterungen) zu filtern, oder durchsuchen Sie die Liste der Verwaltungstools.</span><span class="sxs-lookup"><span data-stu-id="52159-184">Type "ex" in the search box to filter for "Extensions" or scroll down the list of management tools.</span></span>
1. <span data-ttu-id="52159-185">Wählen Sie **Erweiterungen** aus.</span><span class="sxs-lookup"><span data-stu-id="52159-185">Select **Extensions**.</span></span>
1. <span data-ttu-id="52159-186">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="52159-186">Select **Add**.</span></span>
1. <span data-ttu-id="52159-187">Wählen Sie die Erweiterung **ASP.NET Core {X.Y} ({x64|x86}) Runtime** aus der Liste aus. Dabei ist `{X.Y}` die ASP.NET Core-Vorschauversion, und `{x64|x86}` gibt die Plattform an.</span><span class="sxs-lookup"><span data-stu-id="52159-187">Select the **ASP.NET Core {X.Y} ({x64|x86}) Runtime** extension from the list, where `{X.Y}` is the ASP.NET Core preview version and `{x64|x86}` specifies the platform.</span></span>
1. <span data-ttu-id="52159-188">Klicken Sie auf **OK**, um die rechtlichen Bedingungen zu akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="52159-188">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="52159-189">Wählen Sie **OK** aus, um die Erweiterung zu installieren.</span><span class="sxs-lookup"><span data-stu-id="52159-189">Select **OK** to install the extension.</span></span>

<span data-ttu-id="52159-190">Nach Abschluss dieses Vorgangs wird die neueste .NET Core-Vorschauversion installiert.</span><span class="sxs-lookup"><span data-stu-id="52159-190">When the operation completes, the latest .NET Core preview is installed.</span></span> <span data-ttu-id="52159-191">Überprüfen Sie die Installation:</span><span class="sxs-lookup"><span data-stu-id="52159-191">Verify the installation:</span></span>

1. <span data-ttu-id="52159-192">Wählen Sie **Erweiterte Tools** aus.</span><span class="sxs-lookup"><span data-stu-id="52159-192">Select **Advanced Tools**.</span></span>
1. <span data-ttu-id="52159-193">Wählen Sie **Go** unter **Erweiterte Tools** aus.</span><span class="sxs-lookup"><span data-stu-id="52159-193">Select **Go** in **Advanced Tools**.</span></span>
1. <span data-ttu-id="52159-194">Wählen Sie das Menüelement **Debugkonsole** > **PowerShell** aus.</span><span class="sxs-lookup"><span data-stu-id="52159-194">Select the **Debug console** > **PowerShell** menu item.</span></span>
1. <span data-ttu-id="52159-195">Führen Sie in der PowerShell-Eingabeaufforderung den folgenden Befehl aus.</span><span class="sxs-lookup"><span data-stu-id="52159-195">At the PowerShell prompt, execute the following command.</span></span> <span data-ttu-id="52159-196">Ersetzen Sie im Befehl die ASP.NET Core-Runtimeversion durch `{X.Y}` und die Plattform durch `{PLATFORM}`:</span><span class="sxs-lookup"><span data-stu-id="52159-196">Substitute the ASP.NET Core runtime version for `{X.Y}` and the platform for `{PLATFORM}` in the command:</span></span>

   ```powershell
   Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.{PLATFORM}\
   ```

   <span data-ttu-id="52159-197">Der Befehl gibt `True` zurück, wenn die x64-Vorschauruntime installiert ist.</span><span class="sxs-lookup"><span data-stu-id="52159-197">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="52159-198">Die Plattformarchitektur (x86/x64) einer App Services-App wird in den Einstellungen der App im Azure-Portal festgelegt. Dies gilt für Apps, die in einem A-Series-Computetarif oder einem höheren Hostingtarif gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="52159-198">The platform architecture (x86/x64) of an App Services app is set in the app's settings in the Azure Portal for apps that are hosted on an A-series compute or better hosting tier.</span></span> <span data-ttu-id="52159-199">Wenn die Anwendung im In-Process-Modus ausgeführt wird und die Plattformarchitektur für 64-Bit (x64) konfiguriert ist, verwendet das ASP.NET Core-Modul die 64-Bit-Vorschauruntime, falls vorhanden.</span><span class="sxs-lookup"><span data-stu-id="52159-199">If the app is run in in-process mode and the platform architecture is configured for 64-bit (x64), the ASP.NET Core Module uses the 64-bit preview runtime, if present.</span></span> <span data-ttu-id="52159-200">Installieren Sie die Erweiterung **ASP.NET Core {X.Y} (x64) Runtime**.</span><span class="sxs-lookup"><span data-stu-id="52159-200">Install the **ASP.NET Core {X.Y} (x64) Runtime** extension.</span></span>
>
> <span data-ttu-id="52159-201">Nach der Installation der x64-Vorschauruntime führen Sie den folgenden Befehl im Kudu PowerShell-Befehlsfenster aus, um die Installation zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="52159-201">After installing the x64 preview runtime, run the following command in the Kudu PowerShell command window to verify the installation.</span></span> <span data-ttu-id="52159-202">Ersetzen Sie im Befehl die ASP.NET Core-Runtimeversion für `{X.Y}`:</span><span class="sxs-lookup"><span data-stu-id="52159-202">Substitute the ASP.NET Core runtime version for `{X.Y}` in the command:</span></span>
>
> ```powershell
> Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64\
> ```
>
> <span data-ttu-id="52159-203">Der Befehl gibt `True` zurück, wenn die x64-Vorschauruntime installiert ist.</span><span class="sxs-lookup"><span data-stu-id="52159-203">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="52159-204">**ASP.NET Core-Erweiterungen** aktivieren zusätzliche Funktionen für ASP.NET Core in Azure App Services, z.B. Azure-Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="52159-204">**ASP.NET Core Extensions** enables additional functionality for ASP.NET Core on Azure App Services, such as enabling Azure logging.</span></span> <span data-ttu-id="52159-205">Die Erweiterung wird automatisch installiert, wenn die Bereitstellung aus Visual Studio erfolgt.</span><span class="sxs-lookup"><span data-stu-id="52159-205">The extension is installed automatically when deploying from Visual Studio.</span></span> <span data-ttu-id="52159-206">Wenn die Erweiterung nicht installiert ist, installieren Sie sie für die App.</span><span class="sxs-lookup"><span data-stu-id="52159-206">If the extension isn't installed, install it for the app.</span></span>

**<span data-ttu-id="52159-207">Verwenden der Vorschau-Websiteerweiterung mit einer ARM-Vorlage</span><span class="sxs-lookup"><span data-stu-id="52159-207">Use the preview site extension with an ARM template</span></span>**

<span data-ttu-id="52159-208">Wenn Sie eine ARM-Vorlage zum Erstellen und Bereitstellen von Anwendungen verwenden, können Sie den Ressourcentyp `siteextensions` verwenden, um die Websiteerweiterung zu einer Web-App hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="52159-208">If an ARM template is used to create and deploy apps, the `siteextensions` resource type can be used to add the site extension to a web app.</span></span> <span data-ttu-id="52159-209">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="52159-209">For example:</span></span>

[!code-json[](index/sample/arm.json?highlight=2)]

### <a name="deploy-the-app-self-contained"></a><span data-ttu-id="52159-210">Bereitstellen der App als eigenständige App</span><span class="sxs-lookup"><span data-stu-id="52159-210">Deploy the app self-contained</span></span>

<span data-ttu-id="52159-211">Eine [eigenständige Bereitstellung](/dotnet/core/deploying/#self-contained-deployments-scd), die auf eine Vorschauruntime abzielt, enthält die Vorschauruntime in der Bereitstellung.</span><span class="sxs-lookup"><span data-stu-id="52159-211">A [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) that targets a preview runtime carries the preview runtime in the deployment.</span></span>

<span data-ttu-id="52159-212">Wenn Sie eine eigenständige App bereitstellen, gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="52159-212">When deploying a self-contained app:</span></span>

* <span data-ttu-id="52159-213">Die Site in Azure App Service erfordert nicht die [Vorschau-Websiteerweiterung](#install-the-preview-site-extension).</span><span class="sxs-lookup"><span data-stu-id="52159-213">The site in Azure App Service doesn't require the [preview site extension](#install-the-preview-site-extension).</span></span>
* <span data-ttu-id="52159-214">Die App muss gemäß einem anderen Ansatz veröffentlicht werden, als beim Veröffentlichen für eine [frameworkabhängige Bereitstellung](/dotnet/core/deploying#framework-dependent-deployments-fdd).</span><span class="sxs-lookup"><span data-stu-id="52159-214">The app must be published following a different approach than when publishing for a [framework-dependent deployment (FDD)](/dotnet/core/deploying#framework-dependent-deployments-fdd).</span></span>

#### <a name="publish-from-visual-studio"></a><span data-ttu-id="52159-215">Veröffentlichen aus Visual Studio</span><span class="sxs-lookup"><span data-stu-id="52159-215">Publish from Visual Studio</span></span>

1. <span data-ttu-id="52159-216">Wählen Sie in der Visual Studio-Symbolleiste **Erstellen** > **{Anwendungsname} veröffentlichen** aus.</span><span class="sxs-lookup"><span data-stu-id="52159-216">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar.</span></span>
1. <span data-ttu-id="52159-217">Vergewissern Sie sich im Dialogfeld **Veröffentlichungsziel auswählen**, dass **App Service** ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="52159-217">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="52159-218">Wählen Sie **Erweitert** aus.</span><span class="sxs-lookup"><span data-stu-id="52159-218">Select **Advanced**.</span></span> <span data-ttu-id="52159-219">Das Dialogfeld **Veröffentlichen** wird geöffnet.</span><span class="sxs-lookup"><span data-stu-id="52159-219">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="52159-220">Führen Sie im Dialogfeld **Veröffentlichen** folgende Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="52159-220">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="52159-221">Überprüfen Sie, ob die Konfiguration **Release** ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="52159-221">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="52159-222">Öffnen Sie die Dropdownliste **Bereitstellungsmodus**, und wählen Sie **Eigenständig** aus.</span><span class="sxs-lookup"><span data-stu-id="52159-222">Open the **Deployment Mode** drop-down list and select **Self-Contained**.</span></span>
   * <span data-ttu-id="52159-223">Wählen Sie die Zielruntime in der Dropdownliste **Zielruntime** aus.</span><span class="sxs-lookup"><span data-stu-id="52159-223">Select the target runtime from the **Target Runtime** drop-down list.</span></span> <span data-ttu-id="52159-224">Die Standardeinstellung ist `win-x86`.</span><span class="sxs-lookup"><span data-stu-id="52159-224">The default is `win-x86`.</span></span>
   * <span data-ttu-id="52159-225">Wenn Sie zusätzliche Dateien bei der Bereitstellung entfernen müssen, öffnen Sie **Dateiveröffentlichungsoptionen**, und aktivieren Sie das Kontrollkästchen, um zusätzliche Dateien am Ziel zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="52159-225">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="52159-226">Klicken Sie auf **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="52159-226">Select **Save**.</span></span>
1. <span data-ttu-id="52159-227">Sie können eine neue Website erstellen oder eine vorhandene aktualisieren, indem Sie die folgenden Anweisungen des Veröffentlichungs-Assistenten befolgen.</span><span class="sxs-lookup"><span data-stu-id="52159-227">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

#### <a name="publish-using-command-line-interface-cli-tools"></a><span data-ttu-id="52159-228">Veröffentlichen mithilfe des Befehlszeilenschnittstelle-Tools (CLI)</span><span class="sxs-lookup"><span data-stu-id="52159-228">Publish using command-line interface (CLI) tools</span></span>

1. <span data-ttu-id="52159-229">Geben Sie in der Projektdatei eine oder mehrere [Runtimebezeichner (RIDs)](/dotnet/core/rid-catalog) an.</span><span class="sxs-lookup"><span data-stu-id="52159-229">In the project file, specify one or more [Runtime Identifiers (RIDs)](/dotnet/core/rid-catalog).</span></span> <span data-ttu-id="52159-230">Verwenden Sie die Singularform `<RuntimeIdentifier>` für eine einzelne RID oder die Pluralform `<RuntimeIdentifiers>`, um eine durch Semikolons getrennte Liste von RIDs bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="52159-230">Use `<RuntimeIdentifier>` (singular) for a single RID, or use `<RuntimeIdentifiers>` (plural) to provide a semicolon-delimited list of RIDs.</span></span> <span data-ttu-id="52159-231">Im folgenden Beispiel wird die RID `win-x86` angegeben:</span><span class="sxs-lookup"><span data-stu-id="52159-231">In the following example, the `win-x86` RID is specified:</span></span>

   ```xml
   <PropertyGroup>
     <TargetFramework>netcoreapp2.1</TargetFramework>
     <RuntimeIdentifier>win-x86</RuntimeIdentifier>
   </PropertyGroup>
   ```

1. <span data-ttu-id="52159-232">Veröffentlichen Sie aus einer Befehlsshell die App in der Releasekonfiguration für die Runtime des Hosts mit dem Befehl [dotnet publish](/dotnet/core/tools/dotnet-publish).</span><span class="sxs-lookup"><span data-stu-id="52159-232">From a command shell, publish the app in Release configuration for the host's runtime with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="52159-233">Im folgenden Beispiel wird die App für die RID `win-x86` veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="52159-233">In the following example, the app is published for the `win-x86` RID.</span></span> <span data-ttu-id="52159-234">Die für die Option `--runtime` angegebene RID muss in der Eigenschaft `<RuntimeIdentifier>` (oder `<RuntimeIdentifiers>`) in der Projektdatei angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="52159-234">The RID supplied to the `--runtime` option must be provided in the `<RuntimeIdentifier>` (or `<RuntimeIdentifiers>`) property in the project file.</span></span>

   ```console
   dotnet publish --configuration Release --runtime win-x86
   ```

1. <span data-ttu-id="52159-235">Verschieben Sie den Inhalt des Verzeichnisses *bin/Release/{ZIELFRAMEWORK}/{RUNTIME-ID}/publish* auf die Site in App Service.</span><span class="sxs-lookup"><span data-stu-id="52159-235">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* directory to the site in App Service.</span></span>

### <a name="use-docker-with-web-apps-for-containers"></a><span data-ttu-id="52159-236">Verwenden von Docker mit Web-Apps für Container</span><span class="sxs-lookup"><span data-stu-id="52159-236">Use Docker with Web Apps for containers</span></span>

<span data-ttu-id="52159-237">Der [Docker-Hub](https://hub.docker.com/r/microsoft/aspnetcore/) enthält die aktuellen Images für die Docker-Vorschauversion.</span><span class="sxs-lookup"><span data-stu-id="52159-237">The [Docker Hub](https://hub.docker.com/r/microsoft/aspnetcore/) contains the latest preview Docker images.</span></span> <span data-ttu-id="52159-238">Die Images können als Basisimage verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="52159-238">The images can be used as a base image.</span></span> <span data-ttu-id="52159-239">Verwenden Sie das Image, und führen Sie wie gewohnt eine Bereitstellung für Web-Apps für Container durch.</span><span class="sxs-lookup"><span data-stu-id="52159-239">Use the image and deploy to Web Apps for Containers normally.</span></span>

## <a name="protocol-settings-https"></a><span data-ttu-id="52159-240">Protokolleinstellungen (HTTPS)</span><span class="sxs-lookup"><span data-stu-id="52159-240">Protocol settings (HTTPS)</span></span>

<span data-ttu-id="52159-241">Durch sichere Protokollbindungen können Sie ein Zertifikat festlegen, das verwendet werden soll, wenn über HTTPS auf Anforderungen geantwortet wird.</span><span class="sxs-lookup"><span data-stu-id="52159-241">Secure protocol bindings allow you specify a certificate to use when responding to requests over HTTPS.</span></span> <span data-ttu-id="52159-242">Bindungen erfordern gültige private Zertifikate (*.pfx*), die für den angegebenen Hostnamen ausgestellt wurden.</span><span class="sxs-lookup"><span data-stu-id="52159-242">Binding requires a valid private certificate (*.pfx*) issued for the specific hostname.</span></span> <span data-ttu-id="52159-243">Weitere Informationen finden Sie unter [Tutorial: Binden eines vorhandenen benutzerdefinierten SSL-Zertifikats an Azure App Service](/azure/app-service/app-service-web-tutorial-custom-ssl).</span><span class="sxs-lookup"><span data-stu-id="52159-243">For more information, see [Tutorial: Bind an existing custom SSL certificate to Azure App Service](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

## <a name="transform-webconfig"></a><span data-ttu-id="52159-244">Transformieren von web.config</span><span class="sxs-lookup"><span data-stu-id="52159-244">Transform web.config</span></span>

<span data-ttu-id="52159-245">Wenn Sie *web.config* beim Veröffentlichen transformieren müssen (z.B. Umgebungsvariablen basierend auf der Konfiguration, dem Profil oder der Umgebung festlegen müssen), finden Sie weitere Informationen unter <xref:host-and-deploy/iis/transform-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="52159-245">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="52159-246">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="52159-246">Additional resources</span></span>

* [<span data-ttu-id="52159-247">App Service-Übersicht</span><span class="sxs-lookup"><span data-stu-id="52159-247">App Service overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="52159-248">Azure App Service: The Best Place to Host your .NET Apps (Azure App Service: Ideal zum Hosten von .NET-Apps; 55-minütiges Video)</span><span class="sxs-lookup"><span data-stu-id="52159-248">Azure App Service: The Best Place to Host your .NET Apps (55-minute overview video)</span></span>](https://channel9.msdn.com/events/dotnetConf/2017/T222)
* [<span data-ttu-id="52159-249">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (Azure Friday: Azure App Service-Diagnose und -Fehlerbehebung; zwölfminütiges Video)</span><span class="sxs-lookup"><span data-stu-id="52159-249">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
* [<span data-ttu-id="52159-250">Übersicht: Azure App Service-Diagnose</span><span class="sxs-lookup"><span data-stu-id="52159-250">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* <xref:host-and-deploy/web-farm>

<span data-ttu-id="52159-251">Azure App Service auf Windows Server verwendet [Internetinformationsdienste (IIS)](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="52159-251">Azure App Service on Windows Server uses [Internet Information Services (IIS)](https://www.iis.net/).</span></span> <span data-ttu-id="52159-252">Die folgenden Artikel gehen auf die zugrunde liegende IIS-Technologie ein:</span><span class="sxs-lookup"><span data-stu-id="52159-252">The following topics pertain to the underlying IIS technology:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>
* [<span data-ttu-id="52159-253">Windows Server – IT-Administrator-Inhalt für aktuelle und frühere Versionen</span><span class="sxs-lookup"><span data-stu-id="52159-253">Windows Server - IT administrator content for current and previous releases</span></span>](/windows-server/windows-server-versions)

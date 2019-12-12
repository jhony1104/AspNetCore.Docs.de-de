---
title: Hosten und Bereitstellen von ASP.NET Core Blazor Server
author: guardrex
description: Erfahren Sie, wie Sie eine Blazor Server-App mit ASP.NET Core hosten und bereitstellen.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/server
ms.openlocfilehash: d45d355eabc53fc90bcda4cb7be22fb6a9f04541
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74944333"
---
# <a name="host-and-deploy-opno-locblazor-server"></a><span data-ttu-id="b17c5-103">Hosten und Bereitstellen von Blazor Server</span><span class="sxs-lookup"><span data-stu-id="b17c5-103">Host and deploy Blazor Server</span></span>

<span data-ttu-id="b17c5-104">Von [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="b17c5-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="b17c5-105">Hostkonfigurationswerte</span><span class="sxs-lookup"><span data-stu-id="b17c5-105">Host configuration values</span></span>

<span data-ttu-id="b17c5-106">[Blazor Server-Apps](xref:blazor/hosting-models#blazor-server) können [generische Hostkonfigurationswerte](xref:fundamentals/host/generic-host#host-configuration) akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="b17c5-106">[Blazor Server apps](xref:blazor/hosting-models#blazor-server) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="b17c5-107">Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="b17c5-107">Deployment</span></span>

<span data-ttu-id="b17c5-108">Mit dem [Blazor Server-Hostingmodell](xref:blazor/hosting-models#blazor-server) wird Blazor in einer ASP.NET Core-App auf dem Server ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="b17c5-108">Using the [Blazor Server hosting model](xref:blazor/hosting-models#blazor-server), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="b17c5-109">Benutzeroberflächenupdates, Ereignisbehandlung und JavaScript-Aufrufe werden über eine [SignalR](xref:signalr/introduction)-Verbindung verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="b17c5-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="b17c5-110">Hierfür wird ein Webserver benötigt, der eine ASP.NET Core-App hosten kann.</span><span class="sxs-lookup"><span data-stu-id="b17c5-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="b17c5-111">Visual Studio enthält die **Blazor Server-App**-Projektvorlage (oder die `blazorserverside`-Vorlage bei Verwendung des Befehls [dotnet new](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="b17c5-111">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="scalability"></a><span data-ttu-id="b17c5-112">Skalierbarkeit</span><span class="sxs-lookup"><span data-stu-id="b17c5-112">Scalability</span></span>

<span data-ttu-id="b17c5-113">Planen Sie eine Bereitstellung, um die verfügbare Infrastruktur für eine Blazor Server-App optimal zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="b17c5-113">Plan a deployment to make the best use of the available infrastructure for a Blazor Server app.</span></span> <span data-ttu-id="b17c5-114">Sehen Sie sich die folgenden Ressourcen an, um die Skalierbarkeit von Blazor Server-Apps zu behandeln:</span><span class="sxs-lookup"><span data-stu-id="b17c5-114">See the following resources to address Blazor Server app scalability:</span></span>

* <span data-ttu-id="b17c5-115">[Grundlagen von Blazor Server-Apps](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="b17c5-115">[Fundamentals of Blazor Server apps](xref:blazor/hosting-models#blazor-server)</span></span>
* <xref:security/blazor/server>

### <a name="deployment-server"></a><span data-ttu-id="b17c5-116">Bereitstellungsserver</span><span class="sxs-lookup"><span data-stu-id="b17c5-116">Deployment server</span></span>

<span data-ttu-id="b17c5-117">Wenn Sie die Skalierbarkeit eines einzelnen Servers in Erwägung ziehen (zentrales Hochskalieren), ist der für eine App verfügbare Arbeitsspeicher wahrscheinlich die erste Ressource, die die App erschöpft, wenn sich die Benutzeranforderungen erhöhen.</span><span class="sxs-lookup"><span data-stu-id="b17c5-117">When considering the scalability of a single server (scale up), the memory available to an app is likely the first resource that the app will exhaust as user demands increase.</span></span> <span data-ttu-id="b17c5-118">Der verfügbare Arbeitsspeicher auf dem Server wirkt sich auf Folgendes aus:</span><span class="sxs-lookup"><span data-stu-id="b17c5-118">The available memory on the server affects the:</span></span>

* <span data-ttu-id="b17c5-119">Anzahl der aktiven Verbindungen, die ein Server unterstützen kann.</span><span class="sxs-lookup"><span data-stu-id="b17c5-119">Number of active circuits that a server can support.</span></span>
* <span data-ttu-id="b17c5-120">Benutzeroberflächenlatenz auf dem Client.</span><span class="sxs-lookup"><span data-stu-id="b17c5-120">UI latency on the client.</span></span>

<span data-ttu-id="b17c5-121">Anleitungen zum Erstellen sicherer und skalierbarer Blazor Server-Apps finden Sie unter <xref:security/blazor/server>.</span><span class="sxs-lookup"><span data-stu-id="b17c5-121">For guidance on building secure and scalable Blazor server apps, see <xref:security/blazor/server>.</span></span>

<span data-ttu-id="b17c5-122">Jede Verbindung verwendet ungefähr 250 KB Arbeitsspeicher für eine minimale App im *Hello World*-Stil.</span><span class="sxs-lookup"><span data-stu-id="b17c5-122">Each circuit uses approximately 250 KB of memory for a minimal *Hello World*-style app.</span></span> <span data-ttu-id="b17c5-123">Die Größe einer Verbindung hängt vom App-Code und den Zustandsverwaltungsanforderungen der einzelnen Komponenten ab.</span><span class="sxs-lookup"><span data-stu-id="b17c5-123">The size of a circuit depends on the app's code and the state maintenance requirements associated with each component.</span></span> <span data-ttu-id="b17c5-124">Sie sollten die Ressourcenanforderungen während der Entwicklung für Ihre App und die Infrastruktur messen, aber die folgende Baseline kann ein Ausgangspunkt zur Planung des Bereitstellungsziels sein: Wenn Sie davon ausgehen, dass Ihre App 5.000 gleichzeitige Benutzer unterstützt, sollten Sie erwägen, mindestens 1,3 GB Serverarbeitsspeicher (oder ~273 KB pro Benutzer) für die App einzukalkulieren.</span><span class="sxs-lookup"><span data-stu-id="b17c5-124">We recommend that you measure resource demands during development for your app and infrastructure, but the following baseline can be a starting point in planning your deployment target: If you expect your app to support 5,000 concurrent users, consider budgeting at least 1.3 GB of server memory to the app (or ~273 KB per user).</span></span>

### <a name="opno-locsignalr-configuration"></a>SignalR<span data-ttu-id="b17c5-125">-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="b17c5-125"> configuration</span></span>

Blazor<span data-ttu-id="b17c5-126"> Server-Apps verwenden ASP.NET Core SignalR, um mit dem Browser zu kommunizieren.</span><span class="sxs-lookup"><span data-stu-id="b17c5-126"> Server apps use ASP.NET Core SignalR to communicate with the browser.</span></span> <span data-ttu-id="b17c5-127">[Die Hosting- und Skalierungsbedingungen von SignalR](xref:signalr/publish-to-azure-web-app) gelten für Blazor Server-Apps.</span><span class="sxs-lookup"><span data-stu-id="b17c5-127">[SignalR's hosting and scaling conditions](xref:signalr/publish-to-azure-web-app) apply to Blazor Server apps.</span></span>

Blazor<span data-ttu-id="b17c5-128"> funktioniert am besten, wenn WebSockets aufgrund geringerer Latenz und wegen Zuverlässigkeit und [Sicherheit](xref:signalr/security) zum SignalR-Transport verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="b17c5-128"> works best when using WebSockets as the SignalR transport due to lower latency, reliability, and [security](xref:signalr/security).</span></span> <span data-ttu-id="b17c5-129">SignalR verwendet Long Polling, wenn WebSockets nicht verfügbar oder die App explizit für die Verwendung von Long Polling konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="b17c5-129">Long Polling is used by SignalR when WebSockets isn't available or when the app is explicitly configured to use Long Polling.</span></span> <span data-ttu-id="b17c5-130">Konfigurieren Sie die App bei Bereitstellung für Azure App Service in den Einstellungen für den Dienst im Azure-Portal für die Verwendung von WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b17c5-130">When deploying to Azure App Service, configure the app to use WebSockets in the Azure portal settings for the service.</span></span> <span data-ttu-id="b17c5-131">Weitere Informationen zum Konfigurieren der App für Azure App Service finden Sie in den [Richtlinien für die SignalR-Veröffentlichung](xref:signalr/publish-to-azure-web-app).</span><span class="sxs-lookup"><span data-stu-id="b17c5-131">For details on configuring the app for Azure App Service, see the [SignalR publishing guidelines](xref:signalr/publish-to-azure-web-app).</span></span>

#### <a name="azure-opno-locsignalr-service"></a><span data-ttu-id="b17c5-132">Azure SignalR Service</span><span class="sxs-lookup"><span data-stu-id="b17c5-132">Azure SignalR Service</span></span>

<span data-ttu-id="b17c5-133">Sie sollten [Azure SignalR Service](/azure/azure-signalr) für Blazor Server-Apps verwenden.</span><span class="sxs-lookup"><span data-stu-id="b17c5-133">We recommend using the [Azure SignalR Service](/azure/azure-signalr) for Blazor Server apps.</span></span> <span data-ttu-id="b17c5-134">Der Dienst ermöglicht das zentrale Hochskalieren einer Blazor Server-App auf eine große Anzahl gleichzeitiger SignalR-Verbindungen.</span><span class="sxs-lookup"><span data-stu-id="b17c5-134">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="b17c5-135">Außerdem tragen die globale Reichweite und die Hochleistungsrechenzentren von SignalR Service erheblich zur Verringerung der geografiebedingten Latenz bei.</span><span class="sxs-lookup"><span data-stu-id="b17c5-135">In addition, the SignalR service's global reach and high-performance data centers significantly aid in reducing latency due to geography.</span></span> <span data-ttu-id="b17c5-136">So konfigurieren Sie eine App (und stellen optional Azure SignalR Service bereit)</span><span class="sxs-lookup"><span data-stu-id="b17c5-136">To configure an app (and optionally provision) the Azure SignalR Service:</span></span>

1. <span data-ttu-id="b17c5-137">Aktivieren Sie die Unterstützung von *persistenten Sitzungen* des Diensts, damit Clients [beim Vorabrendering an denselben Server umgeleitet werden](xref:blazor/hosting-models#reconnection-to-the-same-server).</span><span class="sxs-lookup"><span data-stu-id="b17c5-137">Enable the service to support *sticky sessions*, where clients are [redirected back to the same server when prerendering](xref:blazor/hosting-models#reconnection-to-the-same-server).</span></span> <span data-ttu-id="b17c5-138">Legen Sie die `ServerStickyMode`-Option oder den Konfigurationswert auf `Required` fest.</span><span class="sxs-lookup"><span data-stu-id="b17c5-138">Set the `ServerStickyMode` option or configuration value to `Required`.</span></span> <span data-ttu-id="b17c5-139">In der Regel erstellt eine App die Konfiguration mithilfe von **einem** der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="b17c5-139">Typically, an app creates the configuration using **one** of the following approaches:</span></span>

   * <span data-ttu-id="b17c5-140">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b17c5-140">`Startup.ConfigureServices`:</span></span>
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * <span data-ttu-id="b17c5-141">Konfiguration (verwenden Sie **einen** der folgenden Ansätze):</span><span class="sxs-lookup"><span data-stu-id="b17c5-141">Configuration (use **one** of the following approaches):</span></span>
  
     * <span data-ttu-id="b17c5-142">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="b17c5-142">*appsettings.json*:</span></span>

       ```json
       "Azure:SignalR:ServerStickyMode": "Required"
       ```

     * <span data-ttu-id="b17c5-143">**Konfiguration** > **Anwendungseinstellungen** des App-Diensts im Azure-Portal (**Name**: `Azure:SignalR:ServerStickyMode`, **Wert**: `Required`).</span><span class="sxs-lookup"><span data-stu-id="b17c5-143">The app service's **Configuration** > **Application settings** in the Azure portal (**Name**: `Azure:SignalR:ServerStickyMode`, **Value**: `Required`).</span></span>

1. <span data-ttu-id="b17c5-144">Erstellen Sie ein Veröffentlichungsprofil für Azure-Apps in Visual Studio für die Blazor Server-App.</span><span class="sxs-lookup"><span data-stu-id="b17c5-144">Create an Azure Apps publish profile in Visual Studio for the Blazor Server app.</span></span>
1. <span data-ttu-id="b17c5-145">Fügen Sie dem Profil die **Azure SignalR Service**-Abhängigkeit hinzu.</span><span class="sxs-lookup"><span data-stu-id="b17c5-145">Add the **Azure SignalR Service** dependency to the profile.</span></span> <span data-ttu-id="b17c5-146">Wenn das Azure-Abonnement nicht über eine bereits vorhandene Azure SignalR Service-Instanz verfügt, die der App zugewiesen werden soll, wählen Sie **Neue Azure SignalR Service-Instanz erstellen** aus, um eine neue Dienstinstanz bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="b17c5-146">If the Azure subscription doesn't have a pre-existing Azure SignalR Service instance to assign to the app, select **Create a new Azure SignalR Service instance** to provision a new service instance.</span></span>
1. <span data-ttu-id="b17c5-147">Veröffentlichen der App in Azure</span><span class="sxs-lookup"><span data-stu-id="b17c5-147">Publish the app to Azure.</span></span>

#### <a name="iis"></a><span data-ttu-id="b17c5-148">IIS</span><span class="sxs-lookup"><span data-stu-id="b17c5-148">IIS</span></span>

<span data-ttu-id="b17c5-149">Bei der Verwendung von IIS sind persistente Sitzungen mit Routing von Anwendungsanforderungen aktiviert.</span><span class="sxs-lookup"><span data-stu-id="b17c5-149">When using IIS, sticky sessions are enabled with Application Request Routing.</span></span> <span data-ttu-id="b17c5-150">Weitere Informationen finden Sie unter [HTTP-Lastenausgleiche mithilfe von Routing von Anwendungsanforderungen](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span><span class="sxs-lookup"><span data-stu-id="b17c5-150">For more information, see [HTTP Load Balancing using Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="kubernetes"></a><span data-ttu-id="b17c5-151">Kubernetes</span><span class="sxs-lookup"><span data-stu-id="b17c5-151">Kubernetes</span></span>

<span data-ttu-id="b17c5-152">Erstellen Sie eine Eingangsdefinition mithilfe der folgenden [Kubernetes-Anmerkungen für persistente Sitzungen:](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/)</span><span class="sxs-lookup"><span data-stu-id="b17c5-152">Create an ingress definition with the following [Kubernetes annotations for sticky sessions](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span></span>

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: <ingress-name>
  annotations:
    nginx.ingress.kubernetes.io/affinity: "cookie"
    nginx.ingress.kubernetes.io/session-cookie-name: "affinity"
    nginx.ingress.kubernetes.io/session-cookie-expires: "14400"
    nginx.ingress.kubernetes.io/session-cookie-max-age: "14400"
```

### <a name="measure-network-latency"></a><span data-ttu-id="b17c5-153">Messen der Netzwerklatenz</span><span class="sxs-lookup"><span data-stu-id="b17c5-153">Measure network latency</span></span>

<span data-ttu-id="b17c5-154">Mit [JS-Interop](xref:blazor/javascript-interop) kann die Netzwerklatenz gemessen werden, wie im folgenden Beispiel veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="b17c5-154">[JS interop](xref:blazor/javascript-interop) can be used to measure network latency, as the following example demonstrates:</span></span>

```razor
@inject IJSRuntime JS

@if (latency is null)
{
    <span>Calculating...</span>
}
else
{
    <span>@(latency.Value.TotalMilliseconds)ms</span>
}

@code
{
    private DateTime startTime;
    private TimeSpan? latency;

    protected override async Task OnInitializedAsync()
    {
        startTime = DateTime.UtcNow;
        var _ = await JS.InvokeAsync<string>("toString");
        latency = DateTime.UtcNow - startTime;
    }
}
```

<span data-ttu-id="b17c5-155">Für eine sinnvolle Benutzeroberfläche empfehlen wir eine dauerhafte Latenz von höchstens 250 ms.</span><span class="sxs-lookup"><span data-stu-id="b17c5-155">For a reasonable UI experience, we recommend a sustained UI latency of 250ms or less.</span></span>

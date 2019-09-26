---
title: Hosten und Bereitstellen von ASP.NET Core Blazor Server
author: guardrex
description: Erfahren Sie, wie Sie eine Blazor Server-App mit ASP.NET Core hosten und bereitstellen.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: host-and-deploy/blazor/server
ms.openlocfilehash: aedef7fe695dd4a0cbf04d3f3e9947f33f7afa40
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211606"
---
# <a name="host-and-deploy-blazor-server"></a><span data-ttu-id="0ff5a-103">Hosten und Bereitstellen von Blazor Server</span><span class="sxs-lookup"><span data-stu-id="0ff5a-103">Host and deploy Blazor Server</span></span>

<span data-ttu-id="0ff5a-104">Von [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="0ff5a-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="0ff5a-105">Hostkonfigurationswerte</span><span class="sxs-lookup"><span data-stu-id="0ff5a-105">Host configuration values</span></span>

<span data-ttu-id="0ff5a-106">[Blazor Server-Apps](xref:blazor/hosting-models#blazor-server) können [generische Hostkonfigurationswerte](xref:fundamentals/host/generic-host#host-configuration) akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="0ff5a-106">[Blazor Server apps](xref:blazor/hosting-models#blazor-server) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="0ff5a-107">Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="0ff5a-107">Deployment</span></span>

<span data-ttu-id="0ff5a-108">Mit dem [Blazor Server-Hostingmodell](xref:blazor/hosting-models#blazor-server) wird Blazor in einer ASP.NET Core-App auf dem Server ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="0ff5a-108">Using the [Blazor Server hosting model](xref:blazor/hosting-models#blazor-server), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="0ff5a-109">Benutzeroberflächenupdates, Ereignisbehandlung und JavaScript-Aufrufe werden über eine [SignalR](xref:signalr/introduction)-Verbindung verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="0ff5a-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="0ff5a-110">Hierfür wird ein Webserver benötigt, der eine ASP.NET Core-App hosten kann.</span><span class="sxs-lookup"><span data-stu-id="0ff5a-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="0ff5a-111">Visual Studio enthält die **Blazor-Server-App**-Projektvorlage (oder die `blazorserverside`-Vorlage bei Verwendung des Befehls [dotnet new](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="0ff5a-111">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="scalability"></a><span data-ttu-id="0ff5a-112">Skalierbarkeit</span><span class="sxs-lookup"><span data-stu-id="0ff5a-112">Scalability</span></span>

<span data-ttu-id="0ff5a-113">Planen Sie eine Bereitstellung, um die verfügbare Infrastruktur für eine Blazor-Server-App optimal zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="0ff5a-113">Plan a deployment to make the best use of the available infrastructure for a Blazor Server app.</span></span> <span data-ttu-id="0ff5a-114">Sehen Sie sich die folgenden Ressourcen an, um die Skalierbarkeit von Blazor-Server-Apps zu behandeln:</span><span class="sxs-lookup"><span data-stu-id="0ff5a-114">See the following resources to address Blazor Server app scalability:</span></span>

* [<span data-ttu-id="0ff5a-115">Blazor Server</span><span class="sxs-lookup"><span data-stu-id="0ff5a-115">Fundamentals of Blazor Server apps</span></span>](xref:blazor/hosting-models#blazor-server)
* <xref:security/blazor/server>

### <a name="deployment-server"></a><span data-ttu-id="0ff5a-116">Bereitstellungsserver</span><span class="sxs-lookup"><span data-stu-id="0ff5a-116">Deployment server</span></span>

<span data-ttu-id="0ff5a-117">Wenn Sie die Skalierbarkeit eines einzelnen Servers in Erwägung ziehen (zentrales Hochskalieren), ist der für eine App verfügbare Arbeitsspeicher wahrscheinlich die erste Ressource, die die App erschöpft, wenn sich die Benutzeranforderungen erhöhen.</span><span class="sxs-lookup"><span data-stu-id="0ff5a-117">When considering the scalability of a single server (scale up), the memory available to an app is likely the first resource that the app will exhaust as user demands increase.</span></span> <span data-ttu-id="0ff5a-118">Der verfügbare Arbeitsspeicher auf dem Server wirkt sich auf Folgendes aus:</span><span class="sxs-lookup"><span data-stu-id="0ff5a-118">The available memory on the server affects the:</span></span>

* <span data-ttu-id="0ff5a-119">Anzahl der aktiven Verbindungen, die ein Server unterstützen kann.</span><span class="sxs-lookup"><span data-stu-id="0ff5a-119">Number of active circuits that a server can support.</span></span>
* <span data-ttu-id="0ff5a-120">Benutzeroberflächenlatenz auf dem Client.</span><span class="sxs-lookup"><span data-stu-id="0ff5a-120">UI latency on the client.</span></span>

<span data-ttu-id="0ff5a-121">Anleitungen zum Erstellen sicherer und skalierbarer Blazor-Server-Apps finden Sie unter <xref:security/blazor/server>.</span><span class="sxs-lookup"><span data-stu-id="0ff5a-121">For guidance on building secure and scalable Blazor server apps, see <xref:security/blazor/server>.</span></span>

<span data-ttu-id="0ff5a-122">Jede Verbindung verwendet ungefähr 250 KB Arbeitsspeicher für eine minimale App im *Hello World*-Stil.</span><span class="sxs-lookup"><span data-stu-id="0ff5a-122">Each circuit uses approximately 250 KB of memory for a minimal *Hello World*-style app.</span></span> <span data-ttu-id="0ff5a-123">Die Größe einer Verbindung hängt vom App-Code und den Zustandsverwaltungsanforderungen der einzelnen Komponenten ab.</span><span class="sxs-lookup"><span data-stu-id="0ff5a-123">The size of a circuit depends on the app's code and the state maintenance requirements associated with each component.</span></span> <span data-ttu-id="0ff5a-124">Sie sollten die Ressourcenanforderungen während der Entwicklung für Ihre App und die Infrastruktur messen, aber die folgende Baseline kann ein Ausgangspunkt zur Planung des Bereitstellungsziels sein: Wenn Sie davon ausgehen, dass Ihre App 5.000 gleichzeitige Benutzer unterstützt, sollten Sie erwägen, mindestens 1,3 GB Serverarbeitsspeicher (oder ~273 KB pro Benutzer) für die App einzukalkulieren.</span><span class="sxs-lookup"><span data-stu-id="0ff5a-124">We recommend that you measure resource demands during development for your app and infrastructure, but the following baseline can be a starting point in planning your deployment target: If you expect your app to support 5,000 concurrent users, consider budgeting at least 1.3 GB of server memory to the app (or ~273 KB per user).</span></span>

### <a name="signalr-configuration"></a><span data-ttu-id="0ff5a-125">SignalR-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="0ff5a-125">SignalR configuration</span></span>

<span data-ttu-id="0ff5a-126">Blazor-Server-Apps verwenden ASP.NET Core SignalR, um mit dem Browser zu kommunizieren.</span><span class="sxs-lookup"><span data-stu-id="0ff5a-126">Blazor Server apps use ASP.NET Core SignalR to communicate with the browser.</span></span> <span data-ttu-id="0ff5a-127">[Die Hosting- und Skalierungsbedingungen von SignalR](xref:signalr/publish-to-azure-web-app) gelten für Blazor-Server-Apps.</span><span class="sxs-lookup"><span data-stu-id="0ff5a-127">[SignalR's hosting and scaling conditions](xref:signalr/publish-to-azure-web-app) apply to Blazor Server apps.</span></span>

<span data-ttu-id="0ff5a-128">Blazor funktioniert am besten, wenn WebSockets aufgrund geringerer Latenz und wegen Zuverlässigkeit und [Sicherheit](xref:signalr/security) zum SignalR-Transport verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="0ff5a-128">Blazor works best when using WebSockets as the SignalR transport due to lower latency, reliability, and [security](xref:signalr/security).</span></span> <span data-ttu-id="0ff5a-129">SignalR verwendet Long Polling, wenn WebSockets nicht verfügbar oder die App explizit für die Verwendung von Long Polling konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="0ff5a-129">Long Polling is used by SignalR when WebSockets isn't available or when the app is explicitly configured to use Long Polling.</span></span> <span data-ttu-id="0ff5a-130">Konfigurieren Sie die App bei Bereitstellung für Azure App Service in den Einstellungen für den Dienst im Azure-Portal für die Verwendung von WebSockets.</span><span class="sxs-lookup"><span data-stu-id="0ff5a-130">When deploying to Azure App Service, configure the app to use WebSockets in the Azure portal settings for the service.</span></span> <span data-ttu-id="0ff5a-131">Weitere Informationen zum Konfigurieren der App für Azure App Service finden Sie in den [Richtlinien für die SignalR-Veröffentlichung](xref:signalr/publish-to-azure-web-app).</span><span class="sxs-lookup"><span data-stu-id="0ff5a-131">For details on configuring the app for Azure App Service, see the [SignalR publishing guidelines](xref:signalr/publish-to-azure-web-app).</span></span>

<span data-ttu-id="0ff5a-132">Sie sollten den [Azure SignalR-Dienst](/azure/azure-signalr) für Blazor-Server-Apps verwenden.</span><span class="sxs-lookup"><span data-stu-id="0ff5a-132">We recommend using the [Azure SignalR Service](/azure/azure-signalr) for Blazor Server apps.</span></span> <span data-ttu-id="0ff5a-133">Der Dienst ermöglicht das zentrale Hochskalieren einer Blazor-Server-App auf eine große Anzahl gleichzeitiger SignalR-Verbindungen.</span><span class="sxs-lookup"><span data-stu-id="0ff5a-133">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="0ff5a-134">Außerdem tragen die globale Reichweite und die Hochleistungsrechenzentren des SignalR-Diensts erheblich zur Verringerung der geografiebedingten Latenz bei.</span><span class="sxs-lookup"><span data-stu-id="0ff5a-134">In addition, the SignalR service's global reach and high-performance data centers significantly aid in reducing latency due to geography.</span></span>

### <a name="measure-network-latency"></a><span data-ttu-id="0ff5a-135">Messen der Netzwerklatenz</span><span class="sxs-lookup"><span data-stu-id="0ff5a-135">Measure network latency</span></span>

<span data-ttu-id="0ff5a-136">Mit [JS-Interop](xref:blazor/javascript-interop) kann die Netzwerklatenz gemessen werden, wie im folgenden Beispiel veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="0ff5a-136">[JS interop](xref:blazor/javascript-interop) can be used to measure network latency, as the following example demonstrates:</span></span>

```cshtml
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

<span data-ttu-id="0ff5a-137">Für eine sinnvolle Benutzeroberfläche empfehlen wir eine dauerhafte Latenz von höchstens 250 ms.</span><span class="sxs-lookup"><span data-stu-id="0ff5a-137">For a reasonable UI experience, we recommend a sustained UI latency of 250ms or less.</span></span>

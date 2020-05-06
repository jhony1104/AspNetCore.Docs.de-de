---
title: Host ASP.net Core SignalR in Hintergrund Diensten
author: bradygaster
description: Erfahren Sie, wie Sie nach SignalR richten von .net Core-backgroundservice-Klassen an Clients senden.
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/background-services
ms.openlocfilehash: d5f1668d601f520939956985e46c62f3a5bdfcfa
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777292"
---
# <a name="host-aspnet-core-signalr-in-background-services"></a><span data-ttu-id="b15ec-103">Host ASP.net Core SignalR in Hintergrund Diensten</span><span class="sxs-lookup"><span data-stu-id="b15ec-103">Host ASP.NET Core SignalR in background services</span></span>

<span data-ttu-id="b15ec-104">Von [Brady Gastern](https://twitter.com/bradygaster)</span><span class="sxs-lookup"><span data-stu-id="b15ec-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="b15ec-105">Dieser Artikel enthält Anleitungen für:</span><span class="sxs-lookup"><span data-stu-id="b15ec-105">This article provides guidance for:</span></span>

* <span data-ttu-id="b15ec-106">Hosten SignalR von Hubs mit einem Hintergrund Arbeitsprozess, der mit ASP.net Core gehostet wird.</span><span class="sxs-lookup"><span data-stu-id="b15ec-106">Hosting SignalR Hubs using a background worker process hosted with ASP.NET Core.</span></span>
* <span data-ttu-id="b15ec-107">Senden von Nachrichten an verbundene Clients aus einem .net Core- [backgroundservice](xref:Microsoft.Extensions.Hosting.BackgroundService).</span><span class="sxs-lookup"><span data-stu-id="b15ec-107">Sending messages to connected clients from within a .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).</span></span>

<span data-ttu-id="b15ec-108">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(Vorgehensweise zum herunterladen)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="b15ec-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="enable-signalr-in-startup"></a><span data-ttu-id="b15ec-109">Beim SignalR Start aktivieren</span><span class="sxs-lookup"><span data-stu-id="b15ec-109">Enable SignalR in startup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b15ec-110">Das Hosting SignalR von ASP.net Core Hubs im Kontext eines hintergrundworkerprozesses ist identisch mit dem Hosting eines Hubs in einer ASP.net Core Web-App.</span><span class="sxs-lookup"><span data-stu-id="b15ec-110">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="b15ec-111">In der `Startup.ConfigureServices` -Methode fügt `services.AddSignalR` der Aufruf von die erforderlichen Dienste der Ebene der ASP.net Core Abhängigkeitsinjektion (di SignalR) hinzu, um zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="b15ec-111">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="b15ec-112">In `Startup.Configure`wird die `MapHub` -Methode im `UseEndpoints` Rückruf aufgerufen, um die Hub-Endpunkte in der ASP.net Core Anforderungs Pipeline zu verbinden.</span><span class="sxs-lookup"><span data-stu-id="b15ec-112">In `Startup.Configure`, the `MapHub` method is called in the `UseEndpoints` callback to connect the Hub endpoints in the ASP.NET Core request pipeline.</span></span>

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSignalR();
        services.AddHostedService<Worker>();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseRouting();
        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHub<ClockHub>("/hubs/clock");
        });
    }
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="b15ec-113">Das Hosting SignalR von ASP.net Core Hubs im Kontext eines hintergrundworkerprozesses ist identisch mit dem Hosting eines Hubs in einer ASP.net Core Web-App.</span><span class="sxs-lookup"><span data-stu-id="b15ec-113">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="b15ec-114">In der `Startup.ConfigureServices` -Methode fügt `services.AddSignalR` der Aufruf von die erforderlichen Dienste der Ebene der ASP.net Core Abhängigkeitsinjektion (di SignalR) hinzu, um zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="b15ec-114">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="b15ec-115">In `Startup.Configure`wird die `UseSignalR` -Methode aufgerufen, um die Hub-Endpunkte in der ASP.net Core Anforderungs Pipeline zu verbinden.</span><span class="sxs-lookup"><span data-stu-id="b15ec-115">In `Startup.Configure`, the `UseSignalR` method is called to connect the Hub endpoint(s) in the ASP.NET Core request pipeline.</span></span>

[!code-csharp[Startup](background-service/sample/Server/Startup.cs?name=Startup)]

::: moniker-end

<span data-ttu-id="b15ec-116">Im vorherigen Beispiel implementiert die `ClockHub` -Klasse die `Hub<T>` -Klasse, um einen stark typisierten Hub zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="b15ec-116">In the preceding example, the `ClockHub` class implements the `Hub<T>` class to create a strongly typed Hub.</span></span> <span data-ttu-id="b15ec-117">Der `ClockHub` wurde in der `Startup` -Klasse so konfiguriert, dass er auf Anforderungen am `/hubs/clock`Endpunkt antwortet.</span><span class="sxs-lookup"><span data-stu-id="b15ec-117">The `ClockHub` has been configured in the `Startup` class to respond to requests at the endpoint `/hubs/clock`.</span></span>

<span data-ttu-id="b15ec-118">Weitere Informationen zu stark typisierten Hubs finden Sie unter [Verwenden von SignalR Hubs in für ASP.net Core](xref:signalr/hubs#strongly-typed-hubs).</span><span class="sxs-lookup"><span data-stu-id="b15ec-118">For more information on strongly typed Hubs, see [Use hubs in SignalR for ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).</span></span>

> [!NOTE]
> <span data-ttu-id="b15ec-119">Diese Funktion ist nicht auf die [Hub\<t->](xref:Microsoft.AspNetCore.SignalR.Hub`1) Klasse beschränkt.</span><span class="sxs-lookup"><span data-stu-id="b15ec-119">This functionality isn't limited to the [Hub\<T>](xref:Microsoft.AspNetCore.SignalR.Hub`1) class.</span></span> <span data-ttu-id="b15ec-120">Jede Klasse, die von [Hub](xref:Microsoft.AspNetCore.SignalR.Hub)erbt, z. [b. dynamichub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), funktioniert ebenfalls.</span><span class="sxs-lookup"><span data-stu-id="b15ec-120">Any class that inherits from [Hub](xref:Microsoft.AspNetCore.SignalR.Hub), such as [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), will also work.</span></span>

[!code-csharp[Startup](background-service/sample/Server/ClockHub.cs?name=ClockHub)]

<span data-ttu-id="b15ec-121">Die vom stark typisierten `ClockHub` verwendete Schnittstelle ist `IClock` die-Schnittstelle.</span><span class="sxs-lookup"><span data-stu-id="b15ec-121">The interface used by the strongly typed `ClockHub` is the `IClock` interface.</span></span>

[!code-csharp[Startup](background-service/sample/HubServiceInterfaces/IClock.cs?name=IClock)]

## <a name="call-a-signalr-hub-from-a-background-service"></a><span data-ttu-id="b15ec-122">Einen SignalR Hub über einen Hintergrunddienst aufzurufen</span><span class="sxs-lookup"><span data-stu-id="b15ec-122">Call a SignalR Hub from a background service</span></span>

<span data-ttu-id="b15ec-123">Während des Starts wird `Worker` die Klasse, `BackgroundService`a, mithilfe `AddHostedService`von aktiviert.</span><span class="sxs-lookup"><span data-stu-id="b15ec-123">During startup, the `Worker` class, a `BackgroundService`, is enabled using `AddHostedService`.</span></span>

```csharp
services.AddHostedService<Worker>();
```

<span data-ttu-id="b15ec-124">Da SignalR auch in der `Startup` Phase aktiviert ist, in der jeder Hub an einen einzelnen Endpunkt in der HTTP-Anforderungs Pipeline ASP.net Core angefügt ist, wird jeder Hub durch ein `IHubContext<T>` auf dem Server dargestellt.</span><span class="sxs-lookup"><span data-stu-id="b15ec-124">Since SignalR is also enabled up during the `Startup` phase, in which each Hub is attached to an individual endpoint in ASP.NET Core's HTTP request pipeline, each Hub is represented by an `IHubContext<T>` on the server.</span></span> <span data-ttu-id="b15ec-125">Mithilfe der di-Features von ASP.net Core können andere Klassen, die von der hostingschicht `BackgroundService` instanziiert werden (z. b Razor . Klassen, MVC-Controller Klassen oder Seiten Modelle), Verweise auf `IHubContext<ClockHub, IClock>` serverseitige Hubs erhalten, indem Sie Instanzen von während der Konstruktion akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="b15ec-125">Using ASP.NET Core's DI features, other classes instantiated by the hosting layer, like `BackgroundService` classes, MVC Controller classes, or Razor page models, can get references to server-side Hubs by accepting instances of `IHubContext<ClockHub, IClock>` during construction.</span></span>

[!code-csharp[Startup](background-service/sample/Server/Worker.cs?name=Worker)]

<span data-ttu-id="b15ec-126">Wenn die `ExecuteAsync` -Methode im Hintergrunddienst iterativ aufgerufen wird, werden das aktuelle Datum und die aktuelle Uhrzeit des Servers über das `ClockHub`an die verbundenen Clients gesendet.</span><span class="sxs-lookup"><span data-stu-id="b15ec-126">As the `ExecuteAsync` method is called iteratively in the background service, the server's current date and time are sent to the connected clients using the `ClockHub`.</span></span>

## <a name="react-to-signalr-events-with-background-services"></a><span data-ttu-id="b15ec-127">Reagieren auf SignalR Ereignisse mit Hintergrund Diensten</span><span class="sxs-lookup"><span data-stu-id="b15ec-127">React to SignalR events with background services</span></span>

<span data-ttu-id="b15ec-128">Wie eine Einzelseiten SignalR -APP <xref:signalr/dotnet-client>, die den JavaScript-Client für oder eine .net-Desktop-App mithilfe von verwenden `BackgroundService` kann `IHostedService` , kann eine-oder-Implementierung auch SignalR verwendet werden, um eine Verbindung mit Hubs herzustellen und auf Ereignisse zu reagieren.</span><span class="sxs-lookup"><span data-stu-id="b15ec-128">Like a Single Page App using the JavaScript client for SignalR or a .NET desktop app can do using the using the <xref:signalr/dotnet-client>, a `BackgroundService` or `IHostedService` implementation can also be used to connect to SignalR Hubs and respond to events.</span></span>

<span data-ttu-id="b15ec-129">Die `ClockHubClient` -Klasse implementiert die `IClock` -Schnittstelle `IHostedService` und die-Schnittstelle.</span><span class="sxs-lookup"><span data-stu-id="b15ec-129">The `ClockHubClient` class implements both the `IClock` interface and the `IHostedService` interface.</span></span> <span data-ttu-id="b15ec-130">Auf diese Weise kann Sie während `Startup` der kontinuierlichen Durchführung von aktiviert werden und auf Hub-Ereignisse vom Server reagieren.</span><span class="sxs-lookup"><span data-stu-id="b15ec-130">This way it can be enabled during `Startup` to run continuously and respond to Hub events from the server.</span></span>

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

<span data-ttu-id="b15ec-131">Während der `ClockHubClient` Initialisierung wird von eine Instanz von erstellt `HubConnection` und die `IClock.ShowTime` -Methode als Handler für das- `ShowTime` Ereignis des Hubs aktiviert.</span><span class="sxs-lookup"><span data-stu-id="b15ec-131">During initialization, the `ClockHubClient` creates an instance of a `HubConnection` and enables the `IClock.ShowTime` method as the handler for the Hub's `ShowTime` event.</span></span>

[!code-csharp[The ClockHubClient constructor](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

<span data-ttu-id="b15ec-132">In der `IHostedService.StartAsync` -Implementierung wird `HubConnection` der asynchron gestartet.</span><span class="sxs-lookup"><span data-stu-id="b15ec-132">In the `IHostedService.StartAsync` implementation, the `HubConnection` is started asynchronously.</span></span>

[!code-csharp[StartAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

<span data-ttu-id="b15ec-133">Während der `IHostedService.StopAsync` -Methode wird `HubConnection` der asynchron freigegeben.</span><span class="sxs-lookup"><span data-stu-id="b15ec-133">During the `IHostedService.StopAsync` method, the `HubConnection` is disposed of asynchronously.</span></span>

[!code-csharp[StopAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

## <a name="additional-resources"></a><span data-ttu-id="b15ec-134">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="b15ec-134">Additional resources</span></span>

* [<span data-ttu-id="b15ec-135">Erste Schritte</span><span class="sxs-lookup"><span data-stu-id="b15ec-135">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="b15ec-136">Hubs</span><span class="sxs-lookup"><span data-stu-id="b15ec-136">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="b15ec-137">Veröffentlichen in Azure</span><span class="sxs-lookup"><span data-stu-id="b15ec-137">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="b15ec-138">Stark typisierte Hubs</span><span class="sxs-lookup"><span data-stu-id="b15ec-138">Strongly typed Hubs</span></span>](xref:signalr/hubs#strongly-typed-hubs)

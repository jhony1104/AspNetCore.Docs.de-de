---
title: Host ASP.net Core SignalR in Hintergrund Diensten
author: bradygaster
description: Erfahren Sie, wie Sie Nachrichten von .net Core-backgroundservice-Klassen an SignalR Clients senden.
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/background-services
ms.openlocfilehash: 86319cc93febab18c29e2fb6366cef0d025943ba
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651583"
---
# <a name="host-aspnet-core-opno-locsignalr-in-background-services"></a><span data-ttu-id="7fd9b-103">Host ASP.net Core SignalR in Hintergrund Diensten</span><span class="sxs-lookup"><span data-stu-id="7fd9b-103">Host ASP.NET Core SignalR in background services</span></span>

<span data-ttu-id="7fd9b-104">Von [Brady Gastern](https://twitter.com/bradygaster)</span><span class="sxs-lookup"><span data-stu-id="7fd9b-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="7fd9b-105">Dieser Artikel enthält Anleitungen für:</span><span class="sxs-lookup"><span data-stu-id="7fd9b-105">This article provides guidance for:</span></span>

* <span data-ttu-id="7fd9b-106">Hosten von SignalR Hubs mit einem Hintergrund Arbeitsprozess, der mit ASP.net Core gehostet wird.</span><span class="sxs-lookup"><span data-stu-id="7fd9b-106">Hosting SignalR Hubs using a background worker process hosted with ASP.NET Core.</span></span>
* <span data-ttu-id="7fd9b-107">Senden von Nachrichten an verbundene Clients aus einem .net Core- [backgroundservice](xref:Microsoft.Extensions.Hosting.BackgroundService).</span><span class="sxs-lookup"><span data-stu-id="7fd9b-107">Sending messages to connected clients from within a .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).</span></span>

<span data-ttu-id="7fd9b-108">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(Vorgehensweise zum herunterladen)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="7fd9b-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="enable-opno-locsignalr-in-startup"></a><span data-ttu-id="7fd9b-109">SignalR beim Start aktivieren</span><span class="sxs-lookup"><span data-stu-id="7fd9b-109">Enable SignalR in startup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7fd9b-110">Das Hosting ASP.net Core SignalR Hubs im Kontext eines hintergrundworkerprozesses ist identisch mit dem Hosting eines Hubs in einer ASP.net Core Web-App.</span><span class="sxs-lookup"><span data-stu-id="7fd9b-110">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="7fd9b-111">In der `Startup.ConfigureServices`-Methode fügt der Aufruf `services.AddSignalR` die erforderlichen Dienste der ASP.net Core-Abhängigkeitsinjektion (di)-Ebene hinzu, um SignalRzu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="7fd9b-111">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="7fd9b-112">In `Startup.Configure`wird die `MapHub`-Methode im `UseEndpoints` Rückruf aufgerufen, um die Hub-Endpunkte in der ASP.net Core Anforderungs Pipeline zu verbinden.</span><span class="sxs-lookup"><span data-stu-id="7fd9b-112">In `Startup.Configure`, the `MapHub` method is called in the `UseEndpoints` callback to connect the Hub endpoints in the ASP.NET Core request pipeline.</span></span>

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

<span data-ttu-id="7fd9b-113">Das Hosting ASP.net Core SignalR Hubs im Kontext eines hintergrundworkerprozesses ist identisch mit dem Hosting eines Hubs in einer ASP.net Core Web-App.</span><span class="sxs-lookup"><span data-stu-id="7fd9b-113">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="7fd9b-114">In der `Startup.ConfigureServices`-Methode fügt der Aufruf `services.AddSignalR` die erforderlichen Dienste der ASP.net Core-Abhängigkeitsinjektion (di)-Ebene hinzu, um SignalRzu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="7fd9b-114">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="7fd9b-115">In `Startup.Configure`wird die `UseSignalR`-Methode aufgerufen, um die Hub-Endpunkte in der ASP.net Core Anforderungs Pipeline zu verbinden.</span><span class="sxs-lookup"><span data-stu-id="7fd9b-115">In `Startup.Configure`, the `UseSignalR` method is called to connect the Hub endpoint(s) in the ASP.NET Core request pipeline.</span></span>

[!code-csharp[Startup](background-service/sample/Server/Startup.cs?name=Startup)]

::: moniker-end

<span data-ttu-id="7fd9b-116">Im vorherigen Beispiel implementiert die `ClockHub`-Klasse die `Hub<T>`-Klasse, um einen stark typisierten Hub zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="7fd9b-116">In the preceding example, the `ClockHub` class implements the `Hub<T>` class to create a strongly typed Hub.</span></span> <span data-ttu-id="7fd9b-117">Der `ClockHub` wurde in der `Startup`-Klasse konfiguriert, um auf Anforderungen am Endpunkt `/hubs/clock`zu reagieren.</span><span class="sxs-lookup"><span data-stu-id="7fd9b-117">The `ClockHub` has been configured in the `Startup` class to respond to requests at the endpoint `/hubs/clock`.</span></span>

<span data-ttu-id="7fd9b-118">Weitere Informationen zu stark typisierten Hubs finden Sie unter [Verwenden von Hubs in SignalR für ASP.net Core](xref:signalr/hubs#strongly-typed-hubs).</span><span class="sxs-lookup"><span data-stu-id="7fd9b-118">For more information on strongly typed Hubs, see [Use hubs in SignalR for ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).</span></span>

> [!NOTE]
> <span data-ttu-id="7fd9b-119">Diese Funktion ist nicht auf die [Hub-\<t >](xref:Microsoft.AspNetCore.SignalR.Hub`1) Klasse beschränkt.</span><span class="sxs-lookup"><span data-stu-id="7fd9b-119">This functionality isn't limited to the [Hub\<T>](xref:Microsoft.AspNetCore.SignalR.Hub`1) class.</span></span> <span data-ttu-id="7fd9b-120">Jede Klasse, die von [Hub](xref:Microsoft.AspNetCore.SignalR.Hub)erbt, z. [b. dynamichub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), funktioniert ebenfalls.</span><span class="sxs-lookup"><span data-stu-id="7fd9b-120">Any class that inherits from [Hub](xref:Microsoft.AspNetCore.SignalR.Hub), such as [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), will also work.</span></span>

[!code-csharp[Startup](background-service/sample/Server/ClockHub.cs?name=ClockHub)]

<span data-ttu-id="7fd9b-121">Die Schnittstelle, die von der stark typisierten `ClockHub` verwendet wird, ist die `IClock` Schnittstelle.</span><span class="sxs-lookup"><span data-stu-id="7fd9b-121">The interface used by the strongly typed `ClockHub` is the `IClock` interface.</span></span>

[!code-csharp[Startup](background-service/sample/HubServiceInterfaces/IClock.cs?name=IClock)]

## <a name="call-a-opno-locsignalr-hub-from-a-background-service"></a><span data-ttu-id="7fd9b-122">SignalR Hub von einem Hintergrunddienst aus abrufen</span><span class="sxs-lookup"><span data-stu-id="7fd9b-122">Call a SignalR Hub from a background service</span></span>

<span data-ttu-id="7fd9b-123">Während des Starts wird die `Worker`-Klasse, eine `BackgroundService`, mit `AddHostedService`aktiviert.</span><span class="sxs-lookup"><span data-stu-id="7fd9b-123">During startup, the `Worker` class, a `BackgroundService`, is enabled using `AddHostedService`.</span></span>

```csharp
services.AddHostedService<Worker>();
```

<span data-ttu-id="7fd9b-124">Da SignalR auch während der `Startup` Phase aktiviert wird, in der jeder Hub an einen einzelnen Endpunkt in der HTTP-Anforderungs Pipeline ASP.net Core angefügt ist, wird jeder Hub durch eine `IHubContext<T>` auf dem Server dargestellt.</span><span class="sxs-lookup"><span data-stu-id="7fd9b-124">Since SignalR is also enabled up during the `Startup` phase, in which each Hub is attached to an individual endpoint in ASP.NET Core's HTTP request pipeline, each Hub is represented by an `IHubContext<T>` on the server.</span></span> <span data-ttu-id="7fd9b-125">Mithilfe der di-Features von ASP.net Core können andere Klassen, die von der hostingschicht instanziiert werden (z. b. `BackgroundService` Klassen, MVC-Controller Klassen oder Razor Page Models) Verweise auf serverseitige Hubs erhalten, indem während der Erstellung Instanzen von `IHubContext<ClockHub, IClock>` akzeptiert werden.</span><span class="sxs-lookup"><span data-stu-id="7fd9b-125">Using ASP.NET Core's DI features, other classes instantiated by the hosting layer, like `BackgroundService` classes, MVC Controller classes, or Razor page models, can get references to server-side Hubs by accepting instances of `IHubContext<ClockHub, IClock>` during construction.</span></span>

[!code-csharp[Startup](background-service/sample/Server/Worker.cs?name=Worker)]

<span data-ttu-id="7fd9b-126">Da die `ExecuteAsync`-Methode im Hintergrunddienst iterativ aufgerufen wird, werden das aktuelle Datum und die aktuelle Uhrzeit des Servers mithilfe des `ClockHub`an die verbundenen Clients gesendet.</span><span class="sxs-lookup"><span data-stu-id="7fd9b-126">As the `ExecuteAsync` method is called iteratively in the background service, the server's current date and time are sent to the connected clients using the `ClockHub`.</span></span>

## <a name="react-to-opno-locsignalr-events-with-background-services"></a><span data-ttu-id="7fd9b-127">Reagieren auf SignalR Ereignisse mit Hintergrund Diensten</span><span class="sxs-lookup"><span data-stu-id="7fd9b-127">React to SignalR events with background services</span></span>

<span data-ttu-id="7fd9b-128">Wie eine Einzelseiten-APP, die den JavaScript-Client für SignalR oder eine .net-Desktop-App verwendet, kann die mithilfe des <xref:signalr/dotnet-client>verwenden, eine `BackgroundService` oder `IHostedService` Implementierung kann auch verwendet werden, um eine Verbindung mit SignalR Hubs herzustellen und auf Ereignisse zu reagieren.</span><span class="sxs-lookup"><span data-stu-id="7fd9b-128">Like a Single Page App using the JavaScript client for SignalR or a .NET desktop app can do using the using the <xref:signalr/dotnet-client>, a `BackgroundService` or `IHostedService` implementation can also be used to connect to SignalR Hubs and respond to events.</span></span>

<span data-ttu-id="7fd9b-129">Die `ClockHubClient`-Klasse implementiert sowohl die `IClock`-Schnittstelle als auch die `IHostedService`-Schnittstelle.</span><span class="sxs-lookup"><span data-stu-id="7fd9b-129">The `ClockHubClient` class implements both the `IClock` interface and the `IHostedService` interface.</span></span> <span data-ttu-id="7fd9b-130">Auf diese Weise kann Sie während `Startup` fortlaufend ausgeführt werden und auf Hub-Ereignisse vom Server reagieren.</span><span class="sxs-lookup"><span data-stu-id="7fd9b-130">This way it can be enabled during `Startup` to run continuously and respond to Hub events from the server.</span></span>

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

<span data-ttu-id="7fd9b-131">Während der Initialisierung erstellt der `ClockHubClient` eine Instanz eines `HubConnection` und aktiviert die `IClock.ShowTime`-Methode als Handler für das `ShowTime` Ereignis des Hubs.</span><span class="sxs-lookup"><span data-stu-id="7fd9b-131">During initialization, the `ClockHubClient` creates an instance of a `HubConnection` and enables the `IClock.ShowTime` method as the handler for the Hub's `ShowTime` event.</span></span>

[!code-csharp[The ClockHubClient constructor](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

<span data-ttu-id="7fd9b-132">In der `IHostedService.StartAsync`-Implementierung wird der `HubConnection` asynchron gestartet.</span><span class="sxs-lookup"><span data-stu-id="7fd9b-132">In the `IHostedService.StartAsync` implementation, the `HubConnection` is started asynchronously.</span></span>

[!code-csharp[StartAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

<span data-ttu-id="7fd9b-133">Während der `IHostedService.StopAsync`-Methode wird der `HubConnection` asynchron verworfen.</span><span class="sxs-lookup"><span data-stu-id="7fd9b-133">During the `IHostedService.StopAsync` method, the `HubConnection` is disposed of asynchronously.</span></span>

[!code-csharp[StopAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

## <a name="additional-resources"></a><span data-ttu-id="7fd9b-134">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="7fd9b-134">Additional resources</span></span>

* [<span data-ttu-id="7fd9b-135">Erste Schritte</span><span class="sxs-lookup"><span data-stu-id="7fd9b-135">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="7fd9b-136">Hubs</span><span class="sxs-lookup"><span data-stu-id="7fd9b-136">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="7fd9b-137">Veröffentlichen in Azure</span><span class="sxs-lookup"><span data-stu-id="7fd9b-137">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="7fd9b-138">Stark typisierte Hubs</span><span class="sxs-lookup"><span data-stu-id="7fd9b-138">Strongly typed Hubs</span></span>](xref:signalr/hubs#strongly-typed-hubs)

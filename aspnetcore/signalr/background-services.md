---
title: Host ASP.NET Core SignalR in Diensten im Hintergrund
author: bradygaster
description: Erfahren Sie, wie Nachrichten von .NET Core BackgroundService-Klassen für SignalR-Clients gesendet.
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 02/04/2019
uid: signalr/background-services
ms.openlocfilehash: b359bd7f6b0667aeb8d9c8f5eb450637b1347b19
ms.sourcegitcommit: e418cb9cddeb3de06fa0cb4fdb5529da03ff6d63
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2019
ms.locfileid: "55739669"
---
# <a name="host-aspnet-core-signalr-in-background-services"></a><span data-ttu-id="37044-103">Host ASP.NET Core SignalR in Diensten im Hintergrund</span><span class="sxs-lookup"><span data-stu-id="37044-103">Host ASP.NET Core SignalR in background services</span></span>

<span data-ttu-id="37044-104">Durch [Brady Gaster](https://twitter.com/bradygaster)</span><span class="sxs-lookup"><span data-stu-id="37044-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="37044-105">Dieser Artikel enthält Anleitungen für:</span><span class="sxs-lookup"><span data-stu-id="37044-105">This article provides guidance for:</span></span>

* <span data-ttu-id="37044-106">SignalR-Hubs mit einem mit ASP.NET Core gehosteten Hintergrund-Arbeitsprozess gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="37044-106">Hosting SignalR Hubs using a background worker process hosted with ASP.NET Core.</span></span>
* <span data-ttu-id="37044-107">Senden von Nachrichten an verbundene Clients in ein .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).</span><span class="sxs-lookup"><span data-stu-id="37044-107">Sending messages to connected clients from within a .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).</span></span>

<span data-ttu-id="37044-108">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(Herunterladen von)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="37044-108">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="wire-up-signalr-during-startup"></a><span data-ttu-id="37044-109">Während des Starts SignalR verknüpfen</span><span class="sxs-lookup"><span data-stu-id="37044-109">Wire up SignalR during startup</span></span>

<span data-ttu-id="37044-110">Hosten von ASP.NET Core SignalR-Hubs im Kontext eines Arbeitsprozesses für den Hintergrund ist zum Hosten der Hub in einer ASP.NET Core-Web-app identisch.</span><span class="sxs-lookup"><span data-stu-id="37044-110">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="37044-111">In der `Startup.ConfigureServices` -Methode, die aufrufende `services.AddSignalR` fügt die erforderlichen Dienste, die ASP.NET Core Dependency Injection (DI)-Ebene, die SignalR unterstützen.</span><span class="sxs-lookup"><span data-stu-id="37044-111">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="37044-112">In `Startup.Configure`, `UseSignalR` Methode wird aufgerufen, die Hub-Endpunkte in der ASP.NET Core-Anforderungspipeline verknüpfen.</span><span class="sxs-lookup"><span data-stu-id="37044-112">In `Startup.Configure`, the `UseSignalR` method is called to wire up the Hub endpoint(s) in the ASP.NET Core request pipeline.</span></span>

[!code-csharp[Startup](background-service/sample/Server/Startup.cs?name=Startup)]

<span data-ttu-id="37044-113">Im vorherigen Beispiel das `ClockHub` -Klasse implementiert die `Hub<T>` Klasse, um einen stark typisierten Hub zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="37044-113">In the preceding example, the `ClockHub` class implements the `Hub<T>` class to create a strongly typed Hub.</span></span> <span data-ttu-id="37044-114">Die `ClockHub` konfiguriert wurde die `Startup` Klasse zum Reagieren auf Anforderungen an den Endpunkt `/hubs/clock`.</span><span class="sxs-lookup"><span data-stu-id="37044-114">The `ClockHub` has been configured in the `Startup` class to respond to requests at the endpoint `/hubs/clock`.</span></span>

<span data-ttu-id="37044-115">Weitere Informationen zu stark typisierte Hubs finden Sie unter [verwenden Hubs in SignalR für ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).</span><span class="sxs-lookup"><span data-stu-id="37044-115">For more information on strongly typed Hubs, see [Use hubs in SignalR for ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).</span></span>

> [!NOTE]
> <span data-ttu-id="37044-116">Diese Funktionen sind nicht auf die [Hub\<T >](xref:Microsoft.AspNetCore.SignalR.Hub`1) Klasse.</span><span class="sxs-lookup"><span data-stu-id="37044-116">This functionality isn't limited to the [Hub\<T>](xref:Microsoft.AspNetCore.SignalR.Hub`1) class.</span></span> <span data-ttu-id="37044-117">Jede Klasse, die von erbt [Hub](xref:Microsoft.AspNetCore.SignalR.Hub), z. B. [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), funktionieren auch.</span><span class="sxs-lookup"><span data-stu-id="37044-117">Any class that inherits from [Hub](xref:Microsoft.AspNetCore.SignalR.Hub), such as [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), will also work.</span></span>

[!code-csharp[Startup](background-service/sample/Server/ClockHub.cs?name=ClockHub)]

<span data-ttu-id="37044-118">Die Schnittstelle, durch den stark typisierten `ClockHub` ist die `IClock` Schnittstelle.</span><span class="sxs-lookup"><span data-stu-id="37044-118">The interface used by the strongly typed `ClockHub` is the `IClock` interface.</span></span>

[!code-csharp[Startup](background-service/sample/HubServiceInterfaces/IClock.cs?name=IClock)]

## <a name="call-a-signalr-hub-from-a-background-service"></a><span data-ttu-id="37044-119">Rufen Sie einen Hintergrunddienst einen SignalR-Hub</span><span class="sxs-lookup"><span data-stu-id="37044-119">Call a SignalR Hub from a background service</span></span>

<span data-ttu-id="37044-120">Während des Starts der `Worker` -Klasse, eine `BackgroundService`, läuft mit `AddHostedService`.</span><span class="sxs-lookup"><span data-stu-id="37044-120">During startup, the `Worker` class, a `BackgroundService`, is wired up using `AddHostedService`.</span></span>

```csharp
services.AddHostedService<Worker>();
```

<span data-ttu-id="37044-121">Da SignalR auch während der vernetzt ist die `Startup` phase, in dem jeder Hub an einen einzelnen Endpunkt in ASP.NET Core-HTTP-Anforderungspipeline angefügt ist, wird jeder Hub durch dargestellt eine `IHubContext<T>` auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="37044-121">Since SignalR is also wired up during the `Startup` phase, in which each Hub is attached to an individual endpoint in ASP.NET Core's HTTP request pipeline, each Hub is represented by an `IHubContext<T>` on the server.</span></span> <span data-ttu-id="37044-122">Mithilfe von ASP.NET Core DI features, andere Klassen instanziiert, indem der hostingebene, wie z. B. `BackgroundService` Klassen, MVC-Controller-Klassen oder Razor Pages-Modellen, können Verweise auf die serverseitigen Hubs abrufen, indem Sie Instanzen von akzeptieren `IHubContext<ClockHub, IClock>` während der Erstellung.</span><span class="sxs-lookup"><span data-stu-id="37044-122">Using ASP.NET Core's DI features, other classes instantiated by the hosting layer, like `BackgroundService` classes, MVC Controller classes, or Razor page models, can get references to server-side Hubs by accepting instances of `IHubContext<ClockHub, IClock>` during construction.</span></span>

[!code-csharp[Startup](background-service/sample/Server/Worker.cs?name=Worker)]

<span data-ttu-id="37044-123">Als die `ExecuteAsync` -Methode im Hintergrunddienst wiederholt aufgerufen wird, aktuelles Datum und Uhrzeit des Servers gesendet werden an die verbundenen Clients, die mit der `ClockHub`.</span><span class="sxs-lookup"><span data-stu-id="37044-123">As the `ExecuteAsync` method is called iteratively in the background service, the server's current date and time are sent to the connected clients using the `ClockHub`.</span></span>

## <a name="react-to-signalr-events-with-background-services"></a><span data-ttu-id="37044-124">Reagieren Sie auf Ereignisse von SignalR mit Diensten im Hintergrund</span><span class="sxs-lookup"><span data-stu-id="37044-124">React to SignalR events with background services</span></span>

<span data-ttu-id="37044-125">Wie eine Einzelseiten-App mithilfe von JavaScript-Client für SignalR oder eine .NET desktop-app ausführen kann mithilfe der von der <xref:signalr/dotnet-client>, `BackgroundService` oder `IHostedService` Implementierung kann auch verwendet werden, um eine Verbindung mit SignalR-Hubs herstellen und auf Ereignisse reagieren.</span><span class="sxs-lookup"><span data-stu-id="37044-125">Like a Single Page App using the JavaScript client for SignalR or a .NET desktop app can do using the using the <xref:signalr/dotnet-client>, a `BackgroundService` or `IHostedService` implementation can also be used to connect to SignalR Hubs and respond to events.</span></span>

<span data-ttu-id="37044-126">Die `ClockHubClient` -Klasse implementiert sowohl die `IClock` Schnittstelle und die `IHostedService` Schnittstelle.</span><span class="sxs-lookup"><span data-stu-id="37044-126">The `ClockHubClient` class implements both the `IClock` interface and the `IHostedService` interface.</span></span> <span data-ttu-id="37044-127">Auf diese Weise können sie während der eingerichtet werden `Startup` kontinuierlich ausgeführt werden, und reagieren auf Hub-Ereignisse vom Server.</span><span class="sxs-lookup"><span data-stu-id="37044-127">This way it can be wired up during `Startup` to run continuously and respond to Hub events from the server.</span></span> 

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

<span data-ttu-id="37044-128">Während der Initialisierung der `ClockHubClient` erstellt eine Instanz eine `HubConnection` und verbindet die `IClock.ShowTime` Methode als Ereignishandler für des Hubs `ShowTime` Ereignis.</span><span class="sxs-lookup"><span data-stu-id="37044-128">During initialization, the `ClockHubClient` creates an instance of a `HubConnection` and wires up the `IClock.ShowTime` method as the handler for the Hub's `ShowTime` event.</span></span>

[!code-csharp[The ClockHubClient constructor](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

<span data-ttu-id="37044-129">In der `IHostedService.StartAsync` -Implementierung der `HubConnection` wird asynchron gestartet.</span><span class="sxs-lookup"><span data-stu-id="37044-129">In the `IHostedService.StartAsync` implementation, the `HubConnection` is started asynchronously.</span></span>

[!code-csharp[StartAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

<span data-ttu-id="37044-130">Während der `IHostedService.StopAsync` -Methode, die `HubConnection` asynchron verworfen wird.</span><span class="sxs-lookup"><span data-stu-id="37044-130">During the `IHostedService.StopAsync` method, the `HubConnection` is disposed of asynchronously.</span></span>

[!code-csharp[StopAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

## <a name="additional-resources"></a><span data-ttu-id="37044-131">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="37044-131">Additional resources</span></span>

* [<span data-ttu-id="37044-132">Erste Schritte</span><span class="sxs-lookup"><span data-stu-id="37044-132">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="37044-133">Hubs</span><span class="sxs-lookup"><span data-stu-id="37044-133">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="37044-134">Veröffentlichen in Azure</span><span class="sxs-lookup"><span data-stu-id="37044-134">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="37044-135">Stark typisierte Hubs</span><span class="sxs-lookup"><span data-stu-id="37044-135">Strongly typed Hubs</span></span>](xref:signalr/hubs#strongly-typed-hubs)

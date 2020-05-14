---
title: Host ASP.net Core SignalR in Hintergrund Diensten
author: bradygaster
description: Erfahren Sie, wie Sie Nachrichten SignalR von .net Core-backgroundservice-Klassen an Clients senden.
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
ms.openlocfilehash: 7bc3b9535055e3fccf23ffa4638bd29676910348
ms.sourcegitcommit: e87dfa08fec0be1008249b1be678e5f79dcc5acb
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83382568"
---
# <a name="host-aspnet-core-signalr-in-background-services"></a><span data-ttu-id="362c7-103">Host ASP.net Core SignalR in Hintergrund Diensten</span><span class="sxs-lookup"><span data-stu-id="362c7-103">Host ASP.NET Core SignalR in background services</span></span>

<span data-ttu-id="362c7-104">Von [Brady Gastern](https://twitter.com/bradygaster)</span><span class="sxs-lookup"><span data-stu-id="362c7-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="362c7-105">Dieser Artikel enthält Anleitungen für:</span><span class="sxs-lookup"><span data-stu-id="362c7-105">This article provides guidance for:</span></span>

* <span data-ttu-id="362c7-106">Hosten SignalR von Hubs mit einem Hintergrund Arbeitsprozess, der mit ASP.net Core gehostet wird.</span><span class="sxs-lookup"><span data-stu-id="362c7-106">Hosting SignalR Hubs using a background worker process hosted with ASP.NET Core.</span></span>
* <span data-ttu-id="362c7-107">Senden von Nachrichten an verbundene Clients aus einem .net Core- [backgroundservice](xref:Microsoft.Extensions.Hosting.BackgroundService).</span><span class="sxs-lookup"><span data-stu-id="362c7-107">Sending messages to connected clients from within a .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="362c7-108">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/3.x) [(Vorgehensweise zum herunterladen)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="362c7-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/3.x) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="362c7-109">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/2.2) [(Vorgehensweise zum herunterladen)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="362c7-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/2.2) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

::: moniker-end

## <a name="enable-signalr-in-startup"></a><span data-ttu-id="362c7-110">SignalRBeim Start aktivieren</span><span class="sxs-lookup"><span data-stu-id="362c7-110">Enable SignalR in startup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="362c7-111">Das Hosting SignalR von ASP.net Core Hubs im Kontext eines hintergrundworkerprozesses ist identisch mit dem Hosting eines Hubs in einer ASP.net Core Web-App.</span><span class="sxs-lookup"><span data-stu-id="362c7-111">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="362c7-112">In der- `Startup.ConfigureServices` Methode fügt der Aufruf von `services.AddSignalR` die erforderlichen Dienste der Ebene der ASP.net Core Abhängigkeitsinjektion (di) hinzu, um zu unterstützen SignalR .</span><span class="sxs-lookup"><span data-stu-id="362c7-112">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="362c7-113">In `Startup.Configure` wird die- `MapHub` Methode im Rückruf aufgerufen `UseEndpoints` , um die Hub-Endpunkte in der ASP.net Core Anforderungs Pipeline zu verbinden.</span><span class="sxs-lookup"><span data-stu-id="362c7-113">In `Startup.Configure`, the `MapHub` method is called in the `UseEndpoints` callback to connect the Hub endpoints in the ASP.NET Core request pipeline.</span></span>

[!code-csharp[Startup](background-service/samples/3.x/Server/Startup.cs?name=Startup)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="362c7-114">Das Hosting SignalR von ASP.net Core Hubs im Kontext eines hintergrundworkerprozesses ist identisch mit dem Hosting eines Hubs in einer ASP.net Core Web-App.</span><span class="sxs-lookup"><span data-stu-id="362c7-114">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="362c7-115">In der- `Startup.ConfigureServices` Methode fügt der Aufruf von `services.AddSignalR` die erforderlichen Dienste der Ebene der ASP.net Core Abhängigkeitsinjektion (di) hinzu, um zu unterstützen SignalR .</span><span class="sxs-lookup"><span data-stu-id="362c7-115">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="362c7-116">In `Startup.Configure` wird die- `UseSignalR` Methode aufgerufen, um die Hub-Endpunkte in der ASP.net Core Anforderungs Pipeline zu verbinden.</span><span class="sxs-lookup"><span data-stu-id="362c7-116">In `Startup.Configure`, the `UseSignalR` method is called to connect the Hub endpoint(s) in the ASP.NET Core request pipeline.</span></span>

[!code-csharp[Startup](background-service/samples/2.2/Server/Startup.cs?name=Startup)]

::: moniker-end

<span data-ttu-id="362c7-117">Im vorherigen Beispiel implementiert die- `ClockHub` Klasse die- `Hub<T>` Klasse, um einen stark typisierten Hub zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="362c7-117">In the preceding example, the `ClockHub` class implements the `Hub<T>` class to create a strongly typed Hub.</span></span> <span data-ttu-id="362c7-118">Der wurde `ClockHub` in der-Klasse so konfiguriert, dass er `Startup` auf Anforderungen am Endpunkt antwortet `/hubs/clock` .</span><span class="sxs-lookup"><span data-stu-id="362c7-118">The `ClockHub` has been configured in the `Startup` class to respond to requests at the endpoint `/hubs/clock`.</span></span>

<span data-ttu-id="362c7-119">Weitere Informationen zu stark typisierten Hubs finden Sie unter [Verwenden von Hubs in SignalR für ASP.net Core](xref:signalr/hubs#strongly-typed-hubs).</span><span class="sxs-lookup"><span data-stu-id="362c7-119">For more information on strongly typed Hubs, see [Use hubs in SignalR for ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).</span></span>

> [!NOTE]
> <span data-ttu-id="362c7-120">Diese Funktion ist nicht auf die [Hub \< t->](xref:Microsoft.AspNetCore.SignalR.Hub`1) Klasse beschränkt.</span><span class="sxs-lookup"><span data-stu-id="362c7-120">This functionality isn't limited to the [Hub\<T>](xref:Microsoft.AspNetCore.SignalR.Hub`1) class.</span></span> <span data-ttu-id="362c7-121">Jede Klasse, die von [Hub](xref:Microsoft.AspNetCore.SignalR.Hub)erbt, z. [b. dynamichub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), funktioniert.</span><span class="sxs-lookup"><span data-stu-id="362c7-121">Any class that inherits from [Hub](xref:Microsoft.AspNetCore.SignalR.Hub), such as [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), works.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/Server/ClockHub.cs?name=ClockHub)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/Server/ClockHub.cs?name=ClockHub)]

::: moniker-end

<span data-ttu-id="362c7-122">Die vom stark typisierten verwendete Schnittstelle `ClockHub` ist die- `IClock` Schnittstelle.</span><span class="sxs-lookup"><span data-stu-id="362c7-122">The interface used by the strongly typed `ClockHub` is the `IClock` interface.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/HubServiceInterfaces/IClock.cs?name=IClock)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/HubServiceInterfaces/IClock.cs?name=IClock)]

::: moniker-end

## <a name="call-a-signalr-hub-from-a-background-service"></a><span data-ttu-id="362c7-123">Einen SignalR Hub über einen Hintergrunddienst aufzurufen</span><span class="sxs-lookup"><span data-stu-id="362c7-123">Call a SignalR Hub from a background service</span></span>

<span data-ttu-id="362c7-124">Während des Starts `Worker` wird die Klasse, a `BackgroundService` , mithilfe von aktiviert `AddHostedService` .</span><span class="sxs-lookup"><span data-stu-id="362c7-124">During startup, the `Worker` class, a `BackgroundService`, is enabled using `AddHostedService`.</span></span>

```csharp
services.AddHostedService<Worker>();
```

<span data-ttu-id="362c7-125">Da SignalR auch in der Phase aktiviert ist `Startup` , in der jeder Hub an einen einzelnen Endpunkt in der HTTP-Anforderungs Pipeline ASP.net Core angefügt ist, wird jeder Hub durch ein `IHubContext<T>` auf dem Server dargestellt.</span><span class="sxs-lookup"><span data-stu-id="362c7-125">Since SignalR is also enabled up during the `Startup` phase, in which each Hub is attached to an individual endpoint in ASP.NET Core's HTTP request pipeline, each Hub is represented by an `IHubContext<T>` on the server.</span></span> <span data-ttu-id="362c7-126">Mithilfe der di-Features von ASP.net Core können andere Klassen, die von der hostingschicht instanziiert werden (z. b. `BackgroundService` Klassen, MVC-Controller Klassen oder Razor Seiten Modelle), Verweise auf serverseitige Hubs erhalten, indem Sie Instanzen von `IHubContext<ClockHub, IClock>` während der Konstruktion akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="362c7-126">Using ASP.NET Core's DI features, other classes instantiated by the hosting layer, like `BackgroundService` classes, MVC Controller classes, or Razor page models, can get references to server-side Hubs by accepting instances of `IHubContext<ClockHub, IClock>` during construction.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/Server/Worker.cs?name=Worker)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/Server/Worker.cs?name=Worker)]

::: moniker-end

<span data-ttu-id="362c7-127">Wenn die `ExecuteAsync` -Methode im Hintergrunddienst iterativ aufgerufen wird, werden das aktuelle Datum und die aktuelle Uhrzeit des Servers über das an die verbundenen Clients gesendet `ClockHub` .</span><span class="sxs-lookup"><span data-stu-id="362c7-127">As the `ExecuteAsync` method is called iteratively in the background service, the server's current date and time are sent to the connected clients using the `ClockHub`.</span></span>

## <a name="react-to-signalr-events-with-background-services"></a><span data-ttu-id="362c7-128">Reagieren auf SignalR Ereignisse mit Hintergrund Diensten</span><span class="sxs-lookup"><span data-stu-id="362c7-128">React to SignalR events with background services</span></span>

<span data-ttu-id="362c7-129">Wie eine Einzelseiten-APP, die den JavaScript SignalR -Client für oder eine .net-Desktop-App mithilfe von verwenden kann <xref:signalr/dotnet-client> , kann eine- `BackgroundService` oder- `IHostedService` Implementierung auch verwendet werden, um eine Verbindung mit SignalR Hubs herzustellen und auf Ereignisse zu reagieren.</span><span class="sxs-lookup"><span data-stu-id="362c7-129">Like a Single Page App using the JavaScript client for SignalR or a .NET desktop app can do using the using the <xref:signalr/dotnet-client>, a `BackgroundService` or `IHostedService` implementation can also be used to connect to SignalR Hubs and respond to events.</span></span>

<span data-ttu-id="362c7-130">Die `ClockHubClient` -Klasse implementiert die `IClock` -Schnittstelle und die- `IHostedService` Schnittstelle.</span><span class="sxs-lookup"><span data-stu-id="362c7-130">The `ClockHubClient` class implements both the `IClock` interface and the `IHostedService` interface.</span></span> <span data-ttu-id="362c7-131">Auf diese Weise kann Sie während `Startup` der kontinuierlichen Durchführung von aktiviert werden und auf Hub-Ereignisse vom Server reagieren.</span><span class="sxs-lookup"><span data-stu-id="362c7-131">This way it can be enabled during `Startup` to run continuously and respond to Hub events from the server.</span></span>

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

<span data-ttu-id="362c7-132">Während der Initialisierung wird `ClockHubClient` von eine Instanz von erstellt `HubConnection` und die `IClock.ShowTime` -Methode als Handler für das-Ereignis des Hubs aktiviert `ShowTime` .</span><span class="sxs-lookup"><span data-stu-id="362c7-132">During initialization, the `ClockHubClient` creates an instance of a `HubConnection` and enables the `IClock.ShowTime` method as the handler for the Hub's `ShowTime` event.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[The ClockHubClient constructor](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

<span data-ttu-id="362c7-133">In der- `IHostedService.StartAsync` Implementierung `HubConnection` wird der asynchron gestartet.</span><span class="sxs-lookup"><span data-stu-id="362c7-133">In the `IHostedService.StartAsync` implementation, the `HubConnection` is started asynchronously.</span></span>

[!code-csharp[StartAsync method](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

<span data-ttu-id="362c7-134">Während der- `IHostedService.StopAsync` Methode `HubConnection` wird der asynchron freigegeben.</span><span class="sxs-lookup"><span data-stu-id="362c7-134">During the `IHostedService.StopAsync` method, the `HubConnection` is disposed of asynchronously.</span></span>

[!code-csharp[StopAsync method](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[The ClockHubClient constructor](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

<span data-ttu-id="362c7-135">In der- `IHostedService.StartAsync` Implementierung `HubConnection` wird der asynchron gestartet.</span><span class="sxs-lookup"><span data-stu-id="362c7-135">In the `IHostedService.StartAsync` implementation, the `HubConnection` is started asynchronously.</span></span>

[!code-csharp[StartAsync method](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

<span data-ttu-id="362c7-136">Während der- `IHostedService.StopAsync` Methode `HubConnection` wird der asynchron freigegeben.</span><span class="sxs-lookup"><span data-stu-id="362c7-136">During the `IHostedService.StopAsync` method, the `HubConnection` is disposed of asynchronously.</span></span>

[!code-csharp[StopAsync method](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="362c7-137">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="362c7-137">Additional resources</span></span>

* [<span data-ttu-id="362c7-138">Erste Schritte</span><span class="sxs-lookup"><span data-stu-id="362c7-138">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="362c7-139">Hubs</span><span class="sxs-lookup"><span data-stu-id="362c7-139">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="362c7-140">Veröffentlichen in Azure</span><span class="sxs-lookup"><span data-stu-id="362c7-140">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="362c7-141">Stark typisierte Hubs</span><span class="sxs-lookup"><span data-stu-id="362c7-141">Strongly typed Hubs</span></span>](xref:signalr/hubs#strongly-typed-hubs)

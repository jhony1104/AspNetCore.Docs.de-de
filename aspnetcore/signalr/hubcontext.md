---
title: SignalR HubContext
author: bradygaster
description: Erfahren Sie, wie Sie mit der ASP.NET Core SignalR HubContext-Dienst zum Senden von Benachrichtigungen an Clients von außerhalb eines Hubs.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/01/2018
uid: signalr/hubcontext
ms.openlocfilehash: 73cf2c9d30ed5e409a75827fdab1f22b20427884
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837935"
---
# <a name="send-messages-from-outside-a-hub"></a><span data-ttu-id="b4855-103">Senden von Nachrichten von außerhalb eines Hubs</span><span class="sxs-lookup"><span data-stu-id="b4855-103">Send messages from outside a hub</span></span>

<span data-ttu-id="b4855-104">Durch [Mikael Mengistu](https://twitter.com/MikaelM_12)</span><span class="sxs-lookup"><span data-stu-id="b4855-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="b4855-105">Der SignalR-Hub ist die Core-Abstraktion zum Senden von Nachrichten für Clients, die mit dem SignalR-Server verbunden.</span><span class="sxs-lookup"><span data-stu-id="b4855-105">The SignalR hub is the core abstraction for sending messages to clients connected to the SignalR server.</span></span> <span data-ttu-id="b4855-106">Es ist auch möglich, zum Senden von Nachrichten an anderen Stellen in Ihrer app über die `IHubContext` Service.</span><span class="sxs-lookup"><span data-stu-id="b4855-106">It's also possible to send messages from other places in your app using the `IHubContext` service.</span></span> <span data-ttu-id="b4855-107">In diesem Artikel wird erläutert, wie auf eine SignalR `IHubContext` zum Senden von Benachrichtigungen an Clients von außerhalb eines Hubs.</span><span class="sxs-lookup"><span data-stu-id="b4855-107">This article explains how to access a SignalR `IHubContext` to send notifications to clients from outside a hub.</span></span>

<span data-ttu-id="b4855-108">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(Herunterladen von)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="b4855-108">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="get-an-instance-of-ihubcontext"></a><span data-ttu-id="b4855-109">Eine Instanz der IHubContext abrufen</span><span class="sxs-lookup"><span data-stu-id="b4855-109">Get an instance of IHubContext</span></span>

<span data-ttu-id="b4855-110">In ASP.NET Core SignalR und kann auf eine Instanz des `IHubContext` über Dependency Injection.</span><span class="sxs-lookup"><span data-stu-id="b4855-110">In ASP.NET Core SignalR, you can access an instance of `IHubContext` via dependency injection.</span></span> <span data-ttu-id="b4855-111">Fügen Sie eine Instanz von `IHubContext` in einen Controller, Middleware oder andere DI-Dienst.</span><span class="sxs-lookup"><span data-stu-id="b4855-111">You can inject an instance of `IHubContext` into a controller, middleware, or other DI service.</span></span> <span data-ttu-id="b4855-112">Verwenden Sie die Instanz, um Nachrichten an Clients zu senden.</span><span class="sxs-lookup"><span data-stu-id="b4855-112">Use the instance to send messages to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="b4855-113">Dies unterscheidet sich von ASP.NET 4.x SignalR GlobalHost verwendet wird, Zugriff auf die `IHubContext`.</span><span class="sxs-lookup"><span data-stu-id="b4855-113">This differs from ASP.NET 4.x SignalR which used GlobalHost to provide access to the `IHubContext`.</span></span> <span data-ttu-id="b4855-114">ASP.NET Core verfügt über einen Dependency Injection-Framework, die die Notwendigkeit dieses globale Singleton entfernt.</span><span class="sxs-lookup"><span data-stu-id="b4855-114">ASP.NET Core has a dependency injection framework that removes the need for this global singleton.</span></span>

### <a name="inject-an-instance-of-ihubcontext-in-a-controller"></a><span data-ttu-id="b4855-115">Eine Instanz von IHubContext in einem controller</span><span class="sxs-lookup"><span data-stu-id="b4855-115">Inject an instance of IHubContext in a controller</span></span>

<span data-ttu-id="b4855-116">Fügen Sie eine Instanz von `IHubContext` in einen Controller, indem sie Sie dem Konstruktor hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="b4855-116">You can inject an instance of `IHubContext` into a controller by adding it to your constructor:</span></span>

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=12-19,57)]

<span data-ttu-id="b4855-117">Dank des Zugriffs auf eine Instanz von `IHubContext`, Sie können die Hub-Methoden aufrufen, als wären Sie auf dem Hub selbst.</span><span class="sxs-lookup"><span data-stu-id="b4855-117">Now, with access to an instance of `IHubContext`, you can call hub methods as if you were in the hub itself.</span></span>

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=21-25)]

### <a name="get-an-instance-of-ihubcontext-in-middleware"></a><span data-ttu-id="b4855-118">Rufen Sie eine Instanz des IHubContext in-middleware</span><span class="sxs-lookup"><span data-stu-id="b4855-118">Get an instance of IHubContext in middleware</span></span>

<span data-ttu-id="b4855-119">Zugriff die `IHubContext` innerhalb der middlewarepipeline wie folgt:</span><span class="sxs-lookup"><span data-stu-id="b4855-119">Access the `IHubContext` within the middleware pipeline like so:</span></span>

```csharp
app.Use(async (context, next) =>
{
    var hubContext = context.RequestServices
                            .GetRequiredService<IHubContext<MyHub>>();
    //...
});
```

> [!NOTE]
> <span data-ttu-id="b4855-120">Wenn hubmethoden werden aufgerufen, von außerhalb von die `Hub` Klasse, die keine Aufrufer den Aufruf zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="b4855-120">When hub methods are called from outside of the `Hub` class, there's no caller associated with the invocation.</span></span> <span data-ttu-id="b4855-121">Es gibt also keinen Zugriff auf die `ConnectionId`, `Caller`, und `Others` Eigenschaften.</span><span class="sxs-lookup"><span data-stu-id="b4855-121">Therefore, there's no access to the `ConnectionId`, `Caller`, and `Others` properties.</span></span>

### <a name="inject-a-strongly-typed-hubcontext"></a><span data-ttu-id="b4855-122">Eine stark typisierte HubContext einfügen</span><span class="sxs-lookup"><span data-stu-id="b4855-122">Inject a strongly-typed HubContext</span></span>

<span data-ttu-id="b4855-123">Um eine stark typisierte HubContext einzufügen, stellen Sie sicher erbt von Ihrem Hub `Hub<T>`.</span><span class="sxs-lookup"><span data-stu-id="b4855-123">To inject a strongly-typed HubContext, ensure your Hub inherits from `Hub<T>`.</span></span> <span data-ttu-id="b4855-124">Einfügen von mithilfe der `IHubContext<THub, T>` Schnittstelle statt `IHubContext<THub>`.</span><span class="sxs-lookup"><span data-stu-id="b4855-124">Inject it using the `IHubContext<THub, T>` interface rather than `IHubContext<THub>`.</span></span>

```csharp
public class ChatController : Controller
{
    public IHubContext<ChatHub, IChatClient> _strongChatHubContext { get; }

    public ChatController(IHubContext<ChatHub, IChatClient> chatHubContext)
    {
        _strongChatHubContext = chatHubContext;
    }

    public async Task SendMessage(string message)
    {
        await _strongChatHubContext.Clients.All.ReceiveMessage(message);
    }
}
```

## <a name="related-resources"></a><span data-ttu-id="b4855-125">Weitere Informationen</span><span class="sxs-lookup"><span data-stu-id="b4855-125">Related resources</span></span>

* [<span data-ttu-id="b4855-126">Erste Schritte</span><span class="sxs-lookup"><span data-stu-id="b4855-126">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="b4855-127">Hubs</span><span class="sxs-lookup"><span data-stu-id="b4855-127">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="b4855-128">Veröffentlichen in Azure</span><span class="sxs-lookup"><span data-stu-id="b4855-128">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)

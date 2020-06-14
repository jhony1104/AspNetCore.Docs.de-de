---
title: SignalRHubcontext
author: bradygaster
description: Erfahren Sie, wie Sie den ASP.net Core SignalR hubcontext-Dienst verwenden, um Benachrichtigungen von außerhalb eines Hubs an Clients zu senden.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/hubcontext
ms.openlocfilehash: 336173866e9346d836bb31955644d07403fc238d
ms.sourcegitcommit: a423e8fcde4b6181a3073ed646a603ba20bfa5f9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2020
ms.locfileid: "84756053"
---
# <a name="send-messages-from-outside-a-hub"></a><span data-ttu-id="47f05-103">Senden von Nachrichten von außerhalb eines Hubs</span><span class="sxs-lookup"><span data-stu-id="47f05-103">Send messages from outside a hub</span></span>

<span data-ttu-id="47f05-104">Von [Mikael Mengistu](https://twitter.com/MikaelM_12)</span><span class="sxs-lookup"><span data-stu-id="47f05-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="47f05-105">Der SignalR Hub ist die Kern Abstraktion für das Senden von Nachrichten an Clients, die mit dem Server verbunden sind SignalR .</span><span class="sxs-lookup"><span data-stu-id="47f05-105">The SignalR hub is the core abstraction for sending messages to clients connected to the SignalR server.</span></span> <span data-ttu-id="47f05-106">Es ist auch möglich, Nachrichten von anderen Stellen in Ihrer APP mit dem `IHubContext` Dienst zu senden.</span><span class="sxs-lookup"><span data-stu-id="47f05-106">It's also possible to send messages from other places in your app using the `IHubContext` service.</span></span> <span data-ttu-id="47f05-107">In diesem Artikel wird erläutert, wie Sie auf einen zugreifen SignalR `IHubContext` , um Benachrichtigungen von außerhalb eines Hubs an Clients zu senden.</span><span class="sxs-lookup"><span data-stu-id="47f05-107">This article explains how to access a SignalR `IHubContext` to send notifications to clients from outside a hub.</span></span>

<span data-ttu-id="47f05-108">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(Vorgehensweise zum Herunterladen)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="47f05-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="get-an-instance-of-ihubcontext"></a><span data-ttu-id="47f05-109">Eine Instanz von "ihubcontext" erhalten</span><span class="sxs-lookup"><span data-stu-id="47f05-109">Get an instance of IHubContext</span></span>

<span data-ttu-id="47f05-110">In ASP.net Core SignalR können Sie über Abhängigkeitsinjektion auf eine Instanz von zugreifen `IHubContext` .</span><span class="sxs-lookup"><span data-stu-id="47f05-110">In ASP.NET Core SignalR, you can access an instance of `IHubContext` via dependency injection.</span></span> <span data-ttu-id="47f05-111">Sie können eine Instanz von `IHubContext` in einen Controller, eine Middleware oder einen anderen di-Dienst einfügen.</span><span class="sxs-lookup"><span data-stu-id="47f05-111">You can inject an instance of `IHubContext` into a controller, middleware, or other DI service.</span></span> <span data-ttu-id="47f05-112">Verwenden Sie die-Instanz, um Nachrichten an Clients zu senden.</span><span class="sxs-lookup"><span data-stu-id="47f05-112">Use the instance to send messages to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="47f05-113">Dies unterscheidet sich von ASP.NET 4. x SignalR , bei dem globalhost verwendet wurde, um den Zugriff auf zu ermöglichen `IHubContext` .</span><span class="sxs-lookup"><span data-stu-id="47f05-113">This differs from ASP.NET 4.x SignalR which used GlobalHost to provide access to the `IHubContext`.</span></span> <span data-ttu-id="47f05-114">ASP.net Core verfügt über ein Framework für die Abhängigkeitsinjektion, das den Bedarf an diesem globalen Singleton entfällt.</span><span class="sxs-lookup"><span data-stu-id="47f05-114">ASP.NET Core has a dependency injection framework that removes the need for this global singleton.</span></span>

### <a name="inject-an-instance-of-ihubcontext-in-a-controller"></a><span data-ttu-id="47f05-115">Einfügen einer Instanz von ihubcontext in einen Controller</span><span class="sxs-lookup"><span data-stu-id="47f05-115">Inject an instance of IHubContext in a controller</span></span>

<span data-ttu-id="47f05-116">Sie können eine Instanz von `IHubContext` in einen Controller einfügen, indem Sie Sie dem Konstruktor hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="47f05-116">You can inject an instance of `IHubContext` into a controller by adding it to your constructor:</span></span>

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=12-19,57)]

<span data-ttu-id="47f05-117">Nun können Sie mit dem Zugriff auf eine Instanz von `IHubContext` hubmethoden aufrufen, als ob Sie sich im Hub selbst befinden würden.</span><span class="sxs-lookup"><span data-stu-id="47f05-117">Now, with access to an instance of `IHubContext`, you can call hub methods as if you were in the hub itself.</span></span>

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=21-25)]

### <a name="get-an-instance-of-ihubcontext-in-middleware"></a><span data-ttu-id="47f05-118">Eine Instanz von "ihubcontext" in der Middleware erhalten</span><span class="sxs-lookup"><span data-stu-id="47f05-118">Get an instance of IHubContext in middleware</span></span>

<span data-ttu-id="47f05-119">Greifen Sie `IHubContext` in der middlewarepipeline wie folgt auf zu:</span><span class="sxs-lookup"><span data-stu-id="47f05-119">Access the `IHubContext` within the middleware pipeline like so:</span></span>

```csharp
app.Use(async (context, next) =>
{
    var hubContext = context.RequestServices
                            .GetRequiredService<IHubContext<ChatHub>>();
    //...
    
    if (next != null)
    {
        await next.Invoke();
    }
});
```

> [!NOTE]
> <span data-ttu-id="47f05-120">Wenn hubmethoden von außerhalb der Klasse aufgerufen werden `Hub` , gibt es keinen Aufrufer, der dem Aufruf zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="47f05-120">When hub methods are called from outside of the `Hub` class, there's no caller associated with the invocation.</span></span> <span data-ttu-id="47f05-121">Daher gibt es keinen Zugriff auf die `ConnectionId` Eigenschaften, `Caller` und `Others` .</span><span class="sxs-lookup"><span data-stu-id="47f05-121">Therefore, there's no access to the `ConnectionId`, `Caller`, and `Others` properties.</span></span>

### <a name="get-an-instance-of-ihubcontext-from-ihost"></a><span data-ttu-id="47f05-122">Eine Instanz von "ihubcontext" von IHost erhalten</span><span class="sxs-lookup"><span data-stu-id="47f05-122">Get an instance of IHubContext from IHost</span></span>

<span data-ttu-id="47f05-123">Der Zugriff `IHubContext` auf ein vom Webhost aus ist für die Integration in Bereiche außerhalb von ASP.net Core hilfreich, z. b. durch die Verwendung von Drittanbieter-Abhängigkeits Injection-Frameworks</span><span class="sxs-lookup"><span data-stu-id="47f05-123">Accessing an `IHubContext` from the web host is useful for integrating with areas outside of ASP.NET Core, for example, using 3rd party dependency injection frameworks:</span></span>

```csharp
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = CreateHostBuilder(args).Build();
            var hubContext = host.Services.GetService(typeof(IHubContext<ChatHub>));
            host.Run();
        }

        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureWebHostDefaults(webBuilder => {
                    webBuilder.UseStartup<Startup>();
                });
    }
```

### <a name="inject-a-strongly-typed-hubcontext"></a><span data-ttu-id="47f05-124">Einfügen eines stark typisierten hubcontext</span><span class="sxs-lookup"><span data-stu-id="47f05-124">Inject a strongly-typed HubContext</span></span>

<span data-ttu-id="47f05-125">Um einen stark typisierten hubcontext einzufügen, stellen Sie sicher, dass Ihr Hub von erbt `Hub<T>` .</span><span class="sxs-lookup"><span data-stu-id="47f05-125">To inject a strongly-typed HubContext, ensure your Hub inherits from `Hub<T>`.</span></span> <span data-ttu-id="47f05-126">Fügen Sie Sie mithilfe der- `IHubContext<THub, T>` Schnittstelle anstelle von ein `IHubContext<THub>` .</span><span class="sxs-lookup"><span data-stu-id="47f05-126">Inject it using the `IHubContext<THub, T>` interface rather than `IHubContext<THub>`.</span></span>

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

## <a name="related-resources"></a><span data-ttu-id="47f05-127">Verwandte Ressourcen</span><span class="sxs-lookup"><span data-stu-id="47f05-127">Related resources</span></span>

* [<span data-ttu-id="47f05-128">Erste Schritte</span><span class="sxs-lookup"><span data-stu-id="47f05-128">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="47f05-129">Hubs</span><span class="sxs-lookup"><span data-stu-id="47f05-129">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="47f05-130">Veröffentlichen in Azure</span><span class="sxs-lookup"><span data-stu-id="47f05-130">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)

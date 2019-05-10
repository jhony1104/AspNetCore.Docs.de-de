---
title: SignalR HubContext
author: bradygaster
description: Erfahren Sie, wie Sie mit der ASP.NET Core SignalR HubContext-Dienst zum Senden von Benachrichtigungen an Clients von außerhalb eines Hubs.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/01/2018
uid: signalr/hubcontext
ms.openlocfilehash: 7ec52d4711fc191dcb83120cf54b1dc28c41f947
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64894477"
---
# <a name="send-messages-from-outside-a-hub"></a>Senden von Nachrichten von außerhalb eines Hubs

Durch [Mikael Mengistu](https://twitter.com/MikaelM_12)

Der SignalR-Hub ist die Core-Abstraktion zum Senden von Nachrichten für Clients, die mit dem SignalR-Server verbunden. Es ist auch möglich, zum Senden von Nachrichten an anderen Stellen in Ihrer app über die `IHubContext` Service. In diesem Artikel wird erläutert, wie auf eine SignalR `IHubContext` zum Senden von Benachrichtigungen an Clients von außerhalb eines Hubs.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(Herunterladen von)](xref:index#how-to-download-a-sample)

## <a name="get-an-instance-of-ihubcontext"></a>Eine Instanz der IHubContext abrufen

In ASP.NET Core SignalR und kann auf eine Instanz des `IHubContext` über Dependency Injection. Fügen Sie eine Instanz von `IHubContext` in einen Controller, Middleware oder andere DI-Dienst. Verwenden Sie die Instanz, um Nachrichten an Clients zu senden.

> [!NOTE]
> Dies unterscheidet sich von ASP.NET 4.x SignalR GlobalHost verwendet wird, Zugriff auf die `IHubContext`. ASP.NET Core verfügt über einen Dependency Injection-Framework, die die Notwendigkeit dieses globale Singleton entfernt.

### <a name="inject-an-instance-of-ihubcontext-in-a-controller"></a>Eine Instanz von IHubContext in einem controller

Fügen Sie eine Instanz von `IHubContext` in einen Controller, indem sie Sie dem Konstruktor hinzufügen:

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=12-19,57)]

Dank des Zugriffs auf eine Instanz von `IHubContext`, Sie können die Hub-Methoden aufrufen, als wären Sie auf dem Hub selbst.

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=21-25)]

### <a name="get-an-instance-of-ihubcontext-in-middleware"></a>Rufen Sie eine Instanz des IHubContext in-middleware

Zugriff die `IHubContext` innerhalb der middlewarepipeline wie folgt:

```csharp
app.Use(async (context, next) =>
{
    var hubContext = context.RequestServices
                            .GetRequiredService<IHubContext<MyHub>>();
    //...
});
```

> [!NOTE]
> Wenn hubmethoden werden aufgerufen, von außerhalb von die `Hub` Klasse, die keine Aufrufer den Aufruf zugeordnet ist. Es gibt also keinen Zugriff auf die `ConnectionId`, `Caller`, und `Others` Eigenschaften.

### <a name="inject-a-strongly-typed-hubcontext"></a>Eine stark typisierte HubContext einfügen

Um eine stark typisierte HubContext einzufügen, stellen Sie sicher erbt von Ihrem Hub `Hub<T>`. Einfügen von mithilfe der `IHubContext<THub, T>` Schnittstelle statt `IHubContext<THub>`.

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

## <a name="related-resources"></a>Weitere Informationen

* [Erste Schritte](xref:tutorials/signalr)
* [Hubs](xref:signalr/hubs)
* [Veröffentlichen in Azure](xref:signalr/publish-to-azure-web-app)

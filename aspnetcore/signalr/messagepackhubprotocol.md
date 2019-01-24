---
title: Verwenden von MessagePack Hub-Protokoll in SignalR für ASP.NET Core
author: bradygaster
description: Fügen Sie in ASP.NET Core SignalR MessagePack Hub-Protokoll hinzu.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/04/2018
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: 2852ca93c62e706e9a5203625822c2fb954fd2b8
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54835608"
---
# <a name="use-messagepack-hub-protocol-in-signalr-for-aspnet-core"></a>Verwenden von MessagePack Hub-Protokoll in SignalR für ASP.NET Core

Durch [Brennan Conroy](https://github.com/BrennanConroy)

In diesem Artikel geht davon aus, der Reader befindet sich mit den Themen vertraut [Einstieg](xref:tutorials/signalr).

## <a name="what-is-messagepack"></a>Was ist MessagePack?

[MessagePack](https://msgpack.org/index.html) ist ein binäres Serialisierungsformat, das schnell und kompakt ist. Es ist hilfreich, wenn die Leistung und relevant sind, da kleinere Nachrichten, die im Vergleich zu erstellt [JSON](https://www.json.org/). Da es sich um ein binäres Format handelt, sind Nachrichten unlesbar, wenn es sich bei netzwerkablaufverfolgungen und die Protokolle ansehen, wenn die Bytes, die über einen MessagePack-Parser übergeben werden. SignalR verfügt über integrierte Unterstützung für das Format MessagePack und stellt APIs für Client und Server zu verwenden.

## <a name="configure-messagepack-on-the-server"></a>Konfigurieren Sie MessagePack, auf dem server

Um das MessagePack-Hub-Protokoll auf dem Server zu aktivieren, installieren die `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` Paket in Ihrer app. Fügen Sie in der Datei "Startup.cs" `AddMessagePackProtocol` auf die `AddSignalR` Aufruf MessagePack-Unterstützung auf dem Server zu aktivieren.

> [!NOTE]
> JSON ist standardmäßig aktiviert. Hinzufügen von MessagePack bietet Unterstützung für JSON- und MessagePack-Clients.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Zum Anpassen, wie MessagePack Ihrer Daten, formatieren soll `AddMessagePackProtocol` verwendet einen Delegaten für das Konfigurieren von Optionen. In diesen Delegaten dem `FormatterResolvers` Eigenschaft kann verwendet werden, um MessagePack Serialisierungsoptionen konfigurieren. Weitere Informationen zur Funktionsweise der Konfliktlöser finden Sie auf der MessagePack-Bibliothek unter [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Attribute können für die Objekte zu serialisieren, die zum definieren, wie sie behandelt werden sollen, verwendet werden.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

## <a name="configure-messagepack-on-the-client"></a>Konfigurieren Sie MessagePack auf dem Client.

> [!NOTE]
> JSON ist für die unterstützten Clients standardmäßig aktiviert. Clients können nur ein einzelnes Protokoll unterstützt. Hinzufügen, MessagePack unterstützen alle zuvor ersetzen konfigurierten Protokolle an.

### <a name="net-client"></a>.NET-Client

Um MessagePack im .NET-Client zu aktivieren, installieren die `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` -Paket, und rufen `AddMessagePackProtocol` auf `HubConnectionBuilder`.

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Dies `AddMessagePackProtocol` Aufruf nimmt einen Delegaten für das Konfigurieren von Optionen wie den Server.

### <a name="javascript-client"></a>JavaScript-client

MessagePack-Unterstützung für Javascript-Client wird bereitgestellt, durch die `@aspnet/signalr-protocol-msgpack` NPM-Paket.

```console
npm install @aspnet/signalr-protocol-msgpack
```

Nach der Installation des Npm-Pakets, das Modul direkt über ein JavaScript-Modul-Ladeprogramm verwendet oder in den Browser durch Verweisen auf importiert werden kann die *"node_modules"\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* Datei. In einem Browser die `msgpack5` Bibliothek muss auch verwiesen werden. Verwenden einer `<script>` Tag, um einen Verweis zu erstellen. Die Bibliothek finden Sie unter *node_modules\msgpack5\dist\msgpack5.js*.

> [!NOTE]
> Bei Verwendung der `<script>` -Element, das die Reihenfolge ist wichtig. Wenn *Signalr-Protokoll-msgpack.js* verwiesen wird, bevor Sie *msgpack5.js*, ein Fehler tritt auf, wenn es sich bei mit MessagePack eine Verbindung herstellen möchten. *SignalR.js* ist auch erforderlich, bevor *Signalr-Protokoll-msgpack.js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

Hinzufügen von `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` auf die `HubConnectionBuilder` konfiguriert den Client das Protokoll MessagePack beim Verbinden mit einem Server zu verwenden.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> Zu diesem Zeitpunkt stehen keine Konfigurationsoptionen für das Protokoll MessagePack auf dem JavaScript-Client zur Verfügung.

## <a name="related-resources"></a>Weitere Informationen

* [Erste Schritte](xref:tutorials/signalr)
* [.NET-Client](xref:signalr/dotnet-client)
* [JavaScript-Client](xref:signalr/javascript-client)

---
title: Verwenden von MessagePack Hub-Protokoll in SignalR für ASP.NET Core
author: bradygaster
description: Fügen Sie in ASP.NET Core SignalR MessagePack Hub-Protokoll hinzu.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 02/27/2019
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: 7742f6f8bb53fb3c299ff98ae52a0da519ff396c
ms.sourcegitcommit: 6ddd8a7675c1c1d997c8ab2d4498538e44954cac
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57400670"
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

### <a name="javascript-client"></a>JavaScript-Client

MessagePack-Unterstützung für JavaScript-Client wird bereitgestellt, durch die `@aspnet/signalr-protocol-msgpack` Npm-Paket.

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

## <a name="messagepack-quirks"></a>MessagePack Quirksmodus

Es gibt einige Probleme bei Verwendung des Hub-Protokolls MessagePack berücksichtigen.

### <a name="messagepack-is-case-sensitive"></a>MessagePack wird Groß-/Kleinschreibung beachtet

Das MessagePack-Protokoll ist Groß-/Kleinschreibung beachtet. Betrachten Sie beispielsweise die folgenden C# Klasse:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Wenn von der JavaScript-Client zu senden, müssen Sie verwenden `PascalCased` Eigenschaftennamen, da die Groß-/Kleinschreibung übereinstimmen muss die C# Klasse genau. Zum Beispiel:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

Mithilfe von `camelCased` Namen wird nicht ordnungsgemäß Binden an die C# Klasse. Sie können diese umgehen mithilfe der `Key` Attribut, um einen anderen Namen für die Eigenschaft MessagePack anzugeben. Weitere Informationen finden Sie unter [der MessagePack-CSharp-Dokumentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime.Kind wird nicht beibehalten werden, bei der Serialisierung oder Deserialisierung

Das Protokoll MessagePack nicht die Möglichkeit, codieren die `Kind` Wert eine `DateTime`. Daher annimmt, wenn ein Datum zu deserialisieren, das MessagePack-Hub-Protokoll, dass das eingehende Datum in UTC-Format ist. Wenn Sie mit der Sie arbeiten `DateTime` Werte in Ortszeit, wir empfehlen in UTC konvertiert, bevor diese gesendet werden. Konvertieren sie von der UTC in eine lokale Zeit, wenn Sie diese erhalten.

Weitere Informationen zu dieser Beschränkung finden Sie unter GitHub Problem [Aspnet/SignalR-2632](https://github.com/aspnet/SignalR/issues/2632).

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime.MinValue wird vom MessagePack in JavaScript nicht unterstützt.

Die [msgpack5](https://github.com/mcollina/msgpack5) Bibliothek ein, die den SignalR JavaScript-Client unterstützt nicht die `timestamp96` MessagePack geben. Dieser Typ wird verwendet, um sehr große Date-Werten (entweder einem frühen Zeitpunkt in der Vergangenheit oder sehr weit in der Zukunft) zu codieren. Der Wert des `DateTime.MinValue` ist `January 1, 0001` die müssen codiert werden, einem `timestamp96` Wert. Aufgrund dieser, senden `DateTime.MinValue` zu einer JavaScript-Client wird nicht unterstützt. Wenn `DateTime.MinValue` empfangen wird durch den JavaScript-Client, der folgende Fehler ausgelöst wird:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

In der Regel `DateTime.MinValue` wird zur Codierung verwendet eine "missing" oder `null` Wert. Wenn Sie diesen Wert in MessagePack codieren möchten, verwenden Sie eine auf NULL festlegbare `DateTime` Wert (`DateTime?`) oder ein separates codieren `bool` Wert, der angibt, wenn das Datum vorhanden ist.

Weitere Informationen zu dieser Beschränkung finden Sie unter GitHub Problem [Aspnet/SignalR-2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>MessagePack-Unterstützung in kompilierungsumgebung "ahead-of-Time"

Die [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp) Bibliothek, die von der .NET Client und Server verwendet codegenerierung verwendet, um Serialisierung zu optimieren. Daher nicht standardmäßig in Umgebungen unterstützt, die Kompilierung von "ahead-of-Time" (z. B. Xamarin.IOS oder Unity) verwenden. Es ist möglich, MessagePack in diesen Umgebungen verwenden, indem"vor" die Serialisierungs-/Deserialisierungsprogramm Code generiert. Weitere Informationen finden Sie unter [der MessagePack-CSharp-Dokumentation](https://github.com/neuecc/MessagePack-CSharp#pre-code-generationunityxamarin-supports). Nachdem Sie die Serialisierungsprogramme vorab generiert haben, können Sie sie mit der Konfiguration-Delegat, der an übergebene registrieren `AddMessagePackProtocol`:

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a>Überprüfungen sind strenger in MessagePack

Das JSON-Hub-Protokoll führt Konvertierungen während der Deserialisierung. Beispielsweise verfügt das eingehende Objekt ein Eigenschaftswerts ist, die eine Zahl (`{ foo: 42 }`) die Eigenschaft für die Klasse .NET weist jedoch Typ `string`, wird der Wert konvertiert werden. MessagePack, allerdings wird diese Konvertierung nicht ausgeführt und löst eine Ausnahme, die angezeigt werden, im Server-echtzeitprotokolle (und in der Konsole):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Weitere Informationen zu dieser Beschränkung finden Sie unter GitHub Problem [Aspnet/SignalR-2937](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Weitere Informationen

* [Erste Schritte](xref:tutorials/signalr)
* [.NET-Client](xref:signalr/dotnet-client)
* [JavaScript-Client](xref:signalr/javascript-client)

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
# <a name="use-messagepack-hub-protocol-in-signalr-for-aspnet-core"></a><span data-ttu-id="22230-103">Verwenden von MessagePack Hub-Protokoll in SignalR für ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="22230-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

<span data-ttu-id="22230-104">Durch [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="22230-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

<span data-ttu-id="22230-105">In diesem Artikel geht davon aus, der Reader befindet sich mit den Themen vertraut [Einstieg](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="22230-105">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="22230-106">Was ist MessagePack?</span><span class="sxs-lookup"><span data-stu-id="22230-106">What is MessagePack?</span></span>

<span data-ttu-id="22230-107">[MessagePack](https://msgpack.org/index.html) ist ein binäres Serialisierungsformat, das schnell und kompakt ist.</span><span class="sxs-lookup"><span data-stu-id="22230-107">[MessagePack](https://msgpack.org/index.html) is a binary serialization format that is fast and compact.</span></span> <span data-ttu-id="22230-108">Es ist hilfreich, wenn die Leistung und relevant sind, da kleinere Nachrichten, die im Vergleich zu erstellt [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="22230-108">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="22230-109">Da es sich um ein binäres Format handelt, sind Nachrichten unlesbar, wenn es sich bei netzwerkablaufverfolgungen und die Protokolle ansehen, wenn die Bytes, die über einen MessagePack-Parser übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="22230-109">Because it's a binary format, messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="22230-110">SignalR verfügt über integrierte Unterstützung für das Format MessagePack und stellt APIs für Client und Server zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="22230-110">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="22230-111">Konfigurieren Sie MessagePack, auf dem server</span><span class="sxs-lookup"><span data-stu-id="22230-111">Configure MessagePack on the server</span></span>

<span data-ttu-id="22230-112">Um das MessagePack-Hub-Protokoll auf dem Server zu aktivieren, installieren die `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` Paket in Ihrer app.</span><span class="sxs-lookup"><span data-stu-id="22230-112">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="22230-113">Fügen Sie in der Datei "Startup.cs" `AddMessagePackProtocol` auf die `AddSignalR` Aufruf MessagePack-Unterstützung auf dem Server zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="22230-113">In the Startup.cs file add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="22230-114">JSON ist standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="22230-114">JSON is enabled by default.</span></span> <span data-ttu-id="22230-115">Hinzufügen von MessagePack bietet Unterstützung für JSON- und MessagePack-Clients.</span><span class="sxs-lookup"><span data-stu-id="22230-115">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="22230-116">Zum Anpassen, wie MessagePack Ihrer Daten, formatieren soll `AddMessagePackProtocol` verwendet einen Delegaten für das Konfigurieren von Optionen.</span><span class="sxs-lookup"><span data-stu-id="22230-116">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="22230-117">In diesen Delegaten dem `FormatterResolvers` Eigenschaft kann verwendet werden, um MessagePack Serialisierungsoptionen konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="22230-117">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="22230-118">Weitere Informationen zur Funktionsweise der Konfliktlöser finden Sie auf der MessagePack-Bibliothek unter [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="22230-118">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="22230-119">Attribute können für die Objekte zu serialisieren, die zum definieren, wie sie behandelt werden sollen, verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="22230-119">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="22230-120">Konfigurieren Sie MessagePack auf dem Client.</span><span class="sxs-lookup"><span data-stu-id="22230-120">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="22230-121">JSON ist für die unterstützten Clients standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="22230-121">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="22230-122">Clients können nur ein einzelnes Protokoll unterstützt.</span><span class="sxs-lookup"><span data-stu-id="22230-122">Clients can only support a single protocol.</span></span> <span data-ttu-id="22230-123">Hinzufügen, MessagePack unterstützen alle zuvor ersetzen konfigurierten Protokolle an.</span><span class="sxs-lookup"><span data-stu-id="22230-123">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="22230-124">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="22230-124">.NET client</span></span>

<span data-ttu-id="22230-125">Um MessagePack im .NET-Client zu aktivieren, installieren die `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` -Paket, und rufen `AddMessagePackProtocol` auf `HubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="22230-125">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="22230-126">Dies `AddMessagePackProtocol` Aufruf nimmt einen Delegaten für das Konfigurieren von Optionen wie den Server.</span><span class="sxs-lookup"><span data-stu-id="22230-126">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="22230-127">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="22230-127">JavaScript client</span></span>

<span data-ttu-id="22230-128">MessagePack-Unterstützung für JavaScript-Client wird bereitgestellt, durch die `@aspnet/signalr-protocol-msgpack` Npm-Paket.</span><span class="sxs-lookup"><span data-stu-id="22230-128">MessagePack support for the JavaScript client is provided by the `@aspnet/signalr-protocol-msgpack` npm package.</span></span>

```console
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="22230-129">Nach der Installation des Npm-Pakets, das Modul direkt über ein JavaScript-Modul-Ladeprogramm verwendet oder in den Browser durch Verweisen auf importiert werden kann die *"node_modules"\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* Datei.</span><span class="sxs-lookup"><span data-stu-id="22230-129">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the *node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* file.</span></span> <span data-ttu-id="22230-130">In einem Browser die `msgpack5` Bibliothek muss auch verwiesen werden.</span><span class="sxs-lookup"><span data-stu-id="22230-130">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="22230-131">Verwenden einer `<script>` Tag, um einen Verweis zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="22230-131">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="22230-132">Die Bibliothek finden Sie unter *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="22230-132">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="22230-133">Bei Verwendung der `<script>` -Element, das die Reihenfolge ist wichtig.</span><span class="sxs-lookup"><span data-stu-id="22230-133">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="22230-134">Wenn *Signalr-Protokoll-msgpack.js* verwiesen wird, bevor Sie *msgpack5.js*, ein Fehler tritt auf, wenn es sich bei mit MessagePack eine Verbindung herstellen möchten.</span><span class="sxs-lookup"><span data-stu-id="22230-134">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="22230-135">*SignalR.js* ist auch erforderlich, bevor *Signalr-Protokoll-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="22230-135">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="22230-136">Hinzufügen von `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` auf die `HubConnectionBuilder` konfiguriert den Client das Protokoll MessagePack beim Verbinden mit einem Server zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="22230-136">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="22230-137">Zu diesem Zeitpunkt stehen keine Konfigurationsoptionen für das Protokoll MessagePack auf dem JavaScript-Client zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="22230-137">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="22230-138">MessagePack Quirksmodus</span><span class="sxs-lookup"><span data-stu-id="22230-138">MessagePack quirks</span></span>

<span data-ttu-id="22230-139">Es gibt einige Probleme bei Verwendung des Hub-Protokolls MessagePack berücksichtigen.</span><span class="sxs-lookup"><span data-stu-id="22230-139">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="22230-140">MessagePack wird Groß-/Kleinschreibung beachtet</span><span class="sxs-lookup"><span data-stu-id="22230-140">MessagePack is case-sensitive</span></span>

<span data-ttu-id="22230-141">Das MessagePack-Protokoll ist Groß-/Kleinschreibung beachtet.</span><span class="sxs-lookup"><span data-stu-id="22230-141">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="22230-142">Betrachten Sie beispielsweise die folgenden C# Klasse:</span><span class="sxs-lookup"><span data-stu-id="22230-142">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="22230-143">Wenn von der JavaScript-Client zu senden, müssen Sie verwenden `PascalCased` Eigenschaftennamen, da die Groß-/Kleinschreibung übereinstimmen muss die C# Klasse genau.</span><span class="sxs-lookup"><span data-stu-id="22230-143">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="22230-144">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="22230-144">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="22230-145">Mithilfe von `camelCased` Namen wird nicht ordnungsgemäß Binden an die C# Klasse.</span><span class="sxs-lookup"><span data-stu-id="22230-145">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="22230-146">Sie können diese umgehen mithilfe der `Key` Attribut, um einen anderen Namen für die Eigenschaft MessagePack anzugeben.</span><span class="sxs-lookup"><span data-stu-id="22230-146">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="22230-147">Weitere Informationen finden Sie unter [der MessagePack-CSharp-Dokumentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="22230-147">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="22230-148">DateTime.Kind wird nicht beibehalten werden, bei der Serialisierung oder Deserialisierung</span><span class="sxs-lookup"><span data-stu-id="22230-148">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="22230-149">Das Protokoll MessagePack nicht die Möglichkeit, codieren die `Kind` Wert eine `DateTime`.</span><span class="sxs-lookup"><span data-stu-id="22230-149">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="22230-150">Daher annimmt, wenn ein Datum zu deserialisieren, das MessagePack-Hub-Protokoll, dass das eingehende Datum in UTC-Format ist.</span><span class="sxs-lookup"><span data-stu-id="22230-150">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="22230-151">Wenn Sie mit der Sie arbeiten `DateTime` Werte in Ortszeit, wir empfehlen in UTC konvertiert, bevor diese gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="22230-151">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="22230-152">Konvertieren sie von der UTC in eine lokale Zeit, wenn Sie diese erhalten.</span><span class="sxs-lookup"><span data-stu-id="22230-152">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="22230-153">Weitere Informationen zu dieser Beschränkung finden Sie unter GitHub Problem [Aspnet/SignalR-2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="22230-153">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="22230-154">DateTime.MinValue wird vom MessagePack in JavaScript nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="22230-154">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="22230-155">Die [msgpack5](https://github.com/mcollina/msgpack5) Bibliothek ein, die den SignalR JavaScript-Client unterstützt nicht die `timestamp96` MessagePack geben.</span><span class="sxs-lookup"><span data-stu-id="22230-155">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="22230-156">Dieser Typ wird verwendet, um sehr große Date-Werten (entweder einem frühen Zeitpunkt in der Vergangenheit oder sehr weit in der Zukunft) zu codieren.</span><span class="sxs-lookup"><span data-stu-id="22230-156">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="22230-157">Der Wert des `DateTime.MinValue` ist `January 1, 0001` die müssen codiert werden, einem `timestamp96` Wert.</span><span class="sxs-lookup"><span data-stu-id="22230-157">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="22230-158">Aufgrund dieser, senden `DateTime.MinValue` zu einer JavaScript-Client wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="22230-158">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="22230-159">Wenn `DateTime.MinValue` empfangen wird durch den JavaScript-Client, der folgende Fehler ausgelöst wird:</span><span class="sxs-lookup"><span data-stu-id="22230-159">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="22230-160">In der Regel `DateTime.MinValue` wird zur Codierung verwendet eine "missing" oder `null` Wert.</span><span class="sxs-lookup"><span data-stu-id="22230-160">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="22230-161">Wenn Sie diesen Wert in MessagePack codieren möchten, verwenden Sie eine auf NULL festlegbare `DateTime` Wert (`DateTime?`) oder ein separates codieren `bool` Wert, der angibt, wenn das Datum vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="22230-161">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="22230-162">Weitere Informationen zu dieser Beschränkung finden Sie unter GitHub Problem [Aspnet/SignalR-2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="22230-162">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="22230-163">MessagePack-Unterstützung in kompilierungsumgebung "ahead-of-Time"</span><span class="sxs-lookup"><span data-stu-id="22230-163">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="22230-164">Die [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp) Bibliothek, die von der .NET Client und Server verwendet codegenerierung verwendet, um Serialisierung zu optimieren.</span><span class="sxs-lookup"><span data-stu-id="22230-164">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="22230-165">Daher nicht standardmäßig in Umgebungen unterstützt, die Kompilierung von "ahead-of-Time" (z. B. Xamarin.IOS oder Unity) verwenden.</span><span class="sxs-lookup"><span data-stu-id="22230-165">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="22230-166">Es ist möglich, MessagePack in diesen Umgebungen verwenden, indem"vor" die Serialisierungs-/Deserialisierungsprogramm Code generiert.</span><span class="sxs-lookup"><span data-stu-id="22230-166">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="22230-167">Weitere Informationen finden Sie unter [der MessagePack-CSharp-Dokumentation](https://github.com/neuecc/MessagePack-CSharp#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="22230-167">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="22230-168">Nachdem Sie die Serialisierungsprogramme vorab generiert haben, können Sie sie mit der Konfiguration-Delegat, der an übergebene registrieren `AddMessagePackProtocol`:</span><span class="sxs-lookup"><span data-stu-id="22230-168">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="22230-169">Überprüfungen sind strenger in MessagePack</span><span class="sxs-lookup"><span data-stu-id="22230-169">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="22230-170">Das JSON-Hub-Protokoll führt Konvertierungen während der Deserialisierung.</span><span class="sxs-lookup"><span data-stu-id="22230-170">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="22230-171">Beispielsweise verfügt das eingehende Objekt ein Eigenschaftswerts ist, die eine Zahl (`{ foo: 42 }`) die Eigenschaft für die Klasse .NET weist jedoch Typ `string`, wird der Wert konvertiert werden.</span><span class="sxs-lookup"><span data-stu-id="22230-171">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="22230-172">MessagePack, allerdings wird diese Konvertierung nicht ausgeführt und löst eine Ausnahme, die angezeigt werden, im Server-echtzeitprotokolle (und in der Konsole):</span><span class="sxs-lookup"><span data-stu-id="22230-172">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="22230-173">Weitere Informationen zu dieser Beschränkung finden Sie unter GitHub Problem [Aspnet/SignalR-2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="22230-173">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="22230-174">Weitere Informationen</span><span class="sxs-lookup"><span data-stu-id="22230-174">Related resources</span></span>

* [<span data-ttu-id="22230-175">Erste Schritte</span><span class="sxs-lookup"><span data-stu-id="22230-175">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="22230-176">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="22230-176">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="22230-177">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="22230-177">JavaScript client</span></span>](xref:signalr/javascript-client)

---
title: Verwenden des messagepack-Hub-Protokolls in SignalR für ASP.net Core
author: bradygaster
description: Fügen Sie ASP.net Core das messagepack-Hub-Protokoll hinzu SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/13/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: ff78321cba11b1c91a12b5c777c505b4c9b85309
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408317"
---
# <a name="use-messagepack-hub-protocol-in-signalr-for-aspnet-core"></a><span data-ttu-id="6fe98-103">Verwenden des messagepack-Hub-Protokolls in SignalR für ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="6fe98-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="6fe98-104">In diesem Artikel wird davon ausgegangen, dass der Reader mit den Themen in " [Get Started](xref:tutorials/signalr)" vertraut ist.</span><span class="sxs-lookup"><span data-stu-id="6fe98-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="6fe98-105">Was ist messagepack?</span><span class="sxs-lookup"><span data-stu-id="6fe98-105">What is MessagePack?</span></span>

<span data-ttu-id="6fe98-106">[Messagepack](https://msgpack.org/index.html) ist ein schnelles und kompaktes binäres Serialisierungsformat.</span><span class="sxs-lookup"><span data-stu-id="6fe98-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="6fe98-107">Dies ist hilfreich, wenn Leistung und Bandbreite wichtig sind, da im Vergleich zu [JSON](https://www.json.org/)kleinere Nachrichten erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="6fe98-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="6fe98-108">Die binären Nachrichten sind bei der Betrachtung von Netzwerk Ablauf Verfolgungen und-Protokollen nicht lesbar, es sei denn, die Bytes werden über einen messagepack-Parser übergeben.</span><span class="sxs-lookup"><span data-stu-id="6fe98-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="6fe98-109">verfügt über integrierte Unterstützung für das messagepack-Format und stellt APIs für den Client und den Server zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="6fe98-109"> has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="6fe98-110">Konfigurieren von messagepack auf dem Server</span><span class="sxs-lookup"><span data-stu-id="6fe98-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="6fe98-111">Um das messagepack-Hub-Protokoll auf dem Server zu aktivieren, installieren `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` Sie das Paket in Ihrer APP.</span><span class="sxs-lookup"><span data-stu-id="6fe98-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="6fe98-112">Fügen Sie in der- `Startup.ConfigureServices` Methode dem-Befehl hinzu, `AddMessagePackProtocol` um die `AddSignalR` Unterstützung von messagepack auf dem Server zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="6fe98-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="6fe98-113">JSON ist standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="6fe98-113">JSON is enabled by default.</span></span> <span data-ttu-id="6fe98-114">Das Hinzufügen von messagepack ermöglicht die Unterstützung für JSON-und messagepack-Clients.</span><span class="sxs-lookup"><span data-stu-id="6fe98-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="6fe98-115">Zum Anpassen der Art und Weise, wie messagepack Ihre Daten formatiert, verwendet einen Delegaten `AddMessagePackProtocol` zum Konfigurieren von Optionen.</span><span class="sxs-lookup"><span data-stu-id="6fe98-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="6fe98-116">In diesem Delegaten kann die- `SerializerOptions` Eigenschaft verwendet werden, um messagepack-Serialisierungsoptionen zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="6fe98-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="6fe98-117">Weitere Informationen zur Funktionsweise der Konflikt Löser finden Sie in der messagepack-Bibliothek unter [messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="6fe98-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="6fe98-118">Attribute können für die Objekte verwendet werden, die Sie serialisieren möchten, um zu definieren, wie Sie behandelt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="6fe98-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(new CustomResolver())
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

> [!WARNING]
> <span data-ttu-id="6fe98-119">Es wird dringend empfohlen, [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) zu überprüfen und die empfohlenen Patches anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="6fe98-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="6fe98-120">Zum Beispiel wird `.WithSecurity(MessagePackSecurity.UntrustedData)` beim Ersetzen von aufgerufen `SerializerOptions` .</span><span class="sxs-lookup"><span data-stu-id="6fe98-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="6fe98-121">Konfigurieren von messagepack auf dem Client</span><span class="sxs-lookup"><span data-stu-id="6fe98-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="6fe98-122">JSON ist für die unterstützten Clients standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="6fe98-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="6fe98-123">Clients können nur ein einzelnes Protokoll unterstützen.</span><span class="sxs-lookup"><span data-stu-id="6fe98-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="6fe98-124">Durch Hinzufügen der Unterstützung von messagepack werden alle zuvor konfigurierten Protokolle ersetzt.</span><span class="sxs-lookup"><span data-stu-id="6fe98-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="6fe98-125">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="6fe98-125">.NET client</span></span>

<span data-ttu-id="6fe98-126">Um messagepack im .NET-Client zu aktivieren, installieren Sie das `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` Paket, und wenden Sie `AddMessagePackProtocol` an `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="6fe98-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="6fe98-127">Dieser `AddMessagePackProtocol` Aufruf verwendet einen Delegaten zum Konfigurieren von Optionen wie dem Server.</span><span class="sxs-lookup"><span data-stu-id="6fe98-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="6fe98-128">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="6fe98-128">JavaScript client</span></span>

<span data-ttu-id="6fe98-129">Die Unterstützung von messagepack für den JavaScript-Client wird durch das [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) NPM-Paket bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="6fe98-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="6fe98-130">Installieren Sie das Paket, indem Sie den folgenden Befehl in einer Befehlsshell ausführen:</span><span class="sxs-lookup"><span data-stu-id="6fe98-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="6fe98-131">Nach der Installation des NPM-Pakets kann das Modul direkt über ein JavaScript-Modul Lade Modul verwendet oder in den Browser importiert werden, indem Sie auf die folgende Datei verweisen:</span><span class="sxs-lookup"><span data-stu-id="6fe98-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="6fe98-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="6fe98-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="6fe98-133">In einem Browser muss auf die `msgpack5` Bibliothek ebenfalls verwiesen werden.</span><span class="sxs-lookup"><span data-stu-id="6fe98-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="6fe98-134">Verwenden Sie ein- `<script>` Tag, um einen Verweis zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="6fe98-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="6fe98-135">Die Bibliothek finden Sie unter *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="6fe98-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="6fe98-136">Wenn Sie das- `<script>` Element verwenden, ist die Reihenfolge wichtig.</span><span class="sxs-lookup"><span data-stu-id="6fe98-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="6fe98-137">Wenn auf *signalr-protocol-msgpack.js* vor *msgpack5.js*verwiesen wird, tritt ein Fehler auf, wenn versucht wird, eine Verbindung mit messagepack herzustellen.</span><span class="sxs-lookup"><span data-stu-id="6fe98-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="6fe98-138">*signalr.js* ist auch erforderlich, bevor *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="6fe98-138">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="6fe98-139">Durch das Hinzufügen von `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` zum `HubConnectionBuilder` wird der Client für die Verwendung des messagepack-Protokolls beim Herstellen einer Verbindung mit einem Server konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="6fe98-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="6fe98-140">Zurzeit gibt es keine Konfigurationsoptionen für das messagepack-Protokoll auf dem JavaScript-Client.</span><span class="sxs-lookup"><span data-stu-id="6fe98-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="6fe98-141">Messagepack-Quiri</span><span class="sxs-lookup"><span data-stu-id="6fe98-141">MessagePack quirks</span></span>

<span data-ttu-id="6fe98-142">Bei der Verwendung des messagepack-Hub-Protokolls sind einige Aspekte zu beachten.</span><span class="sxs-lookup"><span data-stu-id="6fe98-142">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="6fe98-143">Beim messagepack wird die Groß-/Kleinschreibung beachtet</span><span class="sxs-lookup"><span data-stu-id="6fe98-143">MessagePack is case-sensitive</span></span>

<span data-ttu-id="6fe98-144">Beim messagepack-Protokoll wird die Groß-/Kleinschreibung beachtet.</span><span class="sxs-lookup"><span data-stu-id="6fe98-144">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="6fe98-145">Sehen Sie sich beispielsweise die folgende c#-Klasse an:</span><span class="sxs-lookup"><span data-stu-id="6fe98-145">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="6fe98-146">Beim Senden vom JavaScript-Client müssen Sie `PascalCased` Eigenschaftsnamen verwenden, da die Groß-/Kleinschreibung genau mit der c#-Klasse übereinstimmen muss.</span><span class="sxs-lookup"><span data-stu-id="6fe98-146">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="6fe98-147">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="6fe98-147">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="6fe98-148">`camelCased`Die Verwendung von Namen wird nicht ordnungsgemäß an die c#-Klasse gebunden.</span><span class="sxs-lookup"><span data-stu-id="6fe98-148">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="6fe98-149">Sie können dieses Problem umgehen, indem Sie das- `Key` Attribut verwenden, um einen anderen Namen für die messagepack-Eigenschaft anzugeben.</span><span class="sxs-lookup"><span data-stu-id="6fe98-149">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="6fe98-150">Weitere Informationen finden Sie in [der Dokumentation zu messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="6fe98-150">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="6fe98-151">DateTime. Kind wird bei der Serialisierung/Deserialisierung nicht beibehalten.</span><span class="sxs-lookup"><span data-stu-id="6fe98-151">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="6fe98-152">Das messagepack-Protokoll bietet keine Möglichkeit, den `Kind` Wert einer zu codieren `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="6fe98-152">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="6fe98-153">Folglich konvertiert das messagepack-Hub-Protokoll beim Deserialisieren eines Datums in das UTC-Format, wenn der `DateTime.Kind` andernfalls die `DateTimeKind.Local` Zeit nicht berührt und unverändert übergibt.</span><span class="sxs-lookup"><span data-stu-id="6fe98-153">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="6fe98-154">Wenn Sie mit Werten arbeiten `DateTime` , empfiehlt es sich, vor dem Senden in die UTC zu wechseln.</span><span class="sxs-lookup"><span data-stu-id="6fe98-154">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="6fe98-155">Konvertieren Sie Sie von UTC in lokale Zeit, wenn Sie Sie empfangen.</span><span class="sxs-lookup"><span data-stu-id="6fe98-155">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="6fe98-156">"DateTime. MinValue" wird von messagepack in JavaScript nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="6fe98-156">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="6fe98-157">Die vom JavaScript-Client verwendete [msgpack5](https://github.com/mcollina/msgpack5) -Bibliothek SignalR unterstützt den `timestamp96` Typ in messagepack nicht.</span><span class="sxs-lookup"><span data-stu-id="6fe98-157">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="6fe98-158">Dieser Typ wird verwendet, um sehr große Datumswerte zu codieren (entweder sehr früh in der Vergangenheit oder sehr weit in der Zukunft).</span><span class="sxs-lookup"><span data-stu-id="6fe98-158">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="6fe98-159">Der Wert von `DateTime.MinValue` ist `January 1, 0001` , der in einem-Wert codiert werden muss `timestamp96` .</span><span class="sxs-lookup"><span data-stu-id="6fe98-159">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="6fe98-160">Aus diesem Grund wird das Senden `DateTime.MinValue` an einen JavaScript-Client nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="6fe98-160">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="6fe98-161">Wenn `DateTime.MinValue` vom JavaScript-Client empfangen wird, wird der folgende Fehler ausgelöst:</span><span class="sxs-lookup"><span data-stu-id="6fe98-161">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="6fe98-162">In der Regel `DateTime.MinValue` wird verwendet, um einen "Missing"-oder-Wert zu codieren `null` .</span><span class="sxs-lookup"><span data-stu-id="6fe98-162">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="6fe98-163">Wenn Sie diesen Wert in messagepack codieren müssen, verwenden Sie einen Werte zulässt- `DateTime` Wert ( `DateTime?` ), oder codieren Sie einen separaten Wert, `bool` der angibt, ob das Datum vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="6fe98-163">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="6fe98-164">Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub [-Problem ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="6fe98-164">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="6fe98-165">Unterstützung von messagepack in der "Ahead-of-Time"-Kompilierungs Umgebung</span><span class="sxs-lookup"><span data-stu-id="6fe98-165">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="6fe98-166">Die vom .NET-Client und-Server verwendete [messagepack-CSharp-](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) Bibliothek verwendet die Codegenerierung zum Optimieren der Serialisierung.</span><span class="sxs-lookup"><span data-stu-id="6fe98-166">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="6fe98-167">Daher wird Sie standardmäßig nicht in Umgebungen unterstützt, in denen die "vorab"-Kompilierung (z. b. xamarin IOS oder Unity) verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="6fe98-167">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="6fe98-168">Es ist möglich, das messagepack in diesen Umgebungen zu verwenden, indem der Serialisierungsprogramme-/Deserialisierungscode "vorab erstellt" wird.</span><span class="sxs-lookup"><span data-stu-id="6fe98-168">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="6fe98-169">Weitere Informationen finden Sie in [der Dokumentation zu messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span><span class="sxs-lookup"><span data-stu-id="6fe98-169">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="6fe98-170">Nachdem Sie die serialisierungsinitialisierer vorab generiert haben, können Sie Sie mit dem an folgenden-Konfigurations Delegaten registrieren `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="6fe98-170">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        StaticCompositeResolver.Instance.Register(
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        );
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(StaticCompositeResolver.Instance)
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="6fe98-171">Typüberprüfungen sind in messagepack strenger.</span><span class="sxs-lookup"><span data-stu-id="6fe98-171">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="6fe98-172">Das JSON Hub-Protokoll führt während der Deserialisierung Typkonvertierungen durch.</span><span class="sxs-lookup"><span data-stu-id="6fe98-172">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="6fe98-173">Wenn das eingehende Objekt z. b. einen-Eigenschafts Wert hat, der eine Zahl () ist, `{ foo: 42 }` aber die-Eigenschaft der .NET-Klasse vom Typ ist `string` , wird der Wert konvertiert.</span><span class="sxs-lookup"><span data-stu-id="6fe98-173">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="6fe98-174">Allerdings führt messagepack diese Konvertierung nicht durch und löst eine Ausnahme aus, die in serverseitigen Protokollen (und in der-Konsole) angezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="6fe98-174">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="6fe98-175">Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub [-Problem ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="6fe98-175">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="6fe98-176">Zugehörige Ressourcen</span><span class="sxs-lookup"><span data-stu-id="6fe98-176">Related resources</span></span>

* [<span data-ttu-id="6fe98-177">Erste Schritte</span><span class="sxs-lookup"><span data-stu-id="6fe98-177">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="6fe98-178">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="6fe98-178">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="6fe98-179">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="6fe98-179">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="6fe98-180">In diesem Artikel wird davon ausgegangen, dass der Reader mit den Themen in " [Get Started](xref:tutorials/signalr)" vertraut ist.</span><span class="sxs-lookup"><span data-stu-id="6fe98-180">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="6fe98-181">Was ist messagepack?</span><span class="sxs-lookup"><span data-stu-id="6fe98-181">What is MessagePack?</span></span>

<span data-ttu-id="6fe98-182">[Messagepack](https://msgpack.org/index.html) ist ein schnelles und kompaktes binäres Serialisierungsformat.</span><span class="sxs-lookup"><span data-stu-id="6fe98-182">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="6fe98-183">Dies ist hilfreich, wenn Leistung und Bandbreite wichtig sind, da im Vergleich zu [JSON](https://www.json.org/)kleinere Nachrichten erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="6fe98-183">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="6fe98-184">Die binären Nachrichten sind bei der Betrachtung von Netzwerk Ablauf Verfolgungen und-Protokollen nicht lesbar, es sei denn, die Bytes werden über einen messagepack-Parser übergeben.</span><span class="sxs-lookup"><span data-stu-id="6fe98-184">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="6fe98-185">verfügt über integrierte Unterstützung für das messagepack-Format und stellt APIs für den Client und den Server zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="6fe98-185"> has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="6fe98-186">Konfigurieren von messagepack auf dem Server</span><span class="sxs-lookup"><span data-stu-id="6fe98-186">Configure MessagePack on the server</span></span>

<span data-ttu-id="6fe98-187">Um das messagepack-Hub-Protokoll auf dem Server zu aktivieren, installieren `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` Sie das Paket in Ihrer APP.</span><span class="sxs-lookup"><span data-stu-id="6fe98-187">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="6fe98-188">Fügen Sie in der- `Startup.ConfigureServices` Methode dem-Befehl hinzu, `AddMessagePackProtocol` um die `AddSignalR` Unterstützung von messagepack auf dem Server zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="6fe98-188">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="6fe98-189">JSON ist standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="6fe98-189">JSON is enabled by default.</span></span> <span data-ttu-id="6fe98-190">Das Hinzufügen von messagepack ermöglicht die Unterstützung für JSON-und messagepack-Clients.</span><span class="sxs-lookup"><span data-stu-id="6fe98-190">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="6fe98-191">Zum Anpassen der Art und Weise, wie messagepack Ihre Daten formatiert, verwendet einen Delegaten `AddMessagePackProtocol` zum Konfigurieren von Optionen.</span><span class="sxs-lookup"><span data-stu-id="6fe98-191">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="6fe98-192">In diesem Delegaten kann die- `FormatterResolvers` Eigenschaft verwendet werden, um messagepack-Serialisierungsoptionen zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="6fe98-192">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="6fe98-193">Weitere Informationen zur Funktionsweise der Konflikt Löser finden Sie in der messagepack-Bibliothek unter [messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="6fe98-193">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="6fe98-194">Attribute können für die Objekte verwendet werden, die Sie serialisieren möchten, um zu definieren, wie Sie behandelt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="6fe98-194">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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

> [!WARNING]
> <span data-ttu-id="6fe98-195">Es wird dringend empfohlen, [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) zu überprüfen und die empfohlenen Patches anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="6fe98-195">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="6fe98-196">Beispielsweise das Festlegen der `MessagePackSecurity.Active` statischen-Eigenschaft auf `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="6fe98-196">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="6fe98-197">Zum Festlegen `MessagePackSecurity.Active` von muss eine [1.9. x-Version von messagepack](https://www.nuget.org/packages/MessagePack/1.9.3)manuell installiert werden.</span><span class="sxs-lookup"><span data-stu-id="6fe98-197">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="6fe98-198">Durch `MessagePack` die Installation von 1.9. x werden die Versionen von aktualisiert SignalR .</span><span class="sxs-lookup"><span data-stu-id="6fe98-198">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="6fe98-199">Wenn `MessagePackSecurity.Active` nicht auf festgelegt ist `MessagePackSecurity.UntrustedData` , kann ein böswilliger Client einen Denial-of-Service-Angriff auslösen.</span><span class="sxs-lookup"><span data-stu-id="6fe98-199">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="6fe98-200">`MessagePackSecurity.Active`Wird in festgelegt `Program.Main` , wie im folgenden Code gezeigt:</span><span class="sxs-lookup"><span data-stu-id="6fe98-200">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="6fe98-201">Konfigurieren von messagepack auf dem Client</span><span class="sxs-lookup"><span data-stu-id="6fe98-201">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="6fe98-202">JSON ist für die unterstützten Clients standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="6fe98-202">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="6fe98-203">Clients können nur ein einzelnes Protokoll unterstützen.</span><span class="sxs-lookup"><span data-stu-id="6fe98-203">Clients can only support a single protocol.</span></span> <span data-ttu-id="6fe98-204">Durch Hinzufügen der Unterstützung von messagepack werden alle zuvor konfigurierten Protokolle ersetzt.</span><span class="sxs-lookup"><span data-stu-id="6fe98-204">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="6fe98-205">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="6fe98-205">.NET client</span></span>

<span data-ttu-id="6fe98-206">Um messagepack im .NET-Client zu aktivieren, installieren Sie das `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` Paket, und wenden Sie `AddMessagePackProtocol` an `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="6fe98-206">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="6fe98-207">Dieser `AddMessagePackProtocol` Aufruf verwendet einen Delegaten zum Konfigurieren von Optionen wie dem Server.</span><span class="sxs-lookup"><span data-stu-id="6fe98-207">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="6fe98-208">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="6fe98-208">JavaScript client</span></span>

<span data-ttu-id="6fe98-209">Die Unterstützung von messagepack für den JavaScript-Client wird durch das [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) NPM-Paket bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="6fe98-209">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="6fe98-210">Installieren Sie das Paket, indem Sie den folgenden Befehl in einer Befehlsshell ausführen:</span><span class="sxs-lookup"><span data-stu-id="6fe98-210">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="6fe98-211">Nach der Installation des NPM-Pakets kann das Modul direkt über ein JavaScript-Modul Lade Modul verwendet oder in den Browser importiert werden, indem Sie auf die folgende Datei verweisen:</span><span class="sxs-lookup"><span data-stu-id="6fe98-211">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="6fe98-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="6fe98-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="6fe98-213">In einem Browser muss auf die `msgpack5` Bibliothek ebenfalls verwiesen werden.</span><span class="sxs-lookup"><span data-stu-id="6fe98-213">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="6fe98-214">Verwenden Sie ein- `<script>` Tag, um einen Verweis zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="6fe98-214">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="6fe98-215">Die Bibliothek finden Sie unter *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="6fe98-215">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="6fe98-216">Wenn Sie das- `<script>` Element verwenden, ist die Reihenfolge wichtig.</span><span class="sxs-lookup"><span data-stu-id="6fe98-216">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="6fe98-217">Wenn auf *signalr-protocol-msgpack.js* vor *msgpack5.js*verwiesen wird, tritt ein Fehler auf, wenn versucht wird, eine Verbindung mit messagepack herzustellen.</span><span class="sxs-lookup"><span data-stu-id="6fe98-217">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="6fe98-218">*signalr.js* ist auch erforderlich, bevor *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="6fe98-218">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="6fe98-219">Durch das Hinzufügen von `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` zum `HubConnectionBuilder` wird der Client für die Verwendung des messagepack-Protokolls beim Herstellen einer Verbindung mit einem Server konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="6fe98-219">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="6fe98-220">Zurzeit gibt es keine Konfigurationsoptionen für das messagepack-Protokoll auf dem JavaScript-Client.</span><span class="sxs-lookup"><span data-stu-id="6fe98-220">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="6fe98-221">Messagepack-Quiri</span><span class="sxs-lookup"><span data-stu-id="6fe98-221">MessagePack quirks</span></span>

<span data-ttu-id="6fe98-222">Bei der Verwendung des messagepack-Hub-Protokolls sind einige Aspekte zu beachten.</span><span class="sxs-lookup"><span data-stu-id="6fe98-222">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="6fe98-223">Beim messagepack wird die Groß-/Kleinschreibung beachtet</span><span class="sxs-lookup"><span data-stu-id="6fe98-223">MessagePack is case-sensitive</span></span>

<span data-ttu-id="6fe98-224">Beim messagepack-Protokoll wird die Groß-/Kleinschreibung beachtet.</span><span class="sxs-lookup"><span data-stu-id="6fe98-224">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="6fe98-225">Sehen Sie sich beispielsweise die folgende c#-Klasse an:</span><span class="sxs-lookup"><span data-stu-id="6fe98-225">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="6fe98-226">Beim Senden vom JavaScript-Client müssen Sie `PascalCased` Eigenschaftsnamen verwenden, da die Groß-/Kleinschreibung genau mit der c#-Klasse übereinstimmen muss.</span><span class="sxs-lookup"><span data-stu-id="6fe98-226">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="6fe98-227">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="6fe98-227">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="6fe98-228">`camelCased`Die Verwendung von Namen wird nicht ordnungsgemäß an die c#-Klasse gebunden.</span><span class="sxs-lookup"><span data-stu-id="6fe98-228">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="6fe98-229">Sie können dieses Problem umgehen, indem Sie das- `Key` Attribut verwenden, um einen anderen Namen für die messagepack-Eigenschaft anzugeben.</span><span class="sxs-lookup"><span data-stu-id="6fe98-229">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="6fe98-230">Weitere Informationen finden Sie in [der Dokumentation zu messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="6fe98-230">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="6fe98-231">DateTime. Kind wird bei der Serialisierung/Deserialisierung nicht beibehalten.</span><span class="sxs-lookup"><span data-stu-id="6fe98-231">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="6fe98-232">Das messagepack-Protokoll bietet keine Möglichkeit, den `Kind` Wert einer zu codieren `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="6fe98-232">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="6fe98-233">Demzufolge geht das messagepack-Hub-Protokoll beim Deserialisieren eines Datums davon aus, dass das eingehende Datum im UTC-Format vorliegt.</span><span class="sxs-lookup"><span data-stu-id="6fe98-233">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="6fe98-234">Wenn Sie mit `DateTime` Werten in der Ortszeit arbeiten, empfiehlt es sich, vor dem Senden in die UTC zu wechseln.</span><span class="sxs-lookup"><span data-stu-id="6fe98-234">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="6fe98-235">Konvertieren Sie Sie von UTC in lokale Zeit, wenn Sie Sie empfangen.</span><span class="sxs-lookup"><span data-stu-id="6fe98-235">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="6fe98-236">Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub [-Problem ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="6fe98-236">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="6fe98-237">"DateTime. MinValue" wird von messagepack in JavaScript nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="6fe98-237">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="6fe98-238">Die vom JavaScript-Client verwendete [msgpack5](https://github.com/mcollina/msgpack5) -Bibliothek SignalR unterstützt den `timestamp96` Typ in messagepack nicht.</span><span class="sxs-lookup"><span data-stu-id="6fe98-238">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="6fe98-239">Dieser Typ wird verwendet, um sehr große Datumswerte zu codieren (entweder sehr früh in der Vergangenheit oder sehr weit in der Zukunft).</span><span class="sxs-lookup"><span data-stu-id="6fe98-239">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="6fe98-240">Der Wert von `DateTime.MinValue` ist `January 1, 0001` , der in einem-Wert codiert werden muss `timestamp96` .</span><span class="sxs-lookup"><span data-stu-id="6fe98-240">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="6fe98-241">Aus diesem Grund wird das Senden `DateTime.MinValue` an einen JavaScript-Client nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="6fe98-241">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="6fe98-242">Wenn `DateTime.MinValue` vom JavaScript-Client empfangen wird, wird der folgende Fehler ausgelöst:</span><span class="sxs-lookup"><span data-stu-id="6fe98-242">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="6fe98-243">In der Regel `DateTime.MinValue` wird verwendet, um einen "Missing"-oder-Wert zu codieren `null` .</span><span class="sxs-lookup"><span data-stu-id="6fe98-243">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="6fe98-244">Wenn Sie diesen Wert in messagepack codieren müssen, verwenden Sie einen Werte zulässt- `DateTime` Wert ( `DateTime?` ), oder codieren Sie einen separaten Wert, `bool` der angibt, ob das Datum vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="6fe98-244">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="6fe98-245">Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub [-Problem ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="6fe98-245">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="6fe98-246">Unterstützung von messagepack in der "Ahead-of-Time"-Kompilierungs Umgebung</span><span class="sxs-lookup"><span data-stu-id="6fe98-246">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="6fe98-247">Die vom .NET-Client und-Server verwendete [messagepack-CSharp-](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) Bibliothek verwendet die Codegenerierung zum Optimieren der Serialisierung.</span><span class="sxs-lookup"><span data-stu-id="6fe98-247">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="6fe98-248">Daher wird Sie standardmäßig nicht in Umgebungen unterstützt, in denen die "vorab"-Kompilierung (z. b. xamarin IOS oder Unity) verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="6fe98-248">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="6fe98-249">Es ist möglich, das messagepack in diesen Umgebungen zu verwenden, indem der Serialisierungsprogramme-/Deserialisierungscode "vorab erstellt" wird.</span><span class="sxs-lookup"><span data-stu-id="6fe98-249">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="6fe98-250">Weitere Informationen finden Sie in [der Dokumentation zu messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="6fe98-250">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="6fe98-251">Nachdem Sie die serialisierungsinitialisierer vorab generiert haben, können Sie Sie mit dem an folgenden-Konfigurations Delegaten registrieren `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="6fe98-251">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="6fe98-252">Typüberprüfungen sind in messagepack strenger.</span><span class="sxs-lookup"><span data-stu-id="6fe98-252">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="6fe98-253">Das JSON Hub-Protokoll führt während der Deserialisierung Typkonvertierungen durch.</span><span class="sxs-lookup"><span data-stu-id="6fe98-253">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="6fe98-254">Wenn das eingehende Objekt z. b. einen-Eigenschafts Wert hat, der eine Zahl () ist, `{ foo: 42 }` aber die-Eigenschaft der .NET-Klasse vom Typ ist `string` , wird der Wert konvertiert.</span><span class="sxs-lookup"><span data-stu-id="6fe98-254">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="6fe98-255">Allerdings führt messagepack diese Konvertierung nicht durch und löst eine Ausnahme aus, die in serverseitigen Protokollen (und in der-Konsole) angezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="6fe98-255">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="6fe98-256">Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub [-Problem ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="6fe98-256">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="6fe98-257">Zugehörige Ressourcen</span><span class="sxs-lookup"><span data-stu-id="6fe98-257">Related resources</span></span>

* [<span data-ttu-id="6fe98-258">Erste Schritte</span><span class="sxs-lookup"><span data-stu-id="6fe98-258">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="6fe98-259">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="6fe98-259">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="6fe98-260">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="6fe98-260">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6fe98-261">In diesem Artikel wird davon ausgegangen, dass der Reader mit den Themen in " [Get Started](xref:tutorials/signalr)" vertraut ist.</span><span class="sxs-lookup"><span data-stu-id="6fe98-261">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="6fe98-262">Was ist messagepack?</span><span class="sxs-lookup"><span data-stu-id="6fe98-262">What is MessagePack?</span></span>

<span data-ttu-id="6fe98-263">[Messagepack](https://msgpack.org/index.html) ist ein schnelles und kompaktes binäres Serialisierungsformat.</span><span class="sxs-lookup"><span data-stu-id="6fe98-263">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="6fe98-264">Dies ist hilfreich, wenn Leistung und Bandbreite wichtig sind, da im Vergleich zu [JSON](https://www.json.org/)kleinere Nachrichten erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="6fe98-264">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="6fe98-265">Die binären Nachrichten sind bei der Betrachtung von Netzwerk Ablauf Verfolgungen und-Protokollen nicht lesbar, es sei denn, die Bytes werden über einen messagepack-Parser übergeben.</span><span class="sxs-lookup"><span data-stu-id="6fe98-265">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="6fe98-266">verfügt über integrierte Unterstützung für das messagepack-Format und stellt APIs für den Client und den Server zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="6fe98-266"> has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="6fe98-267">Konfigurieren von messagepack auf dem Server</span><span class="sxs-lookup"><span data-stu-id="6fe98-267">Configure MessagePack on the server</span></span>

<span data-ttu-id="6fe98-268">Um das messagepack-Hub-Protokoll auf dem Server zu aktivieren, installieren `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` Sie das Paket in Ihrer APP.</span><span class="sxs-lookup"><span data-stu-id="6fe98-268">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="6fe98-269">Fügen Sie in der- `Startup.ConfigureServices` Methode dem-Befehl hinzu, `AddMessagePackProtocol` um die `AddSignalR` Unterstützung von messagepack auf dem Server zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="6fe98-269">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="6fe98-270">JSON ist standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="6fe98-270">JSON is enabled by default.</span></span> <span data-ttu-id="6fe98-271">Das Hinzufügen von messagepack ermöglicht die Unterstützung für JSON-und messagepack-Clients.</span><span class="sxs-lookup"><span data-stu-id="6fe98-271">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="6fe98-272">Zum Anpassen der Art und Weise, wie messagepack Ihre Daten formatiert, verwendet einen Delegaten `AddMessagePackProtocol` zum Konfigurieren von Optionen.</span><span class="sxs-lookup"><span data-stu-id="6fe98-272">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="6fe98-273">In diesem Delegaten kann die- `FormatterResolvers` Eigenschaft verwendet werden, um messagepack-Serialisierungsoptionen zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="6fe98-273">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="6fe98-274">Weitere Informationen zur Funktionsweise der Konflikt Löser finden Sie in der messagepack-Bibliothek unter [messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="6fe98-274">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="6fe98-275">Attribute können für die Objekte verwendet werden, die Sie serialisieren möchten, um zu definieren, wie Sie behandelt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="6fe98-275">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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

> [!WARNING]
> <span data-ttu-id="6fe98-276">Es wird dringend empfohlen, [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) zu überprüfen und die empfohlenen Patches anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="6fe98-276">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="6fe98-277">Beispielsweise das Festlegen der `MessagePackSecurity.Active` statischen-Eigenschaft auf `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="6fe98-277">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="6fe98-278">Zum Festlegen `MessagePackSecurity.Active` von muss eine [1.9. x-Version von messagepack](https://www.nuget.org/packages/MessagePack/1.9.3)manuell installiert werden.</span><span class="sxs-lookup"><span data-stu-id="6fe98-278">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="6fe98-279">Durch `MessagePack` die Installation von 1.9. x werden die Versionen von aktualisiert SignalR .</span><span class="sxs-lookup"><span data-stu-id="6fe98-279">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="6fe98-280">Wenn `MessagePackSecurity.Active` nicht auf festgelegt ist `MessagePackSecurity.UntrustedData` , kann ein böswilliger Client einen Denial-of-Service-Angriff auslösen.</span><span class="sxs-lookup"><span data-stu-id="6fe98-280">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="6fe98-281">`MessagePackSecurity.Active`Wird in festgelegt `Program.Main` , wie im folgenden Code gezeigt:</span><span class="sxs-lookup"><span data-stu-id="6fe98-281">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="6fe98-282">Konfigurieren von messagepack auf dem Client</span><span class="sxs-lookup"><span data-stu-id="6fe98-282">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="6fe98-283">JSON ist für die unterstützten Clients standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="6fe98-283">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="6fe98-284">Clients können nur ein einzelnes Protokoll unterstützen.</span><span class="sxs-lookup"><span data-stu-id="6fe98-284">Clients can only support a single protocol.</span></span> <span data-ttu-id="6fe98-285">Durch Hinzufügen der Unterstützung von messagepack werden alle zuvor konfigurierten Protokolle ersetzt.</span><span class="sxs-lookup"><span data-stu-id="6fe98-285">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="6fe98-286">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="6fe98-286">.NET client</span></span>

<span data-ttu-id="6fe98-287">Um messagepack im .NET-Client zu aktivieren, installieren Sie das `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` Paket, und wenden Sie `AddMessagePackProtocol` an `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="6fe98-287">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="6fe98-288">Dieser `AddMessagePackProtocol` Aufruf verwendet einen Delegaten zum Konfigurieren von Optionen wie dem Server.</span><span class="sxs-lookup"><span data-stu-id="6fe98-288">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="6fe98-289">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="6fe98-289">JavaScript client</span></span>

<span data-ttu-id="6fe98-290">Die Unterstützung von messagepack für den JavaScript-Client wird durch das [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) NPM-Paket bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="6fe98-290">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="6fe98-291">Installieren Sie das Paket, indem Sie den folgenden Befehl in einer Befehlsshell ausführen:</span><span class="sxs-lookup"><span data-stu-id="6fe98-291">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="6fe98-292">Nach der Installation des NPM-Pakets kann das Modul direkt über ein JavaScript-Modul Lade Modul verwendet oder in den Browser importiert werden, indem Sie auf die folgende Datei verweisen:</span><span class="sxs-lookup"><span data-stu-id="6fe98-292">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="6fe98-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="6fe98-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="6fe98-294">In einem Browser muss auf die `msgpack5` Bibliothek ebenfalls verwiesen werden.</span><span class="sxs-lookup"><span data-stu-id="6fe98-294">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="6fe98-295">Verwenden Sie ein- `<script>` Tag, um einen Verweis zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="6fe98-295">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="6fe98-296">Die Bibliothek finden Sie unter *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="6fe98-296">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="6fe98-297">Wenn Sie das- `<script>` Element verwenden, ist die Reihenfolge wichtig.</span><span class="sxs-lookup"><span data-stu-id="6fe98-297">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="6fe98-298">Wenn auf *signalr-protocol-msgpack.js* vor *msgpack5.js*verwiesen wird, tritt ein Fehler auf, wenn versucht wird, eine Verbindung mit messagepack herzustellen.</span><span class="sxs-lookup"><span data-stu-id="6fe98-298">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="6fe98-299">*signalr.js* ist auch erforderlich, bevor *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="6fe98-299">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="6fe98-300">Durch das Hinzufügen von `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` zum `HubConnectionBuilder` wird der Client für die Verwendung des messagepack-Protokolls beim Herstellen einer Verbindung mit einem Server konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="6fe98-300">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="6fe98-301">Zurzeit gibt es keine Konfigurationsoptionen für das messagepack-Protokoll auf dem JavaScript-Client.</span><span class="sxs-lookup"><span data-stu-id="6fe98-301">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="6fe98-302">Messagepack-Quiri</span><span class="sxs-lookup"><span data-stu-id="6fe98-302">MessagePack quirks</span></span>

<span data-ttu-id="6fe98-303">Bei der Verwendung des messagepack-Hub-Protokolls sind einige Aspekte zu beachten.</span><span class="sxs-lookup"><span data-stu-id="6fe98-303">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="6fe98-304">Beim messagepack wird die Groß-/Kleinschreibung beachtet</span><span class="sxs-lookup"><span data-stu-id="6fe98-304">MessagePack is case-sensitive</span></span>

<span data-ttu-id="6fe98-305">Beim messagepack-Protokoll wird die Groß-/Kleinschreibung beachtet.</span><span class="sxs-lookup"><span data-stu-id="6fe98-305">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="6fe98-306">Sehen Sie sich beispielsweise die folgende c#-Klasse an:</span><span class="sxs-lookup"><span data-stu-id="6fe98-306">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="6fe98-307">Beim Senden vom JavaScript-Client müssen Sie `PascalCased` Eigenschaftsnamen verwenden, da die Groß-/Kleinschreibung genau mit der c#-Klasse übereinstimmen muss.</span><span class="sxs-lookup"><span data-stu-id="6fe98-307">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="6fe98-308">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="6fe98-308">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="6fe98-309">`camelCased`Die Verwendung von Namen wird nicht ordnungsgemäß an die c#-Klasse gebunden.</span><span class="sxs-lookup"><span data-stu-id="6fe98-309">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="6fe98-310">Sie können dieses Problem umgehen, indem Sie das- `Key` Attribut verwenden, um einen anderen Namen für die messagepack-Eigenschaft anzugeben.</span><span class="sxs-lookup"><span data-stu-id="6fe98-310">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="6fe98-311">Weitere Informationen finden Sie in [der Dokumentation zu messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="6fe98-311">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="6fe98-312">DateTime. Kind wird bei der Serialisierung/Deserialisierung nicht beibehalten.</span><span class="sxs-lookup"><span data-stu-id="6fe98-312">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="6fe98-313">Das messagepack-Protokoll bietet keine Möglichkeit, den `Kind` Wert einer zu codieren `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="6fe98-313">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="6fe98-314">Demzufolge geht das messagepack-Hub-Protokoll beim Deserialisieren eines Datums davon aus, dass das eingehende Datum im UTC-Format vorliegt.</span><span class="sxs-lookup"><span data-stu-id="6fe98-314">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="6fe98-315">Wenn Sie mit `DateTime` Werten in der Ortszeit arbeiten, empfiehlt es sich, vor dem Senden in die UTC zu wechseln.</span><span class="sxs-lookup"><span data-stu-id="6fe98-315">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="6fe98-316">Konvertieren Sie Sie von UTC in lokale Zeit, wenn Sie Sie empfangen.</span><span class="sxs-lookup"><span data-stu-id="6fe98-316">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="6fe98-317">Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub [-Problem ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="6fe98-317">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="6fe98-318">"DateTime. MinValue" wird von messagepack in JavaScript nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="6fe98-318">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="6fe98-319">Die vom JavaScript-Client verwendete [msgpack5](https://github.com/mcollina/msgpack5) -Bibliothek SignalR unterstützt den `timestamp96` Typ in messagepack nicht.</span><span class="sxs-lookup"><span data-stu-id="6fe98-319">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="6fe98-320">Dieser Typ wird verwendet, um sehr große Datumswerte zu codieren (entweder sehr früh in der Vergangenheit oder sehr weit in der Zukunft).</span><span class="sxs-lookup"><span data-stu-id="6fe98-320">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="6fe98-321">Der Wert von `DateTime.MinValue` ist `January 1, 0001` , der in einem-Wert codiert werden muss `timestamp96` .</span><span class="sxs-lookup"><span data-stu-id="6fe98-321">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="6fe98-322">Aus diesem Grund wird das Senden `DateTime.MinValue` an einen JavaScript-Client nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="6fe98-322">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="6fe98-323">Wenn `DateTime.MinValue` vom JavaScript-Client empfangen wird, wird der folgende Fehler ausgelöst:</span><span class="sxs-lookup"><span data-stu-id="6fe98-323">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="6fe98-324">In der Regel `DateTime.MinValue` wird verwendet, um einen "Missing"-oder-Wert zu codieren `null` .</span><span class="sxs-lookup"><span data-stu-id="6fe98-324">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="6fe98-325">Wenn Sie diesen Wert in messagepack codieren müssen, verwenden Sie einen Werte zulässt- `DateTime` Wert ( `DateTime?` ), oder codieren Sie einen separaten Wert, `bool` der angibt, ob das Datum vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="6fe98-325">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="6fe98-326">Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub [-Problem ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="6fe98-326">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="6fe98-327">Unterstützung von messagepack in der "Ahead-of-Time"-Kompilierungs Umgebung</span><span class="sxs-lookup"><span data-stu-id="6fe98-327">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="6fe98-328">Die vom .NET-Client und-Server verwendete [messagepack-CSharp-](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) Bibliothek verwendet die Codegenerierung zum Optimieren der Serialisierung.</span><span class="sxs-lookup"><span data-stu-id="6fe98-328">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="6fe98-329">Daher wird Sie standardmäßig nicht in Umgebungen unterstützt, in denen die "vorab"-Kompilierung (z. b. xamarin IOS oder Unity) verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="6fe98-329">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="6fe98-330">Es ist möglich, das messagepack in diesen Umgebungen zu verwenden, indem der Serialisierungsprogramme-/Deserialisierungscode "vorab erstellt" wird.</span><span class="sxs-lookup"><span data-stu-id="6fe98-330">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="6fe98-331">Weitere Informationen finden Sie in [der Dokumentation zu messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="6fe98-331">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="6fe98-332">Nachdem Sie die serialisierungsinitialisierer vorab generiert haben, können Sie Sie mit dem an folgenden-Konfigurations Delegaten registrieren `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="6fe98-332">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="6fe98-333">Typüberprüfungen sind in messagepack strenger.</span><span class="sxs-lookup"><span data-stu-id="6fe98-333">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="6fe98-334">Das JSON Hub-Protokoll führt während der Deserialisierung Typkonvertierungen durch.</span><span class="sxs-lookup"><span data-stu-id="6fe98-334">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="6fe98-335">Wenn das eingehende Objekt z. b. einen-Eigenschafts Wert hat, der eine Zahl () ist, `{ foo: 42 }` aber die-Eigenschaft der .NET-Klasse vom Typ ist `string` , wird der Wert konvertiert.</span><span class="sxs-lookup"><span data-stu-id="6fe98-335">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="6fe98-336">Allerdings führt messagepack diese Konvertierung nicht durch und löst eine Ausnahme aus, die in serverseitigen Protokollen (und in der-Konsole) angezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="6fe98-336">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="6fe98-337">Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub [-Problem ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="6fe98-337">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="6fe98-338">Zugehörige Ressourcen</span><span class="sxs-lookup"><span data-stu-id="6fe98-338">Related resources</span></span>

* [<span data-ttu-id="6fe98-339">Erste Schritte</span><span class="sxs-lookup"><span data-stu-id="6fe98-339">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="6fe98-340">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="6fe98-340">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="6fe98-341">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="6fe98-341">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

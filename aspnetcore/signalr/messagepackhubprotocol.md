---
title: MessagePack Hub-Protokoll SignalR für ASP.NET Core verwenden
author: bradygaster
description: Fügen Sie MessagePack Hub SignalRProtocol zu ASP.NET Core hinzu.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/13/2020
no-loc:
- SignalR
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: bbc34d790387a96bb3b6f75e841b45685eb137ce
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277235"
---
# <a name="use-messagepack-hub-protocol-in-opno-locsignalr-for-aspnet-core"></a><span data-ttu-id="c6811-103">MessagePack Hub-Protokoll SignalR für ASP.NET Core verwenden</span><span class="sxs-lookup"><span data-stu-id="c6811-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="c6811-104">In diesem Artikel wird davon ausgegangen, dass der Leser mit den unter [Erste Schritte](xref:tutorials/signalr)behandelten Themen vertraut ist.</span><span class="sxs-lookup"><span data-stu-id="c6811-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="c6811-105">Was ist MessagePack?</span><span class="sxs-lookup"><span data-stu-id="c6811-105">What is MessagePack?</span></span>

<span data-ttu-id="c6811-106">[MessagePack](https://msgpack.org/index.html) ist ein schnelles und kompaktes binäres Serialisierungsformat.</span><span class="sxs-lookup"><span data-stu-id="c6811-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="c6811-107">Es ist nützlich, wenn Leistung und Bandbreite ein Problem darstellen, da es kleinere Nachrichten im Vergleich zu [JSON](https://www.json.org/)erzeugt.</span><span class="sxs-lookup"><span data-stu-id="c6811-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="c6811-108">Die binären Nachrichten sind beim Betrachten von Netzwerkablaufverfolgungen und -protokollen nicht lesbar, es sei denn, die Bytes werden über einen MessagePack-Parser übergeben.</span><span class="sxs-lookup"><span data-stu-id="c6811-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="c6811-109">hat integrierte Unterstützung für das MessagePack-Format und stellt APIs für den Client und Server bereit.</span><span class="sxs-lookup"><span data-stu-id="c6811-109"> has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="c6811-110">Konfigurieren von MessagePack auf dem Server</span><span class="sxs-lookup"><span data-stu-id="c6811-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="c6811-111">Um das MessagePack Hub-Protokoll auf `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` dem Server zu aktivieren, installieren Sie das Paket in Ihrer App.</span><span class="sxs-lookup"><span data-stu-id="c6811-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="c6811-112">Fügen `Startup.ConfigureServices` Sie `AddMessagePackProtocol` in der `AddSignalR` Methode dem Aufruf hinzu, um die MessagePack-Unterstützung auf dem Server zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="c6811-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="c6811-113">JSON ist standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="c6811-113">JSON is enabled by default.</span></span> <span data-ttu-id="c6811-114">Das Hinzufügen von MessagePack ermöglicht die Unterstützung sowohl für JSON- als auch für MessagePack-Clients.</span><span class="sxs-lookup"><span data-stu-id="c6811-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="c6811-115">Um anzupassen, wie MessagePack `AddMessagePackProtocol` Ihre Daten formatiert, benötigt ein Delegates zum Konfigurieren von Optionen.</span><span class="sxs-lookup"><span data-stu-id="c6811-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="c6811-116">In diesem Delegaten kann die `SerializerOptions` Eigenschaft zum Konfigurieren von MessagePack-Serialisierungsoptionen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="c6811-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="c6811-117">Weitere Informationen zur Funktionsweise der Resolver finden Sie in der MessagePack-Bibliothek unter [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="c6811-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="c6811-118">Attribute können für die Objekte verwendet werden, die Sie serialisieren möchten, um zu definieren, wie sie behandelt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="c6811-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="c6811-119">Es wird dringend empfohlen, [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) zu überprüfen und die empfohlenen Patches anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="c6811-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="c6811-120">Rufen Sie `.WithSecurity(MessagePackSecurity.UntrustedData)` z. `SerializerOptions`B. beim Ersetzen der auf.</span><span class="sxs-lookup"><span data-stu-id="c6811-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="c6811-121">Konfigurieren von MessagePack auf dem Client</span><span class="sxs-lookup"><span data-stu-id="c6811-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="c6811-122">JSON ist standardmäßig für die unterstützten Clients aktiviert.</span><span class="sxs-lookup"><span data-stu-id="c6811-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="c6811-123">Clients können nur ein einzelnes Protokoll unterstützen.</span><span class="sxs-lookup"><span data-stu-id="c6811-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="c6811-124">Durch das Hinzufügen der MessagePack-Unterstützung werden alle zuvor konfigurierten Protokolle ersetzt.</span><span class="sxs-lookup"><span data-stu-id="c6811-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="c6811-125">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="c6811-125">.NET client</span></span>

<span data-ttu-id="c6811-126">Um MessagePack im .NET Client `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` zu aktivieren, `HubConnectionBuilder`installieren Sie das Paket, und rufen Sie es auf. `AddMessagePackProtocol`</span><span class="sxs-lookup"><span data-stu-id="c6811-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="c6811-127">Dieser `AddMessagePackProtocol` Aufruf erfordert einen Delegaten zum Konfigurieren von Optionen genau wie der Server.</span><span class="sxs-lookup"><span data-stu-id="c6811-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="c6811-128">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="c6811-128">JavaScript client</span></span>

<span data-ttu-id="c6811-129">Die MessagePack-Unterstützung für den JavaScript-Client wird vom [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm-Paket bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="c6811-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="c6811-130">Installieren Sie das Paket, indem Sie den folgenden Befehl in einer Befehlsshell ausführen:</span><span class="sxs-lookup"><span data-stu-id="c6811-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="c6811-131">Nach der Installation des npm-Pakets kann das Modul direkt über einen JavaScript-Modullader verwendet oder in den Browser importiert werden, indem auf die folgende Datei verwiesen wird:</span><span class="sxs-lookup"><span data-stu-id="c6811-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="c6811-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="c6811-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="c6811-133">In einem Browser `msgpack5` muss auch auf die Bibliothek verwiesen werden.</span><span class="sxs-lookup"><span data-stu-id="c6811-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="c6811-134">Verwenden `<script>` Sie ein Tag, um einen Verweis zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="c6811-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="c6811-135">Die Bibliothek finden Sie unter *node_modules.msgpack5.dist-msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="c6811-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="c6811-136">Bei der `<script>` Verwendung des Elements ist die Reihenfolge wichtig.</span><span class="sxs-lookup"><span data-stu-id="c6811-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="c6811-137">Wenn *auf signalr-protocol-msgpack.js* vor *msgpack5.js*verwiesen wird, tritt beim Versuch, eine Verbindung mit MessagePack herzustellen, einen Fehler auf.</span><span class="sxs-lookup"><span data-stu-id="c6811-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="c6811-138">*signalr.js* ist auch vor *signalr-protocol-msgpack.js*erforderlich.</span><span class="sxs-lookup"><span data-stu-id="c6811-138">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="c6811-139">Durch `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` Hinzufügen `HubConnectionBuilder` zu dem wird der Client so konfiguriert, dass er das MessagePack-Protokoll verwendet, wenn eine Verbindung mit einem Server hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="c6811-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="c6811-140">Derzeit gibt es keine Konfigurationsoptionen für das MessagePack-Protokoll auf dem JavaScript-Client.</span><span class="sxs-lookup"><span data-stu-id="c6811-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="c6811-141">MessagePack Eigenheiten</span><span class="sxs-lookup"><span data-stu-id="c6811-141">MessagePack quirks</span></span>

<span data-ttu-id="c6811-142">Bei der Verwendung des MessagePack Hub-Protokolls sind einige Probleme zu beachten.</span><span class="sxs-lookup"><span data-stu-id="c6811-142">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="c6811-143">MessagePack berücksichtigt die Groß-/Kleinschreibung</span><span class="sxs-lookup"><span data-stu-id="c6811-143">MessagePack is case-sensitive</span></span>

<span data-ttu-id="c6811-144">Beim MessagePack-Protokoll wird die Groß-/Kleinschreibung beachtet.</span><span class="sxs-lookup"><span data-stu-id="c6811-144">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="c6811-145">Betrachten Sie z. B. die folgende C-Klasse:</span><span class="sxs-lookup"><span data-stu-id="c6811-145">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="c6811-146">Beim Senden vom JavaScript-Client `PascalCased` müssen Sie Eigenschaftsnamen verwenden, da das Gehäuse genau mit der C-Klasse übereinstimmen muss.</span><span class="sxs-lookup"><span data-stu-id="c6811-146">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="c6811-147">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="c6811-147">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="c6811-148">Die `camelCased` Verwendung von Namen wird nicht ordnungsgemäß an die C-Klasse gebunden.</span><span class="sxs-lookup"><span data-stu-id="c6811-148">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="c6811-149">Sie können dies umgehen, indem Sie das `Key` Attribut verwenden, um einen anderen Namen für die MessagePack-Eigenschaft anzugeben.</span><span class="sxs-lookup"><span data-stu-id="c6811-149">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="c6811-150">Weitere Informationen finden Sie [in der MessagePack-CSharp-Dokumentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="c6811-150">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="c6811-151">DateTime.Kind bleibt beim Serialisieren/Deserialisieren nicht erhalten</span><span class="sxs-lookup"><span data-stu-id="c6811-151">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="c6811-152">Das MessagePack-Protokoll bietet keine Möglichkeit, `Kind` den Wert `DateTime`einer zu codieren.</span><span class="sxs-lookup"><span data-stu-id="c6811-152">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="c6811-153">Daher wird das MessagePack Hub-Protokoll beim Deserialisieren eines Datums `DateTime.Kind` in `DateTimeKind.Local` das UTC-Format konvertiert, wenn es andernfalls die Zeit nicht berührt und wie besehen wird.</span><span class="sxs-lookup"><span data-stu-id="c6811-153">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="c6811-154">Wenn Sie mit `DateTime` Werten arbeiten, empfehlen wir, vor dem Senden in UTC zu konvertieren.</span><span class="sxs-lookup"><span data-stu-id="c6811-154">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="c6811-155">Konvertieren Sie sie von UTC in Ortszeit, wenn Sie sie erhalten.</span><span class="sxs-lookup"><span data-stu-id="c6811-155">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="c6811-156">DateTime.MinValue wird von MessagePack in JavaScript nicht unterstützt</span><span class="sxs-lookup"><span data-stu-id="c6811-156">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="c6811-157">Die [msgpack5-Bibliothek,](https://github.com/mcollina/msgpack5) die SignalR vom JavaScript-Client verwendet wird, unterstützt den `timestamp96` Typ in MessagePack nicht.</span><span class="sxs-lookup"><span data-stu-id="c6811-157">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="c6811-158">Dieser Typ wird verwendet, um sehr große Datumswerte zu kodieren (entweder sehr früh in der Vergangenheit oder sehr weit in der Zukunft).</span><span class="sxs-lookup"><span data-stu-id="c6811-158">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="c6811-159">Der Wert `DateTime.MinValue` `January 1, 0001`von ist , der in `timestamp96` einem Wert codiert werden muss.</span><span class="sxs-lookup"><span data-stu-id="c6811-159">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="c6811-160">Aus diesem Grund `DateTime.MinValue` wird das Senden an einen JavaScript-Client nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c6811-160">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="c6811-161">Wenn `DateTime.MinValue` der JavaScript-Client empfangen wird, wird der folgende Fehler ausgelöst:</span><span class="sxs-lookup"><span data-stu-id="c6811-161">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="c6811-162">In `DateTime.MinValue` der Regel wird verwendet, um `null` einen "fehlenden" oder Wert zu kodieren.</span><span class="sxs-lookup"><span data-stu-id="c6811-162">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="c6811-163">Wenn Sie diesen Wert in MessagePack codieren `DateTime` müssen,`DateTime?`verwenden Sie einen `bool` nullablen Wert ( ) oder codieren Sie einen separaten Wert, der angibt, ob das Datum vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="c6811-163">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="c6811-164">Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub-Problem [aspnet/#2228SignalR](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="c6811-164">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="c6811-165">MessagePack-Unterstützung in der "Ahead-of-Time"-Kompilierungsumgebung</span><span class="sxs-lookup"><span data-stu-id="c6811-165">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="c6811-166">Die [MessagePack-CSharp-Bibliothek,](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) die vom .NET-Client und -Server verwendet wird, verwendet die Codegenerierung, um die Serialisierung zu optimieren.</span><span class="sxs-lookup"><span data-stu-id="c6811-166">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="c6811-167">Daher wird es standardmäßig nicht für Umgebungen unterstützt, die die Kompilierung "vor der Zeit" verwenden (z. B. Xamarin iOS oder Unity).</span><span class="sxs-lookup"><span data-stu-id="c6811-167">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="c6811-168">Es ist möglich, MessagePack in diesen Umgebungen zu verwenden, indem Sie den Serialisierungs-/Deserialisierungscode "vorgenerieren".</span><span class="sxs-lookup"><span data-stu-id="c6811-168">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="c6811-169">Weitere Informationen finden Sie [in der MessagePack-CSharp-Dokumentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span><span class="sxs-lookup"><span data-stu-id="c6811-169">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="c6811-170">Nachdem Sie die Serialisierungen vorgeneriert haben, können Sie sie `AddMessagePackProtocol`mit dem Konfigurationsdelegat registrieren, der an:</span><span class="sxs-lookup"><span data-stu-id="c6811-170">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="c6811-171">Typprüfungen sind in MessagePack strenger</span><span class="sxs-lookup"><span data-stu-id="c6811-171">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="c6811-172">Das JSON Hub-Protokoll führt während der Deserialisierung Typkonvertierungen durch.</span><span class="sxs-lookup"><span data-stu-id="c6811-172">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="c6811-173">Wenn das eingehende Objekt beispielsweise über einen Eigenschaftswert`{ foo: 42 }`verfügt, der eine Zahl ( `string`) ist, die Eigenschaft der .NET-Klasse jedoch vom Typ ist, wird der Wert konvertiert.</span><span class="sxs-lookup"><span data-stu-id="c6811-173">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="c6811-174">MessagePack führt diese Konvertierung jedoch nicht durch und löst eine Ausnahme aus, die in serverseitigen Protokollen (und in der Konsole) angezeigt werden kann:</span><span class="sxs-lookup"><span data-stu-id="c6811-174">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="c6811-175">Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub-Problem [aspnet/#2937SignalR](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="c6811-175">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="c6811-176">Verwandte Ressourcen</span><span class="sxs-lookup"><span data-stu-id="c6811-176">Related resources</span></span>

* [<span data-ttu-id="c6811-177">Erste Schritte</span><span class="sxs-lookup"><span data-stu-id="c6811-177">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="c6811-178">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="c6811-178">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="c6811-179">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="c6811-179">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="c6811-180">In diesem Artikel wird davon ausgegangen, dass der Leser mit den unter [Erste Schritte](xref:tutorials/signalr)behandelten Themen vertraut ist.</span><span class="sxs-lookup"><span data-stu-id="c6811-180">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="c6811-181">Was ist MessagePack?</span><span class="sxs-lookup"><span data-stu-id="c6811-181">What is MessagePack?</span></span>

<span data-ttu-id="c6811-182">[MessagePack](https://msgpack.org/index.html) ist ein schnelles und kompaktes binäres Serialisierungsformat.</span><span class="sxs-lookup"><span data-stu-id="c6811-182">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="c6811-183">Es ist nützlich, wenn Leistung und Bandbreite ein Problem darstellen, da es kleinere Nachrichten im Vergleich zu [JSON](https://www.json.org/)erzeugt.</span><span class="sxs-lookup"><span data-stu-id="c6811-183">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="c6811-184">Die binären Nachrichten sind beim Betrachten von Netzwerkablaufverfolgungen und -protokollen nicht lesbar, es sei denn, die Bytes werden über einen MessagePack-Parser übergeben.</span><span class="sxs-lookup"><span data-stu-id="c6811-184">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="c6811-185">bietet integrierte Unterstützung für das MessagePack-Format und stellt APIs für den Client und Server bereit.</span><span class="sxs-lookup"><span data-stu-id="c6811-185"> has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="c6811-186">Konfigurieren von MessagePack auf dem Server</span><span class="sxs-lookup"><span data-stu-id="c6811-186">Configure MessagePack on the server</span></span>

<span data-ttu-id="c6811-187">Um das MessagePack Hub-Protokoll auf `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` dem Server zu aktivieren, installieren Sie das Paket in Ihrer App.</span><span class="sxs-lookup"><span data-stu-id="c6811-187">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="c6811-188">Fügen `Startup.ConfigureServices` Sie `AddMessagePackProtocol` in der `AddSignalR` Methode dem Aufruf hinzu, um die MessagePack-Unterstützung auf dem Server zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="c6811-188">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="c6811-189">JSON ist standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="c6811-189">JSON is enabled by default.</span></span> <span data-ttu-id="c6811-190">Das Hinzufügen von MessagePack ermöglicht die Unterstützung sowohl für JSON- als auch für MessagePack-Clients.</span><span class="sxs-lookup"><span data-stu-id="c6811-190">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="c6811-191">Um anzupassen, wie MessagePack `AddMessagePackProtocol` Ihre Daten formatiert, benötigt ein Delegates zum Konfigurieren von Optionen.</span><span class="sxs-lookup"><span data-stu-id="c6811-191">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="c6811-192">In diesem Delegaten kann die `FormatterResolvers` Eigenschaft zum Konfigurieren von MessagePack-Serialisierungsoptionen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="c6811-192">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="c6811-193">Weitere Informationen zur Funktionsweise der Resolver finden Sie in der MessagePack-Bibliothek unter [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="c6811-193">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="c6811-194">Attribute können für die Objekte verwendet werden, die Sie serialisieren möchten, um zu definieren, wie sie behandelt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="c6811-194">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="c6811-195">Es wird dringend empfohlen, [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) zu überprüfen und die empfohlenen Patches anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="c6811-195">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="c6811-196">Legen Sie z. B. die `MessagePackSecurity.Active` statische Eigenschaft auf `MessagePackSecurity.UntrustedData`fest.</span><span class="sxs-lookup"><span data-stu-id="c6811-196">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="c6811-197">Das `MessagePackSecurity.Active` Festlegen der erfordert die manuelle Installation einer [1.9.x-Version von MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="c6811-197">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="c6811-198">Die `MessagePack` Installation von 1.9.x aktualisiert die Version. SignalR</span><span class="sxs-lookup"><span data-stu-id="c6811-198">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="c6811-199">Wenn `MessagePackSecurity.Active` nicht auf `MessagePackSecurity.UntrustedData`festgelegt ist, kann ein böswilliger Client einen Denial-of-Service verursachen.</span><span class="sxs-lookup"><span data-stu-id="c6811-199">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="c6811-200">Setzen `MessagePackSecurity.Active` `Program.Main`Sie in ein, wie im folgenden Code gezeigt:</span><span class="sxs-lookup"><span data-stu-id="c6811-200">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="c6811-201">Konfigurieren von MessagePack auf dem Client</span><span class="sxs-lookup"><span data-stu-id="c6811-201">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="c6811-202">JSON ist standardmäßig für die unterstützten Clients aktiviert.</span><span class="sxs-lookup"><span data-stu-id="c6811-202">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="c6811-203">Clients können nur ein einzelnes Protokoll unterstützen.</span><span class="sxs-lookup"><span data-stu-id="c6811-203">Clients can only support a single protocol.</span></span> <span data-ttu-id="c6811-204">Durch das Hinzufügen der MessagePack-Unterstützung werden alle zuvor konfigurierten Protokolle ersetzt.</span><span class="sxs-lookup"><span data-stu-id="c6811-204">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="c6811-205">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="c6811-205">.NET client</span></span>

<span data-ttu-id="c6811-206">Um MessagePack im .NET Client `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` zu aktivieren, `HubConnectionBuilder`installieren Sie das Paket, und rufen Sie es auf. `AddMessagePackProtocol`</span><span class="sxs-lookup"><span data-stu-id="c6811-206">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="c6811-207">Dieser `AddMessagePackProtocol` Aufruf erfordert einen Delegaten zum Konfigurieren von Optionen genau wie der Server.</span><span class="sxs-lookup"><span data-stu-id="c6811-207">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="c6811-208">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="c6811-208">JavaScript client</span></span>

<span data-ttu-id="c6811-209">Die MessagePack-Unterstützung für den JavaScript-Client wird vom [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm-Paket bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="c6811-209">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="c6811-210">Installieren Sie das Paket, indem Sie den folgenden Befehl in einer Befehlsshell ausführen:</span><span class="sxs-lookup"><span data-stu-id="c6811-210">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="c6811-211">Nach der Installation des npm-Pakets kann das Modul direkt über einen JavaScript-Modullader verwendet oder in den Browser importiert werden, indem auf die folgende Datei verwiesen wird:</span><span class="sxs-lookup"><span data-stu-id="c6811-211">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="c6811-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="c6811-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="c6811-213">In einem Browser `msgpack5` muss auch auf die Bibliothek verwiesen werden.</span><span class="sxs-lookup"><span data-stu-id="c6811-213">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="c6811-214">Verwenden `<script>` Sie ein Tag, um einen Verweis zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="c6811-214">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="c6811-215">Die Bibliothek finden Sie unter *node_modules.msgpack5.dist-msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="c6811-215">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="c6811-216">Bei der `<script>` Verwendung des Elements ist die Reihenfolge wichtig.</span><span class="sxs-lookup"><span data-stu-id="c6811-216">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="c6811-217">Wenn *auf signalr-protocol-msgpack.js* vor *msgpack5.js*verwiesen wird, tritt beim Versuch, eine Verbindung mit MessagePack herzustellen, einen Fehler auf.</span><span class="sxs-lookup"><span data-stu-id="c6811-217">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="c6811-218">*signalr.js* ist auch vor *signalr-protocol-msgpack.js*erforderlich.</span><span class="sxs-lookup"><span data-stu-id="c6811-218">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="c6811-219">Durch `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` Hinzufügen `HubConnectionBuilder` zu dem wird der Client so konfiguriert, dass er das MessagePack-Protokoll verwendet, wenn eine Verbindung mit einem Server hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="c6811-219">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="c6811-220">Derzeit gibt es keine Konfigurationsoptionen für das MessagePack-Protokoll auf dem JavaScript-Client.</span><span class="sxs-lookup"><span data-stu-id="c6811-220">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="c6811-221">MessagePack Eigenheiten</span><span class="sxs-lookup"><span data-stu-id="c6811-221">MessagePack quirks</span></span>

<span data-ttu-id="c6811-222">Bei der Verwendung des MessagePack Hub-Protokolls sind einige Probleme zu beachten.</span><span class="sxs-lookup"><span data-stu-id="c6811-222">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="c6811-223">MessagePack berücksichtigt die Groß-/Kleinschreibung</span><span class="sxs-lookup"><span data-stu-id="c6811-223">MessagePack is case-sensitive</span></span>

<span data-ttu-id="c6811-224">Beim MessagePack-Protokoll wird die Groß-/Kleinschreibung beachtet.</span><span class="sxs-lookup"><span data-stu-id="c6811-224">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="c6811-225">Betrachten Sie z. B. die folgende C-Klasse:</span><span class="sxs-lookup"><span data-stu-id="c6811-225">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="c6811-226">Beim Senden vom JavaScript-Client `PascalCased` müssen Sie Eigenschaftsnamen verwenden, da das Gehäuse genau mit der C-Klasse übereinstimmen muss.</span><span class="sxs-lookup"><span data-stu-id="c6811-226">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="c6811-227">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="c6811-227">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="c6811-228">Die `camelCased` Verwendung von Namen wird nicht ordnungsgemäß an die C-Klasse gebunden.</span><span class="sxs-lookup"><span data-stu-id="c6811-228">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="c6811-229">Sie können dies umgehen, indem Sie das `Key` Attribut verwenden, um einen anderen Namen für die MessagePack-Eigenschaft anzugeben.</span><span class="sxs-lookup"><span data-stu-id="c6811-229">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="c6811-230">Weitere Informationen finden Sie [in der MessagePack-CSharp-Dokumentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="c6811-230">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="c6811-231">DateTime.Kind bleibt beim Serialisieren/Deserialisieren nicht erhalten</span><span class="sxs-lookup"><span data-stu-id="c6811-231">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="c6811-232">Das MessagePack-Protokoll bietet keine Möglichkeit, `Kind` den Wert `DateTime`einer zu codieren.</span><span class="sxs-lookup"><span data-stu-id="c6811-232">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="c6811-233">Daher geht das MessagePack Hub-Protokoll bei der Deserialisierung eines Datums davon aus, dass das eingehende Datum im UTC-Format liegt.</span><span class="sxs-lookup"><span data-stu-id="c6811-233">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="c6811-234">Wenn Sie mit `DateTime` Werten in der Ortszeit arbeiten, empfehlen wir, vor dem Senden in UTC zu konvertieren.</span><span class="sxs-lookup"><span data-stu-id="c6811-234">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="c6811-235">Konvertieren Sie sie von UTC in Ortszeit, wenn Sie sie erhalten.</span><span class="sxs-lookup"><span data-stu-id="c6811-235">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="c6811-236">Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub-Problem [aspnet/#2632SignalR](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="c6811-236">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="c6811-237">DateTime.MinValue wird von MessagePack in JavaScript nicht unterstützt</span><span class="sxs-lookup"><span data-stu-id="c6811-237">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="c6811-238">Die [msgpack5-Bibliothek,](https://github.com/mcollina/msgpack5) die SignalR vom JavaScript-Client verwendet wird, unterstützt den `timestamp96` Typ in MessagePack nicht.</span><span class="sxs-lookup"><span data-stu-id="c6811-238">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="c6811-239">Dieser Typ wird verwendet, um sehr große Datumswerte zu kodieren (entweder sehr früh in der Vergangenheit oder sehr weit in der Zukunft).</span><span class="sxs-lookup"><span data-stu-id="c6811-239">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="c6811-240">Der Wert `DateTime.MinValue` `January 1, 0001`von ist , der in `timestamp96` einem Wert codiert werden muss.</span><span class="sxs-lookup"><span data-stu-id="c6811-240">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="c6811-241">Aus diesem Grund `DateTime.MinValue` wird das Senden an einen JavaScript-Client nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c6811-241">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="c6811-242">Wenn `DateTime.MinValue` der JavaScript-Client empfangen wird, wird der folgende Fehler ausgelöst:</span><span class="sxs-lookup"><span data-stu-id="c6811-242">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="c6811-243">In `DateTime.MinValue` der Regel wird verwendet, um `null` einen "fehlenden" oder Wert zu kodieren.</span><span class="sxs-lookup"><span data-stu-id="c6811-243">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="c6811-244">Wenn Sie diesen Wert in MessagePack codieren `DateTime` müssen,`DateTime?`verwenden Sie einen `bool` nullablen Wert ( ) oder codieren Sie einen separaten Wert, der angibt, ob das Datum vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="c6811-244">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="c6811-245">Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub-Problem [aspnet/#2228SignalR](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="c6811-245">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="c6811-246">MessagePack-Unterstützung in der "Ahead-of-Time"-Kompilierungsumgebung</span><span class="sxs-lookup"><span data-stu-id="c6811-246">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="c6811-247">Die [MessagePack-CSharp-Bibliothek,](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) die vom .NET-Client und -Server verwendet wird, verwendet die Codegenerierung, um die Serialisierung zu optimieren.</span><span class="sxs-lookup"><span data-stu-id="c6811-247">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="c6811-248">Daher wird es standardmäßig nicht für Umgebungen unterstützt, die die Kompilierung "vor der Zeit" verwenden (z. B. Xamarin iOS oder Unity).</span><span class="sxs-lookup"><span data-stu-id="c6811-248">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="c6811-249">Es ist möglich, MessagePack in diesen Umgebungen zu verwenden, indem Sie den Serialisierungs-/Deserialisierungscode "vorgenerieren".</span><span class="sxs-lookup"><span data-stu-id="c6811-249">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="c6811-250">Weitere Informationen finden Sie [in der MessagePack-CSharp-Dokumentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="c6811-250">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="c6811-251">Nachdem Sie die Serialisierungen vorgeneriert haben, können Sie sie `AddMessagePackProtocol`mit dem Konfigurationsdelegat registrieren, der an:</span><span class="sxs-lookup"><span data-stu-id="c6811-251">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="c6811-252">Typprüfungen sind in MessagePack strenger</span><span class="sxs-lookup"><span data-stu-id="c6811-252">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="c6811-253">Das JSON Hub-Protokoll führt während der Deserialisierung Typkonvertierungen durch.</span><span class="sxs-lookup"><span data-stu-id="c6811-253">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="c6811-254">Wenn das eingehende Objekt beispielsweise über einen Eigenschaftswert`{ foo: 42 }`verfügt, der eine Zahl ( `string`) ist, die Eigenschaft der .NET-Klasse jedoch vom Typ ist, wird der Wert konvertiert.</span><span class="sxs-lookup"><span data-stu-id="c6811-254">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="c6811-255">MessagePack führt diese Konvertierung jedoch nicht durch und löst eine Ausnahme aus, die in serverseitigen Protokollen (und in der Konsole) angezeigt werden kann:</span><span class="sxs-lookup"><span data-stu-id="c6811-255">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="c6811-256">Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub-Problem [aspnet/#2937SignalR](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="c6811-256">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="c6811-257">Verwandte Ressourcen</span><span class="sxs-lookup"><span data-stu-id="c6811-257">Related resources</span></span>

* [<span data-ttu-id="c6811-258">Erste Schritte</span><span class="sxs-lookup"><span data-stu-id="c6811-258">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="c6811-259">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="c6811-259">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="c6811-260">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="c6811-260">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c6811-261">In diesem Artikel wird davon ausgegangen, dass der Leser mit den unter [Erste Schritte](xref:tutorials/signalr)behandelten Themen vertraut ist.</span><span class="sxs-lookup"><span data-stu-id="c6811-261">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="c6811-262">Was ist MessagePack?</span><span class="sxs-lookup"><span data-stu-id="c6811-262">What is MessagePack?</span></span>

<span data-ttu-id="c6811-263">[MessagePack](https://msgpack.org/index.html) ist ein schnelles und kompaktes binäres Serialisierungsformat.</span><span class="sxs-lookup"><span data-stu-id="c6811-263">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="c6811-264">Es ist nützlich, wenn Leistung und Bandbreite ein Problem darstellen, da es kleinere Nachrichten im Vergleich zu [JSON](https://www.json.org/)erzeugt.</span><span class="sxs-lookup"><span data-stu-id="c6811-264">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="c6811-265">Die binären Nachrichten sind beim Betrachten von Netzwerkablaufverfolgungen und -protokollen nicht lesbar, es sei denn, die Bytes werden über einen MessagePack-Parser übergeben.</span><span class="sxs-lookup"><span data-stu-id="c6811-265">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="c6811-266">bietet integrierte Unterstützung für das MessagePack-Format und stellt APIs für den Client und Server bereit.</span><span class="sxs-lookup"><span data-stu-id="c6811-266"> has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="c6811-267">Konfigurieren von MessagePack auf dem Server</span><span class="sxs-lookup"><span data-stu-id="c6811-267">Configure MessagePack on the server</span></span>

<span data-ttu-id="c6811-268">Um das MessagePack Hub-Protokoll auf `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` dem Server zu aktivieren, installieren Sie das Paket in Ihrer App.</span><span class="sxs-lookup"><span data-stu-id="c6811-268">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="c6811-269">Fügen `Startup.ConfigureServices` Sie `AddMessagePackProtocol` in der `AddSignalR` Methode dem Aufruf hinzu, um die MessagePack-Unterstützung auf dem Server zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="c6811-269">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="c6811-270">JSON ist standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="c6811-270">JSON is enabled by default.</span></span> <span data-ttu-id="c6811-271">Das Hinzufügen von MessagePack ermöglicht die Unterstützung sowohl für JSON- als auch für MessagePack-Clients.</span><span class="sxs-lookup"><span data-stu-id="c6811-271">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="c6811-272">Um anzupassen, wie MessagePack `AddMessagePackProtocol` Ihre Daten formatiert, benötigt ein Delegates zum Konfigurieren von Optionen.</span><span class="sxs-lookup"><span data-stu-id="c6811-272">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="c6811-273">In diesem Delegaten kann die `FormatterResolvers` Eigenschaft zum Konfigurieren von MessagePack-Serialisierungsoptionen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="c6811-273">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="c6811-274">Weitere Informationen zur Funktionsweise der Resolver finden Sie in der MessagePack-Bibliothek unter [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="c6811-274">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="c6811-275">Attribute können für die Objekte verwendet werden, die Sie serialisieren möchten, um zu definieren, wie sie behandelt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="c6811-275">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="c6811-276">Es wird dringend empfohlen, [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) zu überprüfen und die empfohlenen Patches anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="c6811-276">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="c6811-277">Legen Sie z. B. die `MessagePackSecurity.Active` statische Eigenschaft auf `MessagePackSecurity.UntrustedData`fest.</span><span class="sxs-lookup"><span data-stu-id="c6811-277">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="c6811-278">Das `MessagePackSecurity.Active` Festlegen der erfordert die manuelle Installation einer [1.9.x-Version von MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="c6811-278">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="c6811-279">Die `MessagePack` Installation von 1.9.x aktualisiert die Version. SignalR</span><span class="sxs-lookup"><span data-stu-id="c6811-279">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="c6811-280">Wenn `MessagePackSecurity.Active` nicht auf `MessagePackSecurity.UntrustedData`festgelegt ist, kann ein böswilliger Client einen Denial-of-Service verursachen.</span><span class="sxs-lookup"><span data-stu-id="c6811-280">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="c6811-281">Setzen `MessagePackSecurity.Active` `Program.Main`Sie in ein, wie im folgenden Code gezeigt:</span><span class="sxs-lookup"><span data-stu-id="c6811-281">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="c6811-282">Konfigurieren von MessagePack auf dem Client</span><span class="sxs-lookup"><span data-stu-id="c6811-282">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="c6811-283">JSON ist standardmäßig für die unterstützten Clients aktiviert.</span><span class="sxs-lookup"><span data-stu-id="c6811-283">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="c6811-284">Clients können nur ein einzelnes Protokoll unterstützen.</span><span class="sxs-lookup"><span data-stu-id="c6811-284">Clients can only support a single protocol.</span></span> <span data-ttu-id="c6811-285">Durch das Hinzufügen der MessagePack-Unterstützung werden alle zuvor konfigurierten Protokolle ersetzt.</span><span class="sxs-lookup"><span data-stu-id="c6811-285">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="c6811-286">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="c6811-286">.NET client</span></span>

<span data-ttu-id="c6811-287">Um MessagePack im .NET Client `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` zu aktivieren, `HubConnectionBuilder`installieren Sie das Paket, und rufen Sie es auf. `AddMessagePackProtocol`</span><span class="sxs-lookup"><span data-stu-id="c6811-287">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="c6811-288">Dieser `AddMessagePackProtocol` Aufruf erfordert einen Delegaten zum Konfigurieren von Optionen genau wie der Server.</span><span class="sxs-lookup"><span data-stu-id="c6811-288">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="c6811-289">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="c6811-289">JavaScript client</span></span>

<span data-ttu-id="c6811-290">Die MessagePack-Unterstützung für den JavaScript-Client wird vom [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm-Paket bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="c6811-290">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="c6811-291">Installieren Sie das Paket, indem Sie den folgenden Befehl in einer Befehlsshell ausführen:</span><span class="sxs-lookup"><span data-stu-id="c6811-291">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="c6811-292">Nach der Installation des npm-Pakets kann das Modul direkt über einen JavaScript-Modullader verwendet oder in den Browser importiert werden, indem auf die folgende Datei verwiesen wird:</span><span class="sxs-lookup"><span data-stu-id="c6811-292">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="c6811-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="c6811-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="c6811-294">In einem Browser `msgpack5` muss auch auf die Bibliothek verwiesen werden.</span><span class="sxs-lookup"><span data-stu-id="c6811-294">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="c6811-295">Verwenden `<script>` Sie ein Tag, um einen Verweis zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="c6811-295">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="c6811-296">Die Bibliothek finden Sie unter *node_modules.msgpack5.dist-msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="c6811-296">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="c6811-297">Bei der `<script>` Verwendung des Elements ist die Reihenfolge wichtig.</span><span class="sxs-lookup"><span data-stu-id="c6811-297">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="c6811-298">Wenn *auf signalr-protocol-msgpack.js* vor *msgpack5.js*verwiesen wird, tritt beim Versuch, eine Verbindung mit MessagePack herzustellen, einen Fehler auf.</span><span class="sxs-lookup"><span data-stu-id="c6811-298">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="c6811-299">*signalr.js* ist auch vor *signalr-protocol-msgpack.js*erforderlich.</span><span class="sxs-lookup"><span data-stu-id="c6811-299">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="c6811-300">Durch `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` Hinzufügen `HubConnectionBuilder` zu dem wird der Client so konfiguriert, dass er das MessagePack-Protokoll verwendet, wenn eine Verbindung mit einem Server hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="c6811-300">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="c6811-301">Derzeit gibt es keine Konfigurationsoptionen für das MessagePack-Protokoll auf dem JavaScript-Client.</span><span class="sxs-lookup"><span data-stu-id="c6811-301">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="c6811-302">MessagePack Eigenheiten</span><span class="sxs-lookup"><span data-stu-id="c6811-302">MessagePack quirks</span></span>

<span data-ttu-id="c6811-303">Bei der Verwendung des MessagePack Hub-Protokolls sind einige Probleme zu beachten.</span><span class="sxs-lookup"><span data-stu-id="c6811-303">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="c6811-304">MessagePack berücksichtigt die Groß-/Kleinschreibung</span><span class="sxs-lookup"><span data-stu-id="c6811-304">MessagePack is case-sensitive</span></span>

<span data-ttu-id="c6811-305">Beim MessagePack-Protokoll wird die Groß-/Kleinschreibung beachtet.</span><span class="sxs-lookup"><span data-stu-id="c6811-305">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="c6811-306">Betrachten Sie z. B. die folgende C-Klasse:</span><span class="sxs-lookup"><span data-stu-id="c6811-306">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="c6811-307">Beim Senden vom JavaScript-Client `PascalCased` müssen Sie Eigenschaftsnamen verwenden, da das Gehäuse genau mit der C-Klasse übereinstimmen muss.</span><span class="sxs-lookup"><span data-stu-id="c6811-307">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="c6811-308">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="c6811-308">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="c6811-309">Die `camelCased` Verwendung von Namen wird nicht ordnungsgemäß an die C-Klasse gebunden.</span><span class="sxs-lookup"><span data-stu-id="c6811-309">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="c6811-310">Sie können dies umgehen, indem Sie das `Key` Attribut verwenden, um einen anderen Namen für die MessagePack-Eigenschaft anzugeben.</span><span class="sxs-lookup"><span data-stu-id="c6811-310">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="c6811-311">Weitere Informationen finden Sie [in der MessagePack-CSharp-Dokumentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="c6811-311">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="c6811-312">DateTime.Kind bleibt beim Serialisieren/Deserialisieren nicht erhalten</span><span class="sxs-lookup"><span data-stu-id="c6811-312">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="c6811-313">Das MessagePack-Protokoll bietet keine Möglichkeit, `Kind` den Wert `DateTime`einer zu codieren.</span><span class="sxs-lookup"><span data-stu-id="c6811-313">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="c6811-314">Daher geht das MessagePack Hub-Protokoll bei der Deserialisierung eines Datums davon aus, dass das eingehende Datum im UTC-Format liegt.</span><span class="sxs-lookup"><span data-stu-id="c6811-314">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="c6811-315">Wenn Sie mit `DateTime` Werten in der Ortszeit arbeiten, empfehlen wir, vor dem Senden in UTC zu konvertieren.</span><span class="sxs-lookup"><span data-stu-id="c6811-315">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="c6811-316">Konvertieren Sie sie von UTC in Ortszeit, wenn Sie sie erhalten.</span><span class="sxs-lookup"><span data-stu-id="c6811-316">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="c6811-317">Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub-Problem [aspnet/#2632SignalR](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="c6811-317">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="c6811-318">DateTime.MinValue wird von MessagePack in JavaScript nicht unterstützt</span><span class="sxs-lookup"><span data-stu-id="c6811-318">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="c6811-319">Die [msgpack5-Bibliothek,](https://github.com/mcollina/msgpack5) die SignalR vom JavaScript-Client verwendet wird, unterstützt den `timestamp96` Typ in MessagePack nicht.</span><span class="sxs-lookup"><span data-stu-id="c6811-319">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="c6811-320">Dieser Typ wird verwendet, um sehr große Datumswerte zu kodieren (entweder sehr früh in der Vergangenheit oder sehr weit in der Zukunft).</span><span class="sxs-lookup"><span data-stu-id="c6811-320">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="c6811-321">Der Wert `DateTime.MinValue` `January 1, 0001` von ist, der in `timestamp96` einem Wert codiert werden muss.</span><span class="sxs-lookup"><span data-stu-id="c6811-321">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="c6811-322">Aus diesem Grund `DateTime.MinValue` wird das Senden an einen JavaScript-Client nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c6811-322">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="c6811-323">Wenn `DateTime.MinValue` der JavaScript-Client empfangen wird, wird der folgende Fehler ausgelöst:</span><span class="sxs-lookup"><span data-stu-id="c6811-323">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="c6811-324">In `DateTime.MinValue` der Regel wird verwendet, um `null` einen "fehlenden" oder Wert zu kodieren.</span><span class="sxs-lookup"><span data-stu-id="c6811-324">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="c6811-325">Wenn Sie diesen Wert in MessagePack codieren `DateTime` müssen,`DateTime?`verwenden Sie einen `bool` nullablen Wert ( ) oder codieren Sie einen separaten Wert, der angibt, ob das Datum vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="c6811-325">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="c6811-326">Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub-Problem [aspnet/#2228SignalR](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="c6811-326">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="c6811-327">MessagePack-Unterstützung in der "Ahead-of-Time"-Kompilierungsumgebung</span><span class="sxs-lookup"><span data-stu-id="c6811-327">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="c6811-328">Die [MessagePack-CSharp-Bibliothek,](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) die vom .NET-Client und -Server verwendet wird, verwendet die Codegenerierung, um die Serialisierung zu optimieren.</span><span class="sxs-lookup"><span data-stu-id="c6811-328">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="c6811-329">Daher wird es standardmäßig nicht für Umgebungen unterstützt, die die Kompilierung "vor der Zeit" verwenden (z. B. Xamarin iOS oder Unity).</span><span class="sxs-lookup"><span data-stu-id="c6811-329">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="c6811-330">Es ist möglich, MessagePack in diesen Umgebungen zu verwenden, indem Sie den Serialisierungs-/Deserialisierungscode "vorgenerieren".</span><span class="sxs-lookup"><span data-stu-id="c6811-330">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="c6811-331">Weitere Informationen finden Sie [in der MessagePack-CSharp-Dokumentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="c6811-331">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="c6811-332">Nachdem Sie die Serialisierungen vorgeneriert haben, können Sie sie `AddMessagePackProtocol`mit dem Konfigurationsdelegat registrieren, der an:</span><span class="sxs-lookup"><span data-stu-id="c6811-332">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="c6811-333">Typprüfungen sind in MessagePack strenger</span><span class="sxs-lookup"><span data-stu-id="c6811-333">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="c6811-334">Das JSON Hub-Protokoll führt während der Deserialisierung Typkonvertierungen durch.</span><span class="sxs-lookup"><span data-stu-id="c6811-334">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="c6811-335">Wenn das eingehende Objekt beispielsweise über einen Eigenschaftswert`{ foo: 42 }`verfügt, der eine Zahl ( `string`) ist, die Eigenschaft der .NET-Klasse jedoch vom Typ ist, wird der Wert konvertiert.</span><span class="sxs-lookup"><span data-stu-id="c6811-335">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="c6811-336">MessagePack führt diese Konvertierung jedoch nicht durch und löst eine Ausnahme aus, die in serverseitigen Protokollen (und in der Konsole) angezeigt werden kann:</span><span class="sxs-lookup"><span data-stu-id="c6811-336">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="c6811-337">Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub-Problem [aspnet/#2937SignalR](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="c6811-337">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="c6811-338">Verwandte Ressourcen</span><span class="sxs-lookup"><span data-stu-id="c6811-338">Related resources</span></span>

* [<span data-ttu-id="c6811-339">Erste Schritte</span><span class="sxs-lookup"><span data-stu-id="c6811-339">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="c6811-340">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="c6811-340">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="c6811-341">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="c6811-341">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

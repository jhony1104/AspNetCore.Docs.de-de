---
title: Verwenden des messagepack-Hub-Protokolls in signalr für ASP.net Core
author: bradygaster
description: Fügen Sie ASP.net Core signalr das messagepack-Hub-Protokoll hinzu.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 10/08/2019
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: fe09b646eba5ae15cbd9e568b276aaf7763e4b1b
ms.sourcegitcommit: fcdf9aaa6c45c1a926bd870ed8f893bdb4935152
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72165404"
---
# <a name="use-messagepack-hub-protocol-in-signalr-for-aspnet-core"></a><span data-ttu-id="fbccd-103">Verwenden des messagepack-Hub-Protokolls in signalr für ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="fbccd-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

<span data-ttu-id="fbccd-104">Von [Brennan](https://github.com/BrennanConroy) "a"</span><span class="sxs-lookup"><span data-stu-id="fbccd-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

<span data-ttu-id="fbccd-105">In diesem Artikel wird davon ausgegangen, dass der Reader mit den Themen in " [Get Started](xref:tutorials/signalr)" vertraut ist.</span><span class="sxs-lookup"><span data-stu-id="fbccd-105">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="fbccd-106">Was ist messagepack?</span><span class="sxs-lookup"><span data-stu-id="fbccd-106">What is MessagePack?</span></span>

<span data-ttu-id="fbccd-107">[Messagepack](https://msgpack.org/index.html) ist ein schnelles und kompaktes binäres Serialisierungsformat.</span><span class="sxs-lookup"><span data-stu-id="fbccd-107">[MessagePack](https://msgpack.org/index.html) is a binary serialization format that is fast and compact.</span></span> <span data-ttu-id="fbccd-108">Dies ist hilfreich, wenn Leistung und Bandbreite wichtig sind, da im Vergleich zu [JSON](https://www.json.org/)kleinere Nachrichten erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="fbccd-108">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="fbccd-109">Da es sich um ein binäres Format handelt, sind Nachrichten bei der Betrachtung von Netzwerk Ablauf Verfolgungen und Protokollen nicht lesbar, es sei denn, die Bytes werden über einen messagepack-Parser übergeben</span><span class="sxs-lookup"><span data-stu-id="fbccd-109">Because it's a binary format, messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="fbccd-110">Signalr verfügt über eine integrierte Unterstützung für das messagepack-Format und stellt APIs für den Client und den Server zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="fbccd-110">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="fbccd-111">Konfigurieren von messagepack auf dem Server</span><span class="sxs-lookup"><span data-stu-id="fbccd-111">Configure MessagePack on the server</span></span>

<span data-ttu-id="fbccd-112">Um das messagepack-Hub-Protokoll auf dem Server zu aktivieren, installieren Sie das `Microsoft.AspNetCore.SignalR.Protocols.MessagePack`-Paket in Ihrer APP.</span><span class="sxs-lookup"><span data-stu-id="fbccd-112">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="fbccd-113">Fügen Sie in der Datei "Startup.cs" `AddMessagePackProtocol` zum `AddSignalR`-Befehl hinzu, um die Unterstützung von messagepack auf dem Server zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="fbccd-113">In the Startup.cs file add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="fbccd-114">JSON ist standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="fbccd-114">JSON is enabled by default.</span></span> <span data-ttu-id="fbccd-115">Das Hinzufügen von messagepack ermöglicht die Unterstützung für JSON-und messagepack-Clients.</span><span class="sxs-lookup"><span data-stu-id="fbccd-115">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="fbccd-116">Um die Art und Weise anzupassen, wie messagepack Ihre Daten formatiert, verwendet `AddMessagePackProtocol` einen Delegaten für die Konfiguration von Optionen.</span><span class="sxs-lookup"><span data-stu-id="fbccd-116">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="fbccd-117">In diesem Delegaten kann die `FormatterResolvers`-Eigenschaft verwendet werden, um messagepack-Serialisierungsoptionen zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="fbccd-117">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="fbccd-118">Weitere Informationen zur Funktionsweise der Konflikt Löser finden Sie in der messagepack-Bibliothek unter [messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="fbccd-118">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="fbccd-119">Attribute können für die Objekte verwendet werden, die Sie serialisieren möchten, um zu definieren, wie Sie behandelt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="fbccd-119">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="fbccd-120">Konfigurieren von messagepack auf dem Client</span><span class="sxs-lookup"><span data-stu-id="fbccd-120">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="fbccd-121">JSON ist für die unterstützten Clients standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="fbccd-121">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="fbccd-122">Clients können nur ein einzelnes Protokoll unterstützen.</span><span class="sxs-lookup"><span data-stu-id="fbccd-122">Clients can only support a single protocol.</span></span> <span data-ttu-id="fbccd-123">Durch Hinzufügen der Unterstützung von messagepack werden alle zuvor konfigurierten Protokolle ersetzt.</span><span class="sxs-lookup"><span data-stu-id="fbccd-123">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="fbccd-124">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="fbccd-124">.NET client</span></span>

<span data-ttu-id="fbccd-125">Um messagepack im .NET-Client zu aktivieren, installieren Sie das Paket `Microsoft.AspNetCore.SignalR.Protocols.MessagePack`, und nennen Sie `AddMessagePackProtocol` auf `HubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="fbccd-125">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="fbccd-126">Dieser `AddMessagePackProtocol`-Aufruf verwendet einen Delegaten zum Konfigurieren von Optionen wie dem Server.</span><span class="sxs-lookup"><span data-stu-id="fbccd-126">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="fbccd-127">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="fbccd-127">JavaScript client</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fbccd-128">Die Unterstützung von messagepack für den JavaScript-Client wird durch das NPM-Paket `@microsoft/signalr-protocol-msgpack` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="fbccd-128">MessagePack support for the JavaScript client is provided by the `@microsoft/signalr-protocol-msgpack` npm package.</span></span> <span data-ttu-id="fbccd-129">Installieren Sie das Paket, indem Sie den folgenden Befehl in einer Befehlsshell ausführen:</span><span class="sxs-lookup"><span data-stu-id="fbccd-129">Install the package by executing the following command in a command shell:</span></span>

```console
npm install @microsoft/signalr-protocol-msgpack
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fbccd-130">Die Unterstützung von messagepack für den JavaScript-Client wird durch das NPM-Paket `@aspnet/signalr-protocol-msgpack` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="fbccd-130">MessagePack support for the JavaScript client is provided by the `@aspnet/signalr-protocol-msgpack` npm package.</span></span> <span data-ttu-id="fbccd-131">Installieren Sie das Paket, indem Sie den folgenden Befehl in einer Befehlsshell ausführen:</span><span class="sxs-lookup"><span data-stu-id="fbccd-131">Install the package by executing the following command in a command shell:</span></span>

```console
npm install @aspnet/signalr-protocol-msgpack
```

::: moniker-end

<span data-ttu-id="fbccd-132">Nach der Installation des NPM-Pakets kann das Modul direkt über ein JavaScript-Modul Lade Modul verwendet oder in den Browser importiert werden, indem Sie auf die folgende Datei verweisen:</span><span class="sxs-lookup"><span data-stu-id="fbccd-132">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fbccd-133">*node_modules @ no__t-1 @ no__t-2*</span><span class="sxs-lookup"><span data-stu-id="fbccd-133">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fbccd-134">*node_modules @ no__t-1 @ no__t-2*</span><span class="sxs-lookup"><span data-stu-id="fbccd-134">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

::: moniker-end

<span data-ttu-id="fbccd-135">In einem Browser muss auch auf die `msgpack5`-Bibliothek verwiesen werden.</span><span class="sxs-lookup"><span data-stu-id="fbccd-135">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="fbccd-136">Verwenden Sie ein `<script>`-Tag, um einen Verweis zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="fbccd-136">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="fbccd-137">Die Bibliothek finden Sie unter *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="fbccd-137">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="fbccd-138">Wenn das `<script>`-Element verwendet wird, ist die Reihenfolge wichtig.</span><span class="sxs-lookup"><span data-stu-id="fbccd-138">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="fbccd-139">Wenn auf *signalr-Protocol-msgpack. js* vor *msgpack5. js*verwiesen wird, tritt ein Fehler auf, wenn versucht wird, eine Verbindung mit messagepack herzustellen.</span><span class="sxs-lookup"><span data-stu-id="fbccd-139">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="fbccd-140">*signalr. js* ist auch vor *signalr-Protocol-msgpack. js*erforderlich.</span><span class="sxs-lookup"><span data-stu-id="fbccd-140">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="fbccd-141">Beim Hinzufügen von `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` zur `HubConnectionBuilder` wird der Client für die Verwendung des messagepack-Protokolls konfiguriert, wenn eine Verbindung mit einem Server hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="fbccd-141">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="fbccd-142">Zurzeit gibt es keine Konfigurationsoptionen für das messagepack-Protokoll auf dem JavaScript-Client.</span><span class="sxs-lookup"><span data-stu-id="fbccd-142">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="fbccd-143">Messagepack-Quiri</span><span class="sxs-lookup"><span data-stu-id="fbccd-143">MessagePack quirks</span></span>

<span data-ttu-id="fbccd-144">Bei der Verwendung des messagepack-Hub-Protokolls sind einige Aspekte zu beachten.</span><span class="sxs-lookup"><span data-stu-id="fbccd-144">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="fbccd-145">Beim messagepack wird die Groß-/Kleinschreibung beachtet</span><span class="sxs-lookup"><span data-stu-id="fbccd-145">MessagePack is case-sensitive</span></span>

<span data-ttu-id="fbccd-146">Beim messagepack-Protokoll wird die Groß-/Kleinschreibung beachtet.</span><span class="sxs-lookup"><span data-stu-id="fbccd-146">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="fbccd-147">Sehen Sie sich beispielsweise die C# folgende Klasse an:</span><span class="sxs-lookup"><span data-stu-id="fbccd-147">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="fbccd-148">Beim Senden vom JavaScript-Client müssen Sie `PascalCased`-Eigenschaftsnamen verwenden, da die Groß-/Kleinschreibung genau mit der C# -Klasse übereinstimmen muss.</span><span class="sxs-lookup"><span data-stu-id="fbccd-148">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="fbccd-149">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="fbccd-149">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="fbccd-150">Die Verwendung von `camelCased`-Namen wird nicht C# ordnungsgemäß an die-Klasse gebunden.</span><span class="sxs-lookup"><span data-stu-id="fbccd-150">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="fbccd-151">Sie können dieses Problem umgehen, indem Sie das `Key`-Attribut verwenden, um einen anderen Namen für die messagepack-Eigenschaft anzugeben.</span><span class="sxs-lookup"><span data-stu-id="fbccd-151">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="fbccd-152">Weitere Informationen finden Sie in [der Dokumentation zu messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="fbccd-152">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="fbccd-153">DateTime. Kind wird bei der Serialisierung/Deserialisierung nicht beibehalten.</span><span class="sxs-lookup"><span data-stu-id="fbccd-153">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="fbccd-154">Das messagepack-Protokoll bietet keine Möglichkeit, den `Kind`-Wert einer `DateTime` zu codieren.</span><span class="sxs-lookup"><span data-stu-id="fbccd-154">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="fbccd-155">Demzufolge geht das messagepack-Hub-Protokoll beim Deserialisieren eines Datums davon aus, dass das eingehende Datum im UTC-Format vorliegt.</span><span class="sxs-lookup"><span data-stu-id="fbccd-155">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="fbccd-156">Wenn Sie in der Ortszeit mit `DateTime`-Werten arbeiten, empfiehlt es sich, vor dem Senden in die UTC zu wechseln.</span><span class="sxs-lookup"><span data-stu-id="fbccd-156">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="fbccd-157">Konvertieren Sie Sie von UTC in lokale Zeit, wenn Sie Sie empfangen.</span><span class="sxs-lookup"><span data-stu-id="fbccd-157">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="fbccd-158">Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub [-Problem ASPNET/signalr # 2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="fbccd-158">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="fbccd-159">"DateTime. MinValue" wird von messagepack in JavaScript nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="fbccd-159">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="fbccd-160">Die vom signalr-JavaScript-Client verwendete [msgpack5](https://github.com/mcollina/msgpack5) -Bibliothek unterstützt den Typ "`timestamp96`" in messagepack nicht.</span><span class="sxs-lookup"><span data-stu-id="fbccd-160">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="fbccd-161">Dieser Typ wird verwendet, um sehr große Datumswerte zu codieren (entweder sehr früh in der Vergangenheit oder sehr weit in der Zukunft).</span><span class="sxs-lookup"><span data-stu-id="fbccd-161">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="fbccd-162">Der Wert von `DateTime.MinValue` ist `January 1, 0001`, der in einem `timestamp96`-Wert codiert werden muss.</span><span class="sxs-lookup"><span data-stu-id="fbccd-162">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="fbccd-163">Aus diesem Grund wird das Senden von `DateTime.MinValue` an einen JavaScript-Client nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="fbccd-163">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="fbccd-164">Wenn `DateTime.MinValue` vom JavaScript-Client empfangen wird, wird der folgende Fehler ausgelöst:</span><span class="sxs-lookup"><span data-stu-id="fbccd-164">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="fbccd-165">In der Regel wird `DateTime.MinValue` verwendet, um einen "Missing"-oder `null`-Wert zu codieren.</span><span class="sxs-lookup"><span data-stu-id="fbccd-165">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="fbccd-166">Wenn Sie diesen Wert in messagepack codieren müssen, verwenden Sie einen NULL@no__t able-Wert (`DateTime?`), oder codieren Sie einen separaten `bool`-Wert, der angibt, ob das Datum vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="fbccd-166">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="fbccd-167">Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub [-Problem ASPNET/signalr # 2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="fbccd-167">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="fbccd-168">Unterstützung von messagepack in der "Ahead-of-Time"-Kompilierungs Umgebung</span><span class="sxs-lookup"><span data-stu-id="fbccd-168">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="fbccd-169">Die vom .NET-Client und-Server verwendete [messagepack-CSharp-](https://github.com/neuecc/MessagePack-CSharp) Bibliothek verwendet die Codegenerierung zum Optimieren der Serialisierung.</span><span class="sxs-lookup"><span data-stu-id="fbccd-169">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="fbccd-170">Daher wird Sie standardmäßig nicht in Umgebungen unterstützt, in denen die "vorab"-Kompilierung (z. b. xamarin IOS oder Unity) verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="fbccd-170">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="fbccd-171">Es ist möglich, das messagepack in diesen Umgebungen zu verwenden, indem der Serialisierungsprogramme-/Deserialisierungscode "vorab erstellt" wird.</span><span class="sxs-lookup"><span data-stu-id="fbccd-171">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="fbccd-172">Weitere Informationen finden Sie in [der Dokumentation zu messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="fbccd-172">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="fbccd-173">Nachdem Sie die serialisierungsinitialisierer vorab generiert haben, können Sie Sie mit dem an `AddMessagePackProtocol` über gebenden Konfigurations Delegaten registrieren:</span><span class="sxs-lookup"><span data-stu-id="fbccd-173">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="fbccd-174">Typüberprüfungen sind in messagepack strenger.</span><span class="sxs-lookup"><span data-stu-id="fbccd-174">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="fbccd-175">Das JSON Hub-Protokoll führt während der Deserialisierung Typkonvertierungen durch.</span><span class="sxs-lookup"><span data-stu-id="fbccd-175">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="fbccd-176">Wenn das eingehende Objekt z. b. einen-Eigenschafts Wert hat, der eine Zahl (`{ foo: 42 }`) ist, aber die-Eigenschaft in der .NET-Klasse den Typ `string` aufweist, wird der Wert konvertiert.</span><span class="sxs-lookup"><span data-stu-id="fbccd-176">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="fbccd-177">Allerdings führt messagepack diese Konvertierung nicht durch und löst eine Ausnahme aus, die in serverseitigen Protokollen (und in der-Konsole) angezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="fbccd-177">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="fbccd-178">Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub [-Problem ASPNET/signalr # 2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="fbccd-178">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="fbccd-179">Verwandte Ressourcen</span><span class="sxs-lookup"><span data-stu-id="fbccd-179">Related resources</span></span>

* [<span data-ttu-id="fbccd-180">Erste Schritte</span><span class="sxs-lookup"><span data-stu-id="fbccd-180">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="fbccd-181">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="fbccd-181">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="fbccd-182">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="fbccd-182">JavaScript client</span></span>](xref:signalr/javascript-client)

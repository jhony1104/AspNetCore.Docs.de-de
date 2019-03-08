---
title: ASP.NET Core SignalR-Konfiguration
author: bradygaster
description: Erfahren Sie, wie Sie ASP.NET Core SignalR-apps konfigurieren.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 02/07/2019
uid: signalr/configuration
ms.openlocfilehash: 070d6fed26b6d14c4b8a35d0f7d94abafb08993b
ms.sourcegitcommit: 191d21c1e37b56f0df0187e795d9a56388bbf4c7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/08/2019
ms.locfileid: "57665414"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="04e12-103">ASP.NET Core SignalR-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="04e12-103">ASP.NET Core SignalR configuration</span></span>

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="04e12-104">JSON/MessagePack Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="04e12-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="04e12-105">ASP.NET Core SignalR unterstützt zwei Protokolle für die Codierung von Nachrichten: [JSON](https://www.json.org/) und [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="04e12-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="04e12-106">Jedes Protokoll verfügt über Konfigurationsoptionen für die Serialisierung.</span><span class="sxs-lookup"><span data-stu-id="04e12-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="04e12-107">JSON-Serialisierung kann konfiguriert werden, auf dem Server mithilfe der [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) Erweiterungsmethode, die nach dem hinzuzufügenden [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in Ihre `Startup.ConfigureServices` Methode.</span><span class="sxs-lookup"><span data-stu-id="04e12-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="04e12-108">Die `AddJsonProtocol` Methode verwendet einen Delegaten, der empfängt eine `options` Objekt.</span><span class="sxs-lookup"><span data-stu-id="04e12-108">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="04e12-109">Die [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) -Eigenschaft für dieses Objekt ist ein JSON.NET `JsonSerializerSettings` -Objekt, das zum Konfigurieren der Serialisierung der Argumente und Rückgabewerte verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="04e12-109">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="04e12-110">Finden Sie unter den [JSON.NET-Dokumentation](https://www.newtonsoft.com/json/help/html/Introduction.htm) Weitere Details.</span><span class="sxs-lookup"><span data-stu-id="04e12-110">See the [JSON.NET Documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm) for more details.</span></span>

<span data-ttu-id="04e12-111">Verwenden Sie beispielsweise zum Konfigurieren der Serialisierer, verwenden Sie "PascalCase" Eigenschaftennamen, anstelle der Standardnamen "CamelCase" den folgenden Code ein:</span><span class="sxs-lookup"><span data-stu-id="04e12-111">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver = 
        new DefaultContractResolver();
    });
```

<span data-ttu-id="04e12-112">In der .NET Client `AddJsonProtocol` Erweiterungsmethode vorhanden ist, auf [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="04e12-112">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="04e12-113">Die `Microsoft.Extensions.DependencyInjection` Namespace muss importiert werden, um die Erweiterungsmethode zu beheben:</span><span class="sxs-lookup"><span data-stu-id="04e12-113">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver = 
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="04e12-114">Es ist nicht möglich, das JSON-Serialisierung in der JavaScript-Client zu diesem Zeitpunkt zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="04e12-114">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="04e12-115">MessagePack Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="04e12-115">MessagePack serialization options</span></span>

<span data-ttu-id="04e12-116">MessagePack Serialisierung kann konfiguriert werden, indem ein Delegat, der die [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) aufrufen.</span><span class="sxs-lookup"><span data-stu-id="04e12-116">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="04e12-117">Finden Sie unter [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) Weitere Details.</span><span class="sxs-lookup"><span data-stu-id="04e12-117">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="04e12-118">Es ist nicht möglich, MessagePack Serialisierung zu diesem Zeitpunkt im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="04e12-118">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="04e12-119">Konfigurieren Sie Serveroptionen</span><span class="sxs-lookup"><span data-stu-id="04e12-119">Configure server options</span></span>

<span data-ttu-id="04e12-120">Die folgende Tabelle beschreibt die Optionen zum Konfigurieren von SignalR-Hubs:</span><span class="sxs-lookup"><span data-stu-id="04e12-120">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="04e12-121">Option</span><span class="sxs-lookup"><span data-stu-id="04e12-121">Option</span></span> | <span data-ttu-id="04e12-122">Standardwert</span><span class="sxs-lookup"><span data-stu-id="04e12-122">Default Value</span></span> | <span data-ttu-id="04e12-123">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="04e12-123">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="04e12-124">30 Sekunden</span><span class="sxs-lookup"><span data-stu-id="04e12-124">30 seconds</span></span> | <span data-ttu-id="04e12-125">Der Server berücksichtigt den Client getrennt wird, wenn sie eine Nachricht (einschließlich Keep-alive-) in einem bestimmten Intervall empfangen hat.</span><span class="sxs-lookup"><span data-stu-id="04e12-125">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="04e12-126">Es kann länger als diese Timeoutintervall für den Client tatsächlich gekennzeichnet werden nicht verbunden ist, aufgrund der Implementierung dauern.</span><span class="sxs-lookup"><span data-stu-id="04e12-126">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="04e12-127">Der empfohlene Wert ist doppelte die `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="04e12-127">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="04e12-128">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="04e12-128">15 seconds</span></span> | <span data-ttu-id="04e12-129">Wenn der Client innerhalb dieses Zeitraums eine anfängliche Handshake-Nachricht senden, nicht, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="04e12-129">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="04e12-130">Dies ist eine erweiterte Einstellung, die nur geändert werden soll, wenn der Handshake-Timeout-Fehlern aufgrund von schweren Netzwerklatenz auftreten.</span><span class="sxs-lookup"><span data-stu-id="04e12-130">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="04e12-131">Weitere Einzelheiten für den handshakeprozess finden Sie in der [SignalR-Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="04e12-131">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="04e12-132">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="04e12-132">15 seconds</span></span> | <span data-ttu-id="04e12-133">Wenn der Server eine Nachricht nicht innerhalb dieses Intervalls gesendet noch nicht, wird eine Ping-Nachricht automatisch gesendet, die Verbindung geöffnet bleiben.</span><span class="sxs-lookup"><span data-stu-id="04e12-133">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="04e12-134">Wenn Sie `KeepAliveInterval`, Ändern der `ServerTimeout` / `serverTimeoutInMilliseconds` auf dem Client festlegen.</span><span class="sxs-lookup"><span data-stu-id="04e12-134">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="04e12-135">Die empfohlene `ServerTimeout` / `serverTimeoutInMilliseconds` Wert double der `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="04e12-135">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="04e12-136">Alle installierten Protokolle</span><span class="sxs-lookup"><span data-stu-id="04e12-136">All installed protocols</span></span> | <span data-ttu-id="04e12-137">Von diesen Hub unterstützten Protokolle.</span><span class="sxs-lookup"><span data-stu-id="04e12-137">Protocols supported by this hub.</span></span> <span data-ttu-id="04e12-138">Standardmäßig alle Protokolle, die auf dem Server registriert sind zulässig, aber Protokolle können aus dieser Liste Deaktivieren bestimmter Protokolle für individuelle Hubs entfernt werden.</span><span class="sxs-lookup"><span data-stu-id="04e12-138">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="04e12-139">Wenn `true`, detaillierte Ausnahme Nachrichten werden an Clients zurückgegeben, wenn in einer hubmethode eine Ausnahme ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="04e12-139">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="04e12-140">Der Standardwert ist `false`, wie diese ausnahmemeldungen können vertraulichen Informationen enthalten.</span><span class="sxs-lookup"><span data-stu-id="04e12-140">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="04e12-141">Optionen für alle Hubs konfiguriert werden können, durch die Bereitstellung einer Optionen-Delegat, der die `AddSignalR` Aufrufen in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="04e12-141">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="04e12-142">Optionen für einen einzelnen Hub überschreiben die globale Optionen im `AddSignalR` und kann konfiguriert werden, mithilfe von <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span><span class="sxs-lookup"><span data-stu-id="04e12-142">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="04e12-143">Erweiterte HTTP-Konfigurationsoptionen</span><span class="sxs-lookup"><span data-stu-id="04e12-143">Advanced HTTP configuration options</span></span>

<span data-ttu-id="04e12-144">Verwendung `HttpConnectionDispatcherOptions` so konfigurieren Sie erweiterte Einstellungen, die im Zusammenhang mit Transporte und Speicherverwaltung für Puffer.</span><span class="sxs-lookup"><span data-stu-id="04e12-144">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="04e12-145">Diese Optionen werden durch Übergeben eines Delegaten, konfiguriert [MapHub\<T >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="04e12-145">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) => 
    {
        var desiredTransports = 
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) => 
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="04e12-146">Die folgende Tabelle beschreibt die Optionen zum Konfigurieren von ASP.NET Core SignalR erweiterten HTTP-Optionen:</span><span class="sxs-lookup"><span data-stu-id="04e12-146">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="04e12-147">Option</span><span class="sxs-lookup"><span data-stu-id="04e12-147">Option</span></span> | <span data-ttu-id="04e12-148">Standardwert</span><span class="sxs-lookup"><span data-stu-id="04e12-148">Default Value</span></span> | <span data-ttu-id="04e12-149">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="04e12-149">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="04e12-150">32 KB</span><span class="sxs-lookup"><span data-stu-id="04e12-150">32 KB</span></span> | <span data-ttu-id="04e12-151">Die maximale Anzahl von Bytes, die vom Client empfangen, die die Server-Puffer.</span><span class="sxs-lookup"><span data-stu-id="04e12-151">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="04e12-152">Durch Erhöhen dieses Wertes können den Server, größere Nachrichten zu empfangen, aber arbeitsspeichernutzung negativ beeinträchtigt werden kann.</span><span class="sxs-lookup"><span data-stu-id="04e12-152">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="04e12-153">Sammeln von Daten automatisch aus der `Authorize` Attribute, die auf die hubklasse angewendet.</span><span class="sxs-lookup"><span data-stu-id="04e12-153">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="04e12-154">Eine Liste der [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) Objekte verwendet, um zu bestimmen, ob ein Client autorisiert ist, eine Verbindung mit dem Hub herstellen.</span><span class="sxs-lookup"><span data-stu-id="04e12-154">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="04e12-155">32 KB</span><span class="sxs-lookup"><span data-stu-id="04e12-155">32 KB</span></span> | <span data-ttu-id="04e12-156">Die maximale Anzahl von Bytes, die von der app gesendet werden, die die Server-Puffer.</span><span class="sxs-lookup"><span data-stu-id="04e12-156">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="04e12-157">Durch Erhöhen dieses Wertes können den Server aus, um größere Nachrichten zu senden, aber arbeitsspeichernutzung negativ beeinträchtigt werden kann.</span><span class="sxs-lookup"><span data-stu-id="04e12-157">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="04e12-158">Alle Transporte sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="04e12-158">All Transports are enabled.</span></span> | <span data-ttu-id="04e12-159">Eine Bitmaske der `HttpTransportType` Werte, die die Transporte einschränken, können ein Client kann die Verbindung verwenden.</span><span class="sxs-lookup"><span data-stu-id="04e12-159">A bitmask of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="04e12-160">Siehe weiter unten.</span><span class="sxs-lookup"><span data-stu-id="04e12-160">See below.</span></span> | <span data-ttu-id="04e12-161">Zusätzliche Optionen, die speziell für den Transport Long Polling.</span><span class="sxs-lookup"><span data-stu-id="04e12-161">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="04e12-162">Siehe weiter unten.</span><span class="sxs-lookup"><span data-stu-id="04e12-162">See below.</span></span> | <span data-ttu-id="04e12-163">Zusätzliche Optionen, die speziell für die WebSockets-Übertragung.</span><span class="sxs-lookup"><span data-stu-id="04e12-163">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="04e12-164">Der Transport Long Polling verfügt über zusätzliche Optionen, die mit konfiguriert werden können die `LongPolling` Eigenschaft:</span><span class="sxs-lookup"><span data-stu-id="04e12-164">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="04e12-165">Option</span><span class="sxs-lookup"><span data-stu-id="04e12-165">Option</span></span> | <span data-ttu-id="04e12-166">Standardwert</span><span class="sxs-lookup"><span data-stu-id="04e12-166">Default Value</span></span> | <span data-ttu-id="04e12-167">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="04e12-167">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="04e12-168">90 Sekunden</span><span class="sxs-lookup"><span data-stu-id="04e12-168">90 seconds</span></span> | <span data-ttu-id="04e12-169">Die maximale Zeitspanne wartet der Server eine Nachricht an den Client gesendet werden soll, bevor eine Anforderung für die einzelnen Abfrage beendet.</span><span class="sxs-lookup"><span data-stu-id="04e12-169">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="04e12-170">Durch senken dieses Werts bewirkt, dass den Client neue abrufanforderungen häufiger ausgeben.</span><span class="sxs-lookup"><span data-stu-id="04e12-170">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="04e12-171">Der WebSocket-Transport verfügt über zusätzliche Optionen, die mit konfiguriert werden können die `WebSockets` Eigenschaft:</span><span class="sxs-lookup"><span data-stu-id="04e12-171">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="04e12-172">Option</span><span class="sxs-lookup"><span data-stu-id="04e12-172">Option</span></span> | <span data-ttu-id="04e12-173">Standardwert</span><span class="sxs-lookup"><span data-stu-id="04e12-173">Default Value</span></span> | <span data-ttu-id="04e12-174">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="04e12-174">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="04e12-175">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="04e12-175">5 seconds</span></span> | <span data-ttu-id="04e12-176">Nachdem der Server wird geschlossen, wenn der Client nicht innerhalb dieses Zeitraums zu schließen, wird die Verbindung beendet.</span><span class="sxs-lookup"><span data-stu-id="04e12-176">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="04e12-177">Ein Delegat, der verwendet werden kann, Festlegen der `Sec-WebSocket-Protocol` -Header auf einen benutzerdefinierten Wert.</span><span class="sxs-lookup"><span data-stu-id="04e12-177">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="04e12-178">Der Delegat empfängt die Werte, die vom Client als Eingabe angefordert werden, und es wird erwartet, um den gewünschten Wert zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="04e12-178">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="04e12-179">Konfigurieren Sie Clientoptionen</span><span class="sxs-lookup"><span data-stu-id="04e12-179">Configure client options</span></span>

<span data-ttu-id="04e12-180">Clientoptionen können konfiguriert werden, auf die `HubConnectionBuilder` Typ (verfügbar in den .NET- und JavaScript-Clients).</span><span class="sxs-lookup"><span data-stu-id="04e12-180">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="04e12-181">Es ist auch verfügbar im Java-Client, aber die `HttpHubConnectionBuilder` Unterklasse ist, was die Builder-Konfigurationsoptionen, sowie auf enthält die `HubConnection` selbst.</span><span class="sxs-lookup"><span data-stu-id="04e12-181">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="04e12-182">Konfigurieren der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="04e12-182">Configure logging</span></span>

<span data-ttu-id="04e12-183">Protokollierung wurde so konfiguriert, der .NET Client die `ConfigureLogging` Methode.</span><span class="sxs-lookup"><span data-stu-id="04e12-183">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="04e12-184">Protokollierung von Anbietern und Filter kann auf die gleiche Weise registriert werden, wie sie auf dem Server sind.</span><span class="sxs-lookup"><span data-stu-id="04e12-184">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="04e12-185">Finden Sie unter den [Protokollierung in ASP.NET Core](xref:fundamentals/logging/index) Dokumentation zu informieren.</span><span class="sxs-lookup"><span data-stu-id="04e12-185">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="04e12-186">Um Anbieter für die Protokollierung zu registrieren, müssen Sie die erforderlichen Pakete installieren.</span><span class="sxs-lookup"><span data-stu-id="04e12-186">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="04e12-187">Finden Sie unter den [integrierte Protokollierungsanbieter](xref:fundamentals/logging/index#built-in-logging-providers) Abschnitt der Dokumentation für eine vollständige Liste.</span><span class="sxs-lookup"><span data-stu-id="04e12-187">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="04e12-188">Beispielsweise um konsolenprotokollierung zu aktivieren, installieren die `Microsoft.Extensions.Logging.Console` NuGet-Paket.</span><span class="sxs-lookup"><span data-stu-id="04e12-188">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="04e12-189">Rufen Sie die `AddConsole` Erweiterungsmethode:</span><span class="sxs-lookup"><span data-stu-id="04e12-189">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="04e12-190">Im JavaScript-Client, eine ähnliche `configureLogging` Methode vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="04e12-190">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="04e12-191">Geben Sie einen `LogLevel` Wert, der angibt, der Mindestebene von protokollmeldungen zu erzeugen.</span><span class="sxs-lookup"><span data-stu-id="04e12-191">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="04e12-192">Protokolle werden in das Browserfenster für die Konsole geschrieben.</span><span class="sxs-lookup"><span data-stu-id="04e12-192">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="04e12-193">Geben Sie zum Deaktivieren der Protokollierung vollständig, `signalR.LogLevel.None` in die `configureLogging` Methode.</span><span class="sxs-lookup"><span data-stu-id="04e12-193">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="04e12-194">Weitere Informationen zur Protokollierung finden Sie unter den [SignalR-Diagnose – Dokumentation](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="04e12-194">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="04e12-195">Der SignalR-Java-Client verwendet die [SLF4J](https://www.slf4j.org/) Bibliothek für die Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="04e12-195">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="04e12-196">Es ist eine allgemeine protokollierungs-API an, die Benutzer der Bibliothek, wählen ihre eigenen spezifischen protokollierungsimplementierung bereitgestellt werden, indem Sie in einer bestimmten Protokollierung Abhängigkeit bringen kann.</span><span class="sxs-lookup"><span data-stu-id="04e12-196">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="04e12-197">Der folgende Codeausschnitt zeigt, wie Sie mit `java.util.logging` mit dem SignalR-Java-Client.</span><span class="sxs-lookup"><span data-stu-id="04e12-197">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="04e12-198">Wenn Sie die Anmeldung bei Ihrer Abhängigkeiten nicht konfigurieren, lädt SLF4J eine Protokollierung standardmäßig nicht-Vorgang die folgende Warnmeldung an:</span><span class="sxs-lookup"><span data-stu-id="04e12-198">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="04e12-199">Dies kann problemlos ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="04e12-199">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="04e12-200">Konfigurieren von zulässigen Transporte</span><span class="sxs-lookup"><span data-stu-id="04e12-200">Configure allowed transports</span></span>

<span data-ttu-id="04e12-201">Die von SignalR verwendete Transporte können konfiguriert werden, der `WithUrl` aufrufen (`withUrl` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="04e12-201">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="04e12-202">Eine bitweise OR-Operation der Werte von `HttpTransportType` kann verwendet werden, um den Client nur die angegebenen Transporte verwenden zu beschränken.</span><span class="sxs-lookup"><span data-stu-id="04e12-202">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="04e12-203">Alle Transporte sind standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="04e12-203">All transports are enabled by default.</span></span>

<span data-ttu-id="04e12-204">Zulassen Sie z. B. um den Transport Server-Sent-Ereignisse zu deaktivieren, aber WebSockets oder Long Polling Verbindungen:</span><span class="sxs-lookup"><span data-stu-id="04e12-204">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="04e12-205">In JavaScript-Client-Transporte werden konfiguriert, durch Festlegen der `transport` Feld für das Options-Objekt, das bereitgestellt, um `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="04e12-205">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="04e12-206">In dieser Version von Java ist Client Websockets der einzige verfügbare Transport.</span><span class="sxs-lookup"><span data-stu-id="04e12-206">In this version of the Java client websockets is the only available transport.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="04e12-207">Im Java-Client der Transport aktiviert ist, und die `withTransport` Methode für die `HttpHubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="04e12-207">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="04e12-208">Die Java-Client verwendet standardmäßig die WebSockets-Übertragung.</span><span class="sxs-lookup"><span data-stu-id="04e12-208">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```
> [!NOTE]
> <span data-ttu-id="04e12-209">Der SignalR-Java-Client-unterstützt fallback Transport noch keine.</span><span class="sxs-lookup"><span data-stu-id="04e12-209">The SignalR Java client doesn't support transport fallback yet.</span></span>

::: moniker-end

### <a name="configure-bearer-authentication"></a><span data-ttu-id="04e12-210">Konfigurieren von Bearer-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="04e12-210">Configure bearer authentication</span></span>

<span data-ttu-id="04e12-211">Um Authentifizierungsdaten zusammen mit Anforderungen für SignalR bereitzustellen, verwenden Sie die `AccessTokenProvider` Option (`accessTokenFactory` in JavaScript) an eine Funktion, die den gewünschten Zugangs-Token zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="04e12-211">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="04e12-212">In der .NET Client dieses Zugriffstoken wird als übergeben, eine HTTP-"Bearer-Authentifizierung" token (mithilfe der `Authorization` Header vom Typ `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="04e12-212">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="04e12-213">In JavaScript-Client wird das Zugriffstoken als bearertoken, verwendet **außer** in einigen Fällen, in dem Browser APIs beschränken die Möglichkeit, anzuwenden Header (insbesondere im Server-Sent-Ereignisse und WebSockets-Anforderungen).</span><span class="sxs-lookup"><span data-stu-id="04e12-213">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="04e12-214">In diesen Fällen wird das Zugriffstoken als einen Abfragezeichenfolgenwert bereitgestellt `access_token`.</span><span class="sxs-lookup"><span data-stu-id="04e12-214">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="04e12-215">In der .NET Client der `AccessTokenProvider` Option kann angegeben werden, mithilfe der Optionen Delegaten in `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="04e12-215">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="04e12-216">In JavaScript-Client das Zugriffstoken ist konfiguriert, indem die `accessTokenFactory` Feld das Optionsobjekt in `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="04e12-216">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```


<span data-ttu-id="04e12-217">In den SignalR-Java-Client, können Sie ein trägertoken, das zur Authentifizierung verwendet wird, durch die Bereitstellung einer Access token Factory konfigurieren die [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="04e12-217">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="04e12-218">Verwendung [WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) zu einer [RxJava](https://github.com/ReactiveX/RxJava) [einzelne<String>](http://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="04e12-218">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single<String>](http://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="04e12-219">Mit einem Aufruf von [Single.defer](http://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), Sie können Logik schreiben, um Zugriffstoken für den Client zu generieren.</span><span class="sxs-lookup"><span data-stu-id="04e12-219">With a call to [Single.defer](http://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="04e12-220">Timeout und Keep-alive-Optionen konfigurieren</span><span class="sxs-lookup"><span data-stu-id="04e12-220">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="04e12-221">Zusätzliche Optionen zum Konfigurieren von Timeouts und Keep-alive-Verhalten befinden sich die `HubConnection` Objekt selbst:</span><span class="sxs-lookup"><span data-stu-id="04e12-221">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="04e12-222">.NET</span><span class="sxs-lookup"><span data-stu-id="04e12-222">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="04e12-223">Option</span><span class="sxs-lookup"><span data-stu-id="04e12-223">Option</span></span> | <span data-ttu-id="04e12-224">Standardwert</span><span class="sxs-lookup"><span data-stu-id="04e12-224">Default value</span></span> | <span data-ttu-id="04e12-225">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="04e12-225">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="04e12-226">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="04e12-226">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="04e12-227">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="04e12-227">Timeout for server activity.</span></span> <span data-ttu-id="04e12-228">Wenn der Server eine Nachricht in einem bestimmten Intervall gesendet noch nicht, der Client betrachtet, der Server getrennt und der Trigger die `Closed` Ereignis (`onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="04e12-228">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="04e12-229">Dieser Wert muss groß genug für eine Ping-Nachricht, die vom Server gesendet werden **und** innerhalb des Timeoutintervalls vom Client empfangenen.</span><span class="sxs-lookup"><span data-stu-id="04e12-229">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="04e12-230">Der empfohlene Wert ist einer Zahl mindestens doppelt auf dem Server `KeepAliveInterval` Wert fest, damit Zeit für Ping-Nachrichten eingehen.</span><span class="sxs-lookup"><span data-stu-id="04e12-230">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="04e12-231">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="04e12-231">15 seconds</span></span> | <span data-ttu-id="04e12-232">Timeout für die erstmalige Server-Handshake.</span><span class="sxs-lookup"><span data-stu-id="04e12-232">Timeout for initial server handshake.</span></span> <span data-ttu-id="04e12-233">Wenn der Server eine Antwort Handshake in einem bestimmten Intervall nicht sendet, wird vom Client abgebrochen, der Handshake und der Trigger die `Closed` Ereignis (`onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="04e12-233">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="04e12-234">Dies ist eine erweiterte Einstellung, die nur geändert werden soll, wenn der Handshake-Timeout-Fehlern aufgrund von schweren Netzwerklatenz auftreten.</span><span class="sxs-lookup"><span data-stu-id="04e12-234">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="04e12-235">Weitere Einzelheiten für den Handshake-Prozess finden Sie in der [SignalR-Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="04e12-235">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="04e12-236">Im .NET Client Timeoutwerte angegeben sind, als `TimeSpan` Werte.</span><span class="sxs-lookup"><span data-stu-id="04e12-236">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="04e12-237">JavaScript</span><span class="sxs-lookup"><span data-stu-id="04e12-237">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="04e12-238">Option</span><span class="sxs-lookup"><span data-stu-id="04e12-238">Option</span></span> | <span data-ttu-id="04e12-239">Standardwert</span><span class="sxs-lookup"><span data-stu-id="04e12-239">Default value</span></span> | <span data-ttu-id="04e12-240">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="04e12-240">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="04e12-241">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="04e12-241">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="04e12-242">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="04e12-242">Timeout for server activity.</span></span> <span data-ttu-id="04e12-243">Wenn der Server eine Nachricht in einem bestimmten Intervall gesendet noch nicht, der Client betrachtet, der Server getrennt und der Trigger die `onclose` Ereignis.</span><span class="sxs-lookup"><span data-stu-id="04e12-243">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="04e12-244">Dieser Wert muss groß genug für eine Ping-Nachricht, die vom Server gesendet werden **und** innerhalb des Timeoutintervalls vom Client empfangenen.</span><span class="sxs-lookup"><span data-stu-id="04e12-244">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="04e12-245">Der empfohlene Wert ist einer Zahl mindestens doppelt auf dem Server `KeepAliveInterval` Wert fest, damit Zeit für Ping-Nachrichten eingehen.</span><span class="sxs-lookup"><span data-stu-id="04e12-245">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="04e12-246">Java</span><span class="sxs-lookup"><span data-stu-id="04e12-246">Java</span></span>](#tab/java)

| <span data-ttu-id="04e12-247">Option</span><span class="sxs-lookup"><span data-stu-id="04e12-247">Option</span></span> | <span data-ttu-id="04e12-248">Standardwert</span><span class="sxs-lookup"><span data-stu-id="04e12-248">Default value</span></span> | <span data-ttu-id="04e12-249">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="04e12-249">Description</span></span> |
| ----------- | ------------- | ----------- |
|<span data-ttu-id="04e12-250">`getServerTimeout` `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="04e12-250">`getServerTimeout` `setServerTimeout`</span></span> | <span data-ttu-id="04e12-251">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="04e12-251">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="04e12-252">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="04e12-252">Timeout for server activity.</span></span> <span data-ttu-id="04e12-253">Wenn der Server eine Nachricht in einem bestimmten Intervall gesendet noch nicht, der Client betrachtet, der Server getrennt und der Trigger die `onClose` Ereignis.</span><span class="sxs-lookup"><span data-stu-id="04e12-253">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="04e12-254">Dieser Wert muss groß genug für eine Ping-Nachricht, die vom Server gesendet werden **und** innerhalb des Timeoutintervalls vom Client empfangenen.</span><span class="sxs-lookup"><span data-stu-id="04e12-254">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="04e12-255">Der empfohlene Wert ist einer Zahl mindestens doppelt auf dem Server `KeepAliveInterval` Wert fest, damit Zeit für Ping-Nachrichten eingehen.</span><span class="sxs-lookup"><span data-stu-id="04e12-255">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="04e12-256">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="04e12-256">15 seconds</span></span> | <span data-ttu-id="04e12-257">Timeout für die erstmalige Server-Handshake.</span><span class="sxs-lookup"><span data-stu-id="04e12-257">Timeout for initial server handshake.</span></span> <span data-ttu-id="04e12-258">Wenn der Server eine Antwort Handshake in einem bestimmten Intervall nicht sendet, wird vom Client abgebrochen, der Handshake und der Trigger die `onClose` Ereignis.</span><span class="sxs-lookup"><span data-stu-id="04e12-258">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="04e12-259">Dies ist eine erweiterte Einstellung, die nur geändert werden soll, wenn der Handshake-Timeout-Fehlern aufgrund von schweren Netzwerklatenz auftreten.</span><span class="sxs-lookup"><span data-stu-id="04e12-259">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="04e12-260">Weitere Einzelheiten für den Handshake-Prozess finden Sie in der [SignalR-Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="04e12-260">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="04e12-261">Konfigurieren zusätzlicher Optionen</span><span class="sxs-lookup"><span data-stu-id="04e12-261">Configure additional options</span></span>

<span data-ttu-id="04e12-262">Zusätzliche Optionen können konfiguriert werden, der `WithUrl` (`withUrl` in JavaScript)-Methode `HubConnectionBuilder` oder auf die verschiedenen Konfigurations-APIs auf die `HttpHubConnectionBuilder` im Java-Client:</span><span class="sxs-lookup"><span data-stu-id="04e12-262">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="04e12-263">.NET</span><span class="sxs-lookup"><span data-stu-id="04e12-263">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="04e12-264">Option für .NET</span><span class="sxs-lookup"><span data-stu-id="04e12-264">.NET Option</span></span> |  <span data-ttu-id="04e12-265">Standardwert</span><span class="sxs-lookup"><span data-stu-id="04e12-265">Default value</span></span> | <span data-ttu-id="04e12-266">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="04e12-266">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="04e12-267">Eine Funktion zurückgeben einer Zeichenfolge, die als bearertoken in HTTP-Anforderungen Authentifizierung bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="04e12-267">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="04e12-268">Legen Sie diesen `true` um die Aushandlung Schritt zu überspringen.</span><span class="sxs-lookup"><span data-stu-id="04e12-268">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="04e12-269">**Nur unterstützt, wenn die WebSockets-Übertragung der einzige aktiviert-Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="04e12-269">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="04e12-270">Diese Einstellung kann nicht aktiviert werden, wenn mithilfe des Azure SignalR Service.</span><span class="sxs-lookup"><span data-stu-id="04e12-270">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="04e12-271">Empty</span><span class="sxs-lookup"><span data-stu-id="04e12-271">Empty</span></span> | <span data-ttu-id="04e12-272">Eine Auflistung von TLS-Zertifikate zum Authentifizieren von Anforderungen senden.</span><span class="sxs-lookup"><span data-stu-id="04e12-272">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="04e12-273">Empty</span><span class="sxs-lookup"><span data-stu-id="04e12-273">Empty</span></span> | <span data-ttu-id="04e12-274">Eine Auflistung von HTTP-Cookies, die mit jeder HTTP-Anforderung gesendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="04e12-274">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="04e12-275">Empty</span><span class="sxs-lookup"><span data-stu-id="04e12-275">Empty</span></span> | <span data-ttu-id="04e12-276">Die Anmeldeinformationen mit jeder HTTP-Anforderung senden.</span><span class="sxs-lookup"><span data-stu-id="04e12-276">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="04e12-277">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="04e12-277">5 seconds</span></span> | <span data-ttu-id="04e12-278">Nur WebSockets.</span><span class="sxs-lookup"><span data-stu-id="04e12-278">WebSockets only.</span></span> <span data-ttu-id="04e12-279">Die maximale Zeitspanne wartet der Client nach dem Schließen für den Server die Anforderung schließende bestätigt.</span><span class="sxs-lookup"><span data-stu-id="04e12-279">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="04e12-280">Wenn der Server das Schließen nicht innerhalb dieses Zeitraums bestätigt nicht, trennt den Client.</span><span class="sxs-lookup"><span data-stu-id="04e12-280">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="04e12-281">Empty</span><span class="sxs-lookup"><span data-stu-id="04e12-281">Empty</span></span> | <span data-ttu-id="04e12-282">Eine Zuordnung von zusätzlichen HTTP-Header, die mit jeder HTTP-Anforderung gesendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="04e12-282">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="04e12-283">Ein Delegat, der verwendet werden kann, um zu konfigurieren, oder Ersetzen Sie die `HttpMessageHandler` zum Senden von HTTP-Anforderungen verwendet.</span><span class="sxs-lookup"><span data-stu-id="04e12-283">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="04e12-284">WebSocket-Verbindungen verwendet nicht.</span><span class="sxs-lookup"><span data-stu-id="04e12-284">Not used for WebSocket connections.</span></span> <span data-ttu-id="04e12-285">Dieser Delegat muss einen Wert ungleich Null zurückgeben, und den Standardwert als Parameter erhält.</span><span class="sxs-lookup"><span data-stu-id="04e12-285">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="04e12-286">Ändern Sie die Einstellungen auf dieser Standardwert und zurückgeben oder ein neues zurückgeben `HttpMessageHandler` Instanz.</span><span class="sxs-lookup"><span data-stu-id="04e12-286">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="04e12-287">**Beim Ersetzen der Handler, stellen Sie sicher, kopieren Sie die Einstellungen, die Sie aus der bereitgestellte Handler beibehalten möchten, anwenden nicht die konfigurierten Optionen (z. B. Cookies und Header), andernfalls auf den neuen Handler.**</span><span class="sxs-lookup"><span data-stu-id="04e12-287">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="04e12-288">Ein HTTP-Proxy beim Senden von HTTP-Anforderungen verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="04e12-288">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="04e12-289">Legen Sie diese boolescher Wert, um die Standardanmeldeinformationen für HTTP und WebSockets-Anforderungen zu senden.</span><span class="sxs-lookup"><span data-stu-id="04e12-289">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="04e12-290">Dadurch wird die Verwendung der Windows-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="04e12-290">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="04e12-291">Ein Delegat, der so konfigurieren Sie zusätzliche "WebSocket"-Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="04e12-291">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="04e12-292">Empfängt eine Instanz von [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , die verwendet werden kann, um Optionen zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="04e12-292">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="04e12-293">JavaScript</span><span class="sxs-lookup"><span data-stu-id="04e12-293">JavaScript</span></span>](#tab/javascript)
| <span data-ttu-id="04e12-294">JavaScript-Option</span><span class="sxs-lookup"><span data-stu-id="04e12-294">JavaScript Option</span></span> | <span data-ttu-id="04e12-295">Standardwert</span><span class="sxs-lookup"><span data-stu-id="04e12-295">Default Value</span></span> | <span data-ttu-id="04e12-296">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="04e12-296">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="04e12-297">Eine Funktion zurückgeben einer Zeichenfolge, die als bearertoken in HTTP-Anforderungen Authentifizierung bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="04e12-297">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="04e12-298">Legen Sie diesen `true` um die Aushandlung Schritt zu überspringen.</span><span class="sxs-lookup"><span data-stu-id="04e12-298">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="04e12-299">**Nur unterstützt, wenn die WebSockets-Übertragung der einzige aktiviert-Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="04e12-299">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="04e12-300">Diese Einstellung kann nicht aktiviert werden, wenn mithilfe des Azure SignalR Service.</span><span class="sxs-lookup"><span data-stu-id="04e12-300">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="04e12-301">Java</span><span class="sxs-lookup"><span data-stu-id="04e12-301">Java</span></span>](#tab/java)
| <span data-ttu-id="04e12-302">Java Option</span><span class="sxs-lookup"><span data-stu-id="04e12-302">Java Option</span></span> | <span data-ttu-id="04e12-303">Standardwert</span><span class="sxs-lookup"><span data-stu-id="04e12-303">Default Value</span></span> | <span data-ttu-id="04e12-304">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="04e12-304">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="04e12-305">Eine Funktion zurückgeben einer Zeichenfolge, die als bearertoken in HTTP-Anforderungen Authentifizierung bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="04e12-305">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="04e12-306">Legen Sie diesen `true` um die Aushandlung Schritt zu überspringen.</span><span class="sxs-lookup"><span data-stu-id="04e12-306">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="04e12-307">**Nur unterstützt, wenn die WebSockets-Übertragung der einzige aktiviert-Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="04e12-307">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="04e12-308">Diese Einstellung kann nicht aktiviert werden, wenn mithilfe des Azure SignalR Service.</span><span class="sxs-lookup"><span data-stu-id="04e12-308">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="04e12-309">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="04e12-309">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="04e12-310">Empty</span><span class="sxs-lookup"><span data-stu-id="04e12-310">Empty</span></span> | <span data-ttu-id="04e12-311">Eine Zuordnung von zusätzlichen HTTP-Header, die mit jeder HTTP-Anforderung gesendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="04e12-311">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="04e12-312">In der .NET Client können diese Optionen von der Delegat Optionen geändert werden `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="04e12-312">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="04e12-313">In JavaScript-Client können in einem JavaScript-Objekt, das bereitgestellt, um diese Optionen bereitgestellt werden `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="04e12-313">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="04e12-314">Im Java-Client diese Optionen können konfiguriert werden mit den Methoden für die `HttpHubConnectionBuilder` zurückgegeben, die von der `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="04e12-314">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>


```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="04e12-315">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="04e12-315">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

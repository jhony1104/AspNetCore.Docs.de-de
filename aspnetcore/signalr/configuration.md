---
title: ASP.net Core SignalR Konfiguration
author: bradygaster
description: Erfahren Sie, wie Sie ASP.net Core SignalR-apps konfigurieren.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/10/2019
no-loc:
- SignalR
uid: signalr/configuration
ms.openlocfilehash: c225ff88110dc17185a430ac1c422d2433306115
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651685"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="fe4b6-103">ASP.net Core signalr-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="fe4b6-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="fe4b6-104">JSON/messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="fe4b6-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="fe4b6-105">ASP.net Core signalr unterstützt zwei Protokolle für das Codieren von Nachrichten: [JSON](https://www.json.org/) und [messagepack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="fe4b6-106">Jedes Protokoll verfügt über Konfigurationsoptionen für die Serialisierung.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="fe4b6-107">Die JSON-Serialisierung kann mithilfe der [addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) -Erweiterungsmethode auf dem Server konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="fe4b6-108">`AddJsonProtocol` können nach [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="fe4b6-109">Die `AddJsonProtocol`-Methode nimmt einen Delegaten an, der ein `options` Objekt empfängt.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="fe4b6-110">Die [payloadserializeroptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) -Eigenschaft für dieses Objekt ist ein `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> Objekt, das zum Konfigurieren der Serialisierung von Argumenten und Rückgabe Werten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="fe4b6-111">Weitere Informationen finden Sie in der [System. Text. JSON-Dokumentation](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="fe4b6-112">Wenn Sie beispielsweise das Serialisierungsprogramm so konfigurieren möchten, dass die Groß-/Kleinschreibung von Eigenschaftsnamen nicht geändert wird, verwenden Sie den folgenden Code in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="fe4b6-113">Im .NET-Client ist dieselbe `AddJsonProtocol` Erweiterungsmethode auf [hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)vorhanden.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="fe4b6-114">Der `Microsoft.Extensions.DependencyInjection`-Namespace muss importiert werden, um die Erweiterungsmethode aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="fe4b6-115">Zurzeit ist es nicht möglich, JSON-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="fe4b6-116">Wechseln Sie zu "newtonsoft. JSON".</span><span class="sxs-lookup"><span data-stu-id="fe4b6-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="fe4b6-117">Wenn Sie Features von `Newtonsoft.Json` benötigen, die in `System.Text.Json`nicht unterstützt werden, finden Sie weitere Informationen unter [Wechseln zu Newton Soft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="fe4b6-118">Messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="fe4b6-118">MessagePack serialization options</span></span>

<span data-ttu-id="fe4b6-119">Die messagepack-Serialisierung kann konfiguriert werden, indem ein Delegat für den [addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) -Aufruf bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="fe4b6-120">Weitere Informationen finden Sie [unter messagepack in signalr](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="fe4b6-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="fe4b6-121">Zurzeit ist es nicht möglich, die messagepack-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="fe4b6-122">Konfigurieren von Serveroptionen</span><span class="sxs-lookup"><span data-stu-id="fe4b6-122">Configure server options</span></span>

<span data-ttu-id="fe4b6-123">In der folgenden Tabelle werden die Optionen zum Konfigurieren von signalr Hubs beschrieben:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="fe4b6-124">Option</span><span class="sxs-lookup"><span data-stu-id="fe4b6-124">Option</span></span> | <span data-ttu-id="fe4b6-125">Standardwert</span><span class="sxs-lookup"><span data-stu-id="fe4b6-125">Default Value</span></span> | <span data-ttu-id="fe4b6-126">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="fe4b6-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="fe4b6-127">30 Sekunden</span><span class="sxs-lookup"><span data-stu-id="fe4b6-127">30 seconds</span></span> | <span data-ttu-id="fe4b6-128">Der Server wird vom-Client als getrennt betrachtet, wenn in diesem Intervall keine Nachricht (einschließlich Keep-Alive) empfangen wurde.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="fe4b6-129">Es kann länger dauern, bis der Client als getrennt gekennzeichnet ist, weil dies implementiert ist.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="fe4b6-130">Der empfohlene Wert ist Double der `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="fe4b6-131">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="fe4b6-131">15 seconds</span></span> | <span data-ttu-id="fe4b6-132">Wenn der Client innerhalb dieses Zeitraums keine anfängliche Hand Shake Nachricht sendet, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="fe4b6-133">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="fe4b6-134">Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="fe4b6-135">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="fe4b6-135">15 seconds</span></span> | <span data-ttu-id="fe4b6-136">Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="fe4b6-137">Wenn Sie `KeepAliveInterval`ändern, ändern Sie die Einstellung `ServerTimeout`/`serverTimeoutInMilliseconds` auf dem Client.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="fe4b6-138">Die empfohlene `ServerTimeout`/`serverTimeoutInMilliseconds` Wert ist Double der `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="fe4b6-139">Alle installierten Protokolle</span><span class="sxs-lookup"><span data-stu-id="fe4b6-139">All installed protocols</span></span> | <span data-ttu-id="fe4b6-140">Protokolle, die von diesem Hub unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-140">Protocols supported by this hub.</span></span> <span data-ttu-id="fe4b6-141">Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, aber Protokolle können aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="fe4b6-142">Wenn `true`, werden detaillierte Ausnahme Meldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="fe4b6-143">Der Standardwert ist `false`, da diese Ausnahme Meldungen vertrauliche Informationen enthalten können.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="fe4b6-144">Die maximale Anzahl von Elementen, die für Client-uploadstreams gepuffert werden können.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="fe4b6-145">Wenn dieser Grenzwert erreicht wird, wird die Verarbeitung von Aufrufen blockiert, bis der Server streamingelemente verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="fe4b6-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="fe4b6-146">32 KB</span></span> | <span data-ttu-id="fe4b6-147">Maximale Größe einer einzelnen eingehenden Hub-Nachricht.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="fe4b6-148">Optionen können für alle Hubs konfiguriert werden, indem ein Options Delegat für den `AddSignalR` Aufruf in `Startup.ConfigureServices`bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="fe4b6-149">Optionen für einen einzelnen Hub überschreiben die in `AddSignalR` bereitgestellten globalen Optionen und können mithilfe <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="fe4b6-150">Erweiterte http-Konfigurationsoptionen</span><span class="sxs-lookup"><span data-stu-id="fe4b6-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="fe4b6-151">Verwenden Sie `HttpConnectionDispatcherOptions`, um erweiterte Einstellungen im Zusammenhang mit Transporte und der Speicherpuffer Verwaltung zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="fe4b6-152">Diese Optionen werden durch Übergeben eines Delegaten an [maphub\<t->](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<MyHub>("/myhub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="fe4b6-153">In der folgenden Tabelle werden die Optionen zum Konfigurieren der erweiterten http-Optionen von ASP.net Core signalr beschrieben:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="fe4b6-154">Option</span><span class="sxs-lookup"><span data-stu-id="fe4b6-154">Option</span></span> | <span data-ttu-id="fe4b6-155">Standardwert</span><span class="sxs-lookup"><span data-stu-id="fe4b6-155">Default Value</span></span> | <span data-ttu-id="fe4b6-156">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="fe4b6-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="fe4b6-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="fe4b6-157">32 KB</span></span> | <span data-ttu-id="fe4b6-158">Die maximale Anzahl von Bytes, die vom Client vor dem Anwenden des backdrucks vom Server gepuffert werden.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="fe4b6-159">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten schneller empfangen, ohne dass ein Rückdruck angewendet wird, der Arbeitsspeicher Verbrauch kann jedoch gesteigert werden.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="fe4b6-160">Automatisch aus den `Authorize` Attributen gesammelte Daten, die auf die Hub-Klasse angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="fe4b6-161">Eine Liste von [iautorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) -Objekten, die verwendet werden, um zu bestimmen, ob ein Client für die Verbindung mit dem Hub autorisiert ist.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="fe4b6-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="fe4b6-162">32 KB</span></span> | <span data-ttu-id="fe4b6-163">Die maximale Anzahl von Bytes, die von der APP gesendet werden, die der Server puffert, bevor der Rückdruck beobachtet wird</span><span class="sxs-lookup"><span data-stu-id="fe4b6-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="fe4b6-164">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten schneller Puffern, ohne dass auf den Rückstand gewartet wird, aber die Arbeitsspeicher Nutzung erhöhen kann.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="fe4b6-165">Alle Transporte sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-165">All Transports are enabled.</span></span> | <span data-ttu-id="fe4b6-166">Ein Bitflags-Enumeration von `HttpTransportType` Werten, mit denen die Transporte eingeschränkt werden können, die ein Client für die Verbindungs Herstellung verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="fe4b6-167">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-167">See below.</span></span> | <span data-ttu-id="fe4b6-168">Zusätzliche Optionen, die für den langen Abruf Transport spezifisch sind.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="fe4b6-169">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-169">See below.</span></span> | <span data-ttu-id="fe4b6-170">Zusätzliche Optionen für den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-170">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="fe4b6-171">Der lange Abruf Transport bietet zusätzliche Optionen, die mit der `LongPolling`-Eigenschaft konfiguriert werden können:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-171">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="fe4b6-172">Option</span><span class="sxs-lookup"><span data-stu-id="fe4b6-172">Option</span></span> | <span data-ttu-id="fe4b6-173">Standardwert</span><span class="sxs-lookup"><span data-stu-id="fe4b6-173">Default Value</span></span> | <span data-ttu-id="fe4b6-174">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="fe4b6-174">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="fe4b6-175">90 Sekunden</span><span class="sxs-lookup"><span data-stu-id="fe4b6-175">90 seconds</span></span> | <span data-ttu-id="fe4b6-176">Die maximale Zeitspanne, die der Server wartet, bis eine Nachricht an den Client gesendet wird, bevor eine einzelne Abruf Anforderung beendet wird.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-176">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="fe4b6-177">Das verringern dieses Werts bewirkt, dass der Client neue Abruf Anforderungen häufiger ausgibt.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-177">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="fe4b6-178">Der WebSocket-Transport verfügt über zusätzliche Optionen, die mit der `WebSockets`-Eigenschaft konfiguriert werden können:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-178">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="fe4b6-179">Option</span><span class="sxs-lookup"><span data-stu-id="fe4b6-179">Option</span></span> | <span data-ttu-id="fe4b6-180">Standardwert</span><span class="sxs-lookup"><span data-stu-id="fe4b6-180">Default Value</span></span> | <span data-ttu-id="fe4b6-181">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="fe4b6-181">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="fe4b6-182">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="fe4b6-182">5 seconds</span></span> | <span data-ttu-id="fe4b6-183">Wenn der Client nach dem Schließen des Servers nicht innerhalb dieses Zeitraums geschlossen werden kann, wird die Verbindung beendet.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-183">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="fe4b6-184">Ein Delegat, der verwendet werden kann, um den `Sec-WebSocket-Protocol`-Header auf einen benutzerdefinierten Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-184">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="fe4b6-185">Der Delegat empfängt die vom Client angeforderten Werte als Eingabe und erwartet, dass der gewünschte Wert zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-185">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="fe4b6-186">Konfigurieren von Client Optionen</span><span class="sxs-lookup"><span data-stu-id="fe4b6-186">Configure client options</span></span>

<span data-ttu-id="fe4b6-187">Client Optionen können für den `HubConnectionBuilder` Typ konfiguriert werden (verfügbar in den .net-und JavaScript-Clients).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-187">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="fe4b6-188">Sie steht auch im Java-Client zur Verfügung, aber die `HttpHubConnectionBuilder`-Unterklasse enthält die Generator-Konfigurationsoptionen sowie die `HubConnection` selbst.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-188">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="fe4b6-189">Konfigurieren der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="fe4b6-189">Configure logging</span></span>

<span data-ttu-id="fe4b6-190">Die Protokollierung wird mit der `ConfigureLogging`-Methode im .NET-Client konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-190">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="fe4b6-191">Protokollierungs Anbieter und Filter können auf die gleiche Weise wie auf dem Server registriert werden.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-191">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="fe4b6-192">Weitere Informationen finden Sie in der Dokumentation zur [Protokollierung ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="fe4b6-192">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="fe4b6-193">Um Protokollierungs Anbieter zu registrieren, müssen Sie die erforderlichen Pakete installieren.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-193">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="fe4b6-194">Eine vollständige Liste finden Sie im Abschnitt [integrierte Protokollierungs Anbieter](xref:fundamentals/logging/index#built-in-logging-providers) der Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-194">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="fe4b6-195">Wenn Sie z. b. die Konsolen Protokollierung aktivieren möchten, installieren Sie das nuget-Paket `Microsoft.Extensions.Logging.Console`.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-195">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="fe4b6-196">Nennen Sie die `AddConsole`-Erweiterungsmethode:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-196">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="fe4b6-197">Im JavaScript-Client ist eine ähnliche `configureLogging` Methode vorhanden.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-197">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="fe4b6-198">Geben Sie einen `LogLevel` Wert an, der die minimale Ebene der zu erstellenden Protokollmeldungen angibt.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-198">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="fe4b6-199">Protokolle werden in das Browser Konsolenfenster geschrieben.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-199">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="fe4b6-200">Anstelle eines `LogLevel` Werts können Sie auch einen `string` Wert angeben, der einen Namen für die Protokollebene darstellt.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-200">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="fe4b6-201">Dies ist hilfreich beim Konfigurieren der signalr-Protokollierung in Umgebungen, in denen Sie keinen Zugriff auf die `LogLevel` Konstanten haben.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-201">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="fe4b6-202">In der folgenden Tabelle sind die verfügbaren Protokoll Ebenen aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-202">The following table lists the available log levels.</span></span> <span data-ttu-id="fe4b6-203">Der von Ihnen bereitgestellte Wert `configureLogging` legt den **minimalen** Protokolliergrad fest, der protokolliert werden soll.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-203">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="fe4b6-204">Nachrichten, die auf dieser Ebene protokolliert werden, **oder die nach der Tabelle aufgeführten Ebenen**werden protokolliert.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-204">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="fe4b6-205">String</span><span class="sxs-lookup"><span data-stu-id="fe4b6-205">String</span></span>                      | <span data-ttu-id="fe4b6-206">LogLevel</span><span class="sxs-lookup"><span data-stu-id="fe4b6-206">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="fe4b6-207">`info` **oder** `information`</span><span class="sxs-lookup"><span data-stu-id="fe4b6-207">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="fe4b6-208">`warn` **oder** `warning`</span><span class="sxs-lookup"><span data-stu-id="fe4b6-208">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="fe4b6-209">Um die Protokollierung vollständig zu deaktivieren, geben Sie `signalR.LogLevel.None` in der `configureLogging` Methode an.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-209">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="fe4b6-210">Weitere Informationen zur Protokollierung finden Sie in der [signalr-Diagnose Dokumentation](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-210">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="fe4b6-211">Der signalr-Java-Client verwendet die [SLF4J](https://www.slf4j.org/) -Bibliothek für die Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-211">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="fe4b6-212">Dabei handelt es sich um eine allgemeine Protokollierungs-API, die es Benutzern der Bibliothek ermöglicht, ihre eigene spezifische Protokollierungs Implementierung zu wählen, indem Sie eine bestimmte Protokollierungs Abhängigkeit einbinden.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-212">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="fe4b6-213">Der folgende Code Ausschnitt zeigt, wie `java.util.logging` mit dem signalr-Java-Client verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-213">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="fe4b6-214">Wenn Sie die Protokollierung in ihren Abhängigkeiten nicht konfigurieren, lädt SLF4J eine Standard Protokollierung ohne Vorgang mit folgender Warnmeldung:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-214">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="fe4b6-215">Dies kann sicher ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-215">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="fe4b6-216">Zulässige Transporte konfigurieren</span><span class="sxs-lookup"><span data-stu-id="fe4b6-216">Configure allowed transports</span></span>

<span data-ttu-id="fe4b6-217">Die von signalr verwendeten Transporte können im `WithUrl`-Befehl (`withUrl` in JavaScript) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-217">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="fe4b6-218">Eine bitweise OR-Angabe der Werte `HttpTransportType` kann verwendet werden, um den Client so einzuschränken, dass nur die angegebenen Transporte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-218">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="fe4b6-219">Alle Transporte sind standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-219">All transports are enabled by default.</span></span>

<span data-ttu-id="fe4b6-220">So können Sie beispielsweise den Transport von Server gesendeten Ereignissen deaktivieren, aber websockets und lange Abruf Verbindungen zulassen:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-220">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="fe4b6-221">Im JavaScript-Client werden Transporte durch Festlegen des `transport` Felds für das Options-Objekt konfiguriert, das `withUrl`bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-221">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="fe4b6-222">In dieser Version des Java-Clients ist websockets der einzige verfügbare Transport.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-222">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="fe4b6-223">Im Java-Client wird der Transport mit der `withTransport`-Methode auf dem `HttpHubConnectionBuilder`ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-223">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="fe4b6-224">Der Java-Client verwendet standardmäßig den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-224">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="fe4b6-225">Der signalr-Java-Client unterstützt noch keinen Transport Fall Back.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-225">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="fe4b6-226">Konfigurieren der bearerauthentifizierung</span><span class="sxs-lookup"><span data-stu-id="fe4b6-226">Configure bearer authentication</span></span>

<span data-ttu-id="fe4b6-227">Um Authentifizierungsdaten zusammen mit signalr-Anforderungen bereitzustellen, verwenden Sie die Option `AccessTokenProvider` (`accessTokenFactory` in JavaScript), um eine Funktion anzugeben, die das gewünschte Zugriffs Token zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-227">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="fe4b6-228">Im .NET-Client wird dieses Zugriffs Token als http-Token für die bearerauthentifizierung (unter Verwendung des `Authorization`-Headers mit dem Typ `Bearer`) übergebenen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-228">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="fe4b6-229">Im JavaScript-Client wird das Zugriffs Token als bearertoken verwendet, **außer** in einigen Fällen, in denen Browser-APIs die Fähigkeit zum Anwenden von Headern einschränken (insbesondere bei vom Server gesendeten Ereignissen und websockets-Anforderungen).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-229">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="fe4b6-230">In diesen Fällen wird das Zugriffs Token als Abfrage Zeichenfolgen-Wert `access_token`bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-230">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="fe4b6-231">Im .NET-Client kann die `AccessTokenProvider`-Option mit dem Options Delegaten in `WithUrl`angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-231">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="fe4b6-232">Im JavaScript-Client wird das Zugriffs Token konfiguriert, indem das `accessTokenFactory`-Feld für das Options-Objekt in `withUrl`festgelegt wird:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-232">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="fe4b6-233">Im signalr-Java-Client können Sie ein bearertoken für die Authentifizierung konfigurieren, indem Sie eine zugriffstokenfactory für " [httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)" bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-233">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="fe4b6-234">Verwenden Sie [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , um eine [rxjava](https://github.com/ReactiveX/RxJava) - [\<Zeichenfolge >](https://reactivex.io/documentation/single.html)bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-234">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="fe4b6-235">Beim Aufrufen von " [Single.](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)Write" können Sie Logik schreiben, um Zugriffs Token für den Client zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-235">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="fe4b6-236">Konfigurieren von Timeout-und Keep-Alive-Optionen</span><span class="sxs-lookup"><span data-stu-id="fe4b6-236">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="fe4b6-237">Zusätzliche Optionen zum Konfigurieren von Timeout und Keep-Alive-Verhalten sind im `HubConnection` Objekt selbst verfügbar:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-237">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="fe4b6-238">.NET</span><span class="sxs-lookup"><span data-stu-id="fe4b6-238">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="fe4b6-239">Option</span><span class="sxs-lookup"><span data-stu-id="fe4b6-239">Option</span></span> | <span data-ttu-id="fe4b6-240">Standardwert</span><span class="sxs-lookup"><span data-stu-id="fe4b6-240">Default value</span></span> | <span data-ttu-id="fe4b6-241">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="fe4b6-241">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="fe4b6-242">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="fe4b6-242">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="fe4b6-243">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-243">Timeout for server activity.</span></span> <span data-ttu-id="fe4b6-244">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `Closed` Ereignis aus (`onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-244">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="fe4b6-245">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-245">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="fe4b6-246">Der empfohlene Wert ist eine Zahl, die mindestens den `KeepAliveInterval` Wert des Servers verdoppelt, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-246">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="fe4b6-247">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="fe4b6-247">15 seconds</span></span> | <span data-ttu-id="fe4b6-248">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-248">Timeout for initial server handshake.</span></span> <span data-ttu-id="fe4b6-249">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `Closed` Ereignis aus (`onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-249">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="fe4b6-250">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-250">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="fe4b6-251">Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-251">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="fe4b6-252">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="fe4b6-252">15 seconds</span></span> | <span data-ttu-id="fe4b6-253">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-253">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="fe4b6-254">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-254">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="fe4b6-255">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` gesendet hat, die auf dem Server festgelegt wurde, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-255">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="fe4b6-256">Im .NET-Client werden Timeout Werte als `TimeSpan` Werte angegeben.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-256">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="fe4b6-257">JavaScript</span><span class="sxs-lookup"><span data-stu-id="fe4b6-257">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="fe4b6-258">Option</span><span class="sxs-lookup"><span data-stu-id="fe4b6-258">Option</span></span> | <span data-ttu-id="fe4b6-259">Standardwert</span><span class="sxs-lookup"><span data-stu-id="fe4b6-259">Default value</span></span> | <span data-ttu-id="fe4b6-260">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="fe4b6-260">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="fe4b6-261">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="fe4b6-261">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="fe4b6-262">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-262">Timeout for server activity.</span></span> <span data-ttu-id="fe4b6-263">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `onclose`-Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-263">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="fe4b6-264">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-264">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="fe4b6-265">Der empfohlene Wert ist eine Zahl, die mindestens den `KeepAliveInterval` Wert des Servers verdoppelt, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-265">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="fe4b6-266">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="fe4b6-266">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="fe4b6-267">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-267">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="fe4b6-268">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-268">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="fe4b6-269">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` gesendet hat, die auf dem Server festgelegt wurde, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-269">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="fe4b6-270">Java</span><span class="sxs-lookup"><span data-stu-id="fe4b6-270">Java</span></span>](#tab/java)

| <span data-ttu-id="fe4b6-271">Option</span><span class="sxs-lookup"><span data-stu-id="fe4b6-271">Option</span></span> | <span data-ttu-id="fe4b6-272">Standardwert</span><span class="sxs-lookup"><span data-stu-id="fe4b6-272">Default value</span></span> | <span data-ttu-id="fe4b6-273">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="fe4b6-273">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="fe4b6-274">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="fe4b6-274">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="fe4b6-275">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="fe4b6-275">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="fe4b6-276">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-276">Timeout for server activity.</span></span> <span data-ttu-id="fe4b6-277">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `onClose`-Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-277">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="fe4b6-278">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-278">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="fe4b6-279">Der empfohlene Wert ist eine Zahl, die mindestens den `KeepAliveInterval` Wert des Servers verdoppelt, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-279">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="fe4b6-280">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="fe4b6-280">15 seconds</span></span> | <span data-ttu-id="fe4b6-281">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-281">Timeout for initial server handshake.</span></span> <span data-ttu-id="fe4b6-282">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `onClose`-Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-282">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="fe4b6-283">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-283">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="fe4b6-284">Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-284">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="fe4b6-285">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="fe4b6-285">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="fe4b6-286">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="fe4b6-286">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="fe4b6-287">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-287">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="fe4b6-288">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-288">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="fe4b6-289">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` gesendet hat, die auf dem Server festgelegt wurde, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-289">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="fe4b6-290">Zusätzliche Optionen konfigurieren</span><span class="sxs-lookup"><span data-stu-id="fe4b6-290">Configure additional options</span></span>

<span data-ttu-id="fe4b6-291">Zusätzliche Optionen können in der `WithUrl`-Methode (`withUrl` in JavaScript) auf `HubConnectionBuilder` oder auf den verschiedenen Konfigurations-APIs auf dem `HttpHubConnectionBuilder` im Java-Client konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-291">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="fe4b6-292">.NET</span><span class="sxs-lookup"><span data-stu-id="fe4b6-292">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="fe4b6-293">.Net-Option</span><span class="sxs-lookup"><span data-stu-id="fe4b6-293">.NET Option</span></span> |  <span data-ttu-id="fe4b6-294">Standardwert</span><span class="sxs-lookup"><span data-stu-id="fe4b6-294">Default value</span></span> | <span data-ttu-id="fe4b6-295">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="fe4b6-295">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="fe4b6-296">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="fe4b6-296">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="fe4b6-297">Legen Sie diese Einstellung auf `true`, um den Aushandlungs Schritt auszulassen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-297">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="fe4b6-298">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-298">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="fe4b6-299">Diese Einstellung kann nicht aktiviert werden, wenn der Azure signalr-Dienst verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-299">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="fe4b6-300">Leer</span><span class="sxs-lookup"><span data-stu-id="fe4b6-300">Empty</span></span> | <span data-ttu-id="fe4b6-301">Eine Auflistung von TLS-Zertifikaten, die zum Authentifizieren von Anforderungen gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-301">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="fe4b6-302">Leer</span><span class="sxs-lookup"><span data-stu-id="fe4b6-302">Empty</span></span> | <span data-ttu-id="fe4b6-303">Eine Auflistung von HTTP-Cookies, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-303">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="fe4b6-304">Leer</span><span class="sxs-lookup"><span data-stu-id="fe4b6-304">Empty</span></span> | <span data-ttu-id="fe4b6-305">Anmelde Informationen, die mit jeder HTTP-Anforderung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-305">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="fe4b6-306">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="fe4b6-306">5 seconds</span></span> | <span data-ttu-id="fe4b6-307">Nur websockets.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-307">WebSockets only.</span></span> <span data-ttu-id="fe4b6-308">Die maximale Zeitspanne, die der Client nach dem Schließen des Servers wartet, um die Anforderung zum Schließen zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-308">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="fe4b6-309">Wenn der Server die Schließung innerhalb dieses Zeitraums nicht anerkennt, trennt der Client die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-309">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="fe4b6-310">Leer</span><span class="sxs-lookup"><span data-stu-id="fe4b6-310">Empty</span></span> | <span data-ttu-id="fe4b6-311">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-311">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="fe4b6-312">Ein Delegat, der verwendet werden kann, um die zum Senden von HTTP-Anforderungen verwendete `HttpMessageHandler` zu konfigurieren oder zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-312">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="fe4b6-313">Wird nicht für WebSocket-Verbindungen verwendet.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-313">Not used for WebSocket connections.</span></span> <span data-ttu-id="fe4b6-314">Dieser Delegat muss einen Wert zurückgeben, der nicht NULL ist, und erhält den Standardwert als Parameter.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-314">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="fe4b6-315">Ändern Sie die Einstellungen für diesen Standardwert, und geben Sie ihn zurück, oder geben Sie eine neue `HttpMessageHandler` Instanz zurück.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-315">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="fe4b6-316">**Wenn Sie den Handler ersetzen, stellen Sie sicher, dass die Einstellungen, die Sie beibehalten möchten, vom bereitgestellten Handler kopiert werden. Andernfalls gelten die konfigurierten Optionen (z. b. Cookies und Header) nicht für den neuen Handler.**</span><span class="sxs-lookup"><span data-stu-id="fe4b6-316">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="fe4b6-317">Ein HTTP-Proxy, der beim Senden von HTTP-Anforderungen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-317">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="fe4b6-318">Legen Sie diesen booleschen Wert fest, um die Standard Anmelde Informationen für http-und websockets-Anforderungen zu senden</span><span class="sxs-lookup"><span data-stu-id="fe4b6-318">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="fe4b6-319">Dies ermöglicht die Verwendung der Windows-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-319">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="fe4b6-320">Ein Delegat, der zum Konfigurieren zusätzlicher WebSocket-Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-320">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="fe4b6-321">Empfängt eine Instanz von [clientwebsocketoptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , die zum Konfigurieren der Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-321">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="fe4b6-322">JavaScript</span><span class="sxs-lookup"><span data-stu-id="fe4b6-322">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="fe4b6-323">JavaScript-Option</span><span class="sxs-lookup"><span data-stu-id="fe4b6-323">JavaScript Option</span></span> | <span data-ttu-id="fe4b6-324">Standardwert</span><span class="sxs-lookup"><span data-stu-id="fe4b6-324">Default Value</span></span> | <span data-ttu-id="fe4b6-325">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="fe4b6-325">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="fe4b6-326">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="fe4b6-326">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="fe4b6-327">Legen Sie diese Einstellung auf `true`, um den Aushandlungs Schritt auszulassen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-327">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="fe4b6-328">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-328">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="fe4b6-329">Diese Einstellung kann nicht aktiviert werden, wenn der Azure signalr-Dienst verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-329">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="fe4b6-330">Java</span><span class="sxs-lookup"><span data-stu-id="fe4b6-330">Java</span></span>](#tab/java)

| <span data-ttu-id="fe4b6-331">Java-Option</span><span class="sxs-lookup"><span data-stu-id="fe4b6-331">Java Option</span></span> | <span data-ttu-id="fe4b6-332">Standardwert</span><span class="sxs-lookup"><span data-stu-id="fe4b6-332">Default Value</span></span> | <span data-ttu-id="fe4b6-333">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="fe4b6-333">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="fe4b6-334">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="fe4b6-334">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="fe4b6-335">Legen Sie diese Einstellung auf `true`, um den Aushandlungs Schritt auszulassen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-335">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="fe4b6-336">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-336">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="fe4b6-337">Diese Einstellung kann nicht aktiviert werden, wenn der Azure signalr-Dienst verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-337">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="fe4b6-338">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="fe4b6-338">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="fe4b6-339">Leer</span><span class="sxs-lookup"><span data-stu-id="fe4b6-339">Empty</span></span> | <span data-ttu-id="fe4b6-340">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-340">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="fe4b6-341">Im .NET-Client können diese Optionen mit dem für `WithUrl`bereitgestellten Options Delegaten geändert werden:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-341">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="fe4b6-342">Im JavaScript-Client können diese Optionen in einem JavaScript-Objekt bereitgestellt werden, das für `withUrl`bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-342">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="fe4b6-343">Im Java-Client können diese Optionen mit den Methoden für die `HttpHubConnectionBuilder` konfiguriert werden, die vom `HubConnectionBuilder.create("HUB URL")` zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-343">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="fe4b6-344">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="fe4b6-344">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="fe4b6-345">JSON/messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="fe4b6-345">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="fe4b6-346">ASP.net Core signalr unterstützt zwei Protokolle für das Codieren von Nachrichten: [JSON](https://www.json.org/) und [messagepack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-346">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="fe4b6-347">Jedes Protokoll verfügt über Konfigurationsoptionen für die Serialisierung.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-347">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="fe4b6-348">Die JSON-Serialisierung kann mithilfe der [addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) -Erweiterungsmethode auf dem Server konfiguriert werden, die nach [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in ihrer `Startup.ConfigureServices`-Methode hinzugefügt werden kann.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-348">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="fe4b6-349">Die `AddJsonProtocol`-Methode nimmt einen Delegaten an, der ein `options` Objekt empfängt.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-349">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="fe4b6-350">Die [payloadserializersettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) -Eigenschaft für dieses Objekt ist ein JSON.net-`JsonSerializerSettings` Objekt, das zum Konfigurieren der Serialisierung von Argumenten und Rückgabe Werten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-350">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="fe4b6-351">Weitere Informationen finden Sie in der [JSON.NET-Dokumentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-351">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="fe4b6-352">Verwenden Sie z. b. den folgenden Code in `Startup.ConfigureServices`, um das Serialisierungsprogramm so zu konfigurieren, dass "PascalCase"-Eigenschaftsnamen anstelle der Standardnamen "CamelCase" verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-352">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="fe4b6-353">Im .NET-Client ist dieselbe `AddJsonProtocol` Erweiterungsmethode auf [hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)vorhanden.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-353">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="fe4b6-354">Der `Microsoft.Extensions.DependencyInjection`-Namespace muss importiert werden, um die Erweiterungsmethode aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-354">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="fe4b6-355">Zurzeit ist es nicht möglich, JSON-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-355">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="fe4b6-356">Messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="fe4b6-356">MessagePack serialization options</span></span>

<span data-ttu-id="fe4b6-357">Die messagepack-Serialisierung kann konfiguriert werden, indem ein Delegat für den [addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) -Aufruf bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-357">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="fe4b6-358">Weitere Informationen finden Sie [unter messagepack in signalr](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="fe4b6-358">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="fe4b6-359">Zurzeit ist es nicht möglich, die messagepack-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-359">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="fe4b6-360">Konfigurieren von Serveroptionen</span><span class="sxs-lookup"><span data-stu-id="fe4b6-360">Configure server options</span></span>

<span data-ttu-id="fe4b6-361">In der folgenden Tabelle werden die Optionen zum Konfigurieren von signalr Hubs beschrieben:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-361">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="fe4b6-362">Option</span><span class="sxs-lookup"><span data-stu-id="fe4b6-362">Option</span></span> | <span data-ttu-id="fe4b6-363">Standardwert</span><span class="sxs-lookup"><span data-stu-id="fe4b6-363">Default Value</span></span> | <span data-ttu-id="fe4b6-364">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="fe4b6-364">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="fe4b6-365">30 Sekunden</span><span class="sxs-lookup"><span data-stu-id="fe4b6-365">30 seconds</span></span> | <span data-ttu-id="fe4b6-366">Der Server wird vom-Client als getrennt betrachtet, wenn in diesem Intervall keine Nachricht (einschließlich Keep-Alive) empfangen wurde.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-366">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="fe4b6-367">Es kann länger dauern, bis der Client als getrennt gekennzeichnet ist, weil dies implementiert ist.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-367">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="fe4b6-368">Der empfohlene Wert ist Double der `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-368">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="fe4b6-369">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="fe4b6-369">15 seconds</span></span> | <span data-ttu-id="fe4b6-370">Wenn der Client innerhalb dieses Zeitraums keine anfängliche Hand Shake Nachricht sendet, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-370">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="fe4b6-371">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-371">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="fe4b6-372">Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-372">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="fe4b6-373">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="fe4b6-373">15 seconds</span></span> | <span data-ttu-id="fe4b6-374">Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-374">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="fe4b6-375">Wenn Sie `KeepAliveInterval`ändern, ändern Sie die Einstellung `ServerTimeout`/`serverTimeoutInMilliseconds` auf dem Client.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-375">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="fe4b6-376">Die empfohlene `ServerTimeout`/`serverTimeoutInMilliseconds` Wert ist Double der `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-376">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="fe4b6-377">Alle installierten Protokolle</span><span class="sxs-lookup"><span data-stu-id="fe4b6-377">All installed protocols</span></span> | <span data-ttu-id="fe4b6-378">Protokolle, die von diesem Hub unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-378">Protocols supported by this hub.</span></span> <span data-ttu-id="fe4b6-379">Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, aber Protokolle können aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-379">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="fe4b6-380">Wenn `true`, werden detaillierte Ausnahme Meldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-380">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="fe4b6-381">Der Standardwert ist `false`, da diese Ausnahme Meldungen vertrauliche Informationen enthalten können.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-381">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="fe4b6-382">Optionen können für alle Hubs konfiguriert werden, indem ein Options Delegat für den `AddSignalR` Aufruf in `Startup.ConfigureServices`bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-382">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="fe4b6-383">Optionen für einen einzelnen Hub überschreiben die in `AddSignalR` bereitgestellten globalen Optionen und können mithilfe <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-383">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="fe4b6-384">Erweiterte http-Konfigurationsoptionen</span><span class="sxs-lookup"><span data-stu-id="fe4b6-384">Advanced HTTP configuration options</span></span>

<span data-ttu-id="fe4b6-385">Verwenden Sie `HttpConnectionDispatcherOptions`, um erweiterte Einstellungen im Zusammenhang mit Transporte und der Speicherpuffer Verwaltung zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-385">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="fe4b6-386">Diese Optionen werden durch Übergeben eines Delegaten an [maphub\<t->](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-386">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="fe4b6-387">In der folgenden Tabelle werden die Optionen zum Konfigurieren der erweiterten http-Optionen von ASP.net Core signalr beschrieben:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-387">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="fe4b6-388">Option</span><span class="sxs-lookup"><span data-stu-id="fe4b6-388">Option</span></span> | <span data-ttu-id="fe4b6-389">Standardwert</span><span class="sxs-lookup"><span data-stu-id="fe4b6-389">Default Value</span></span> | <span data-ttu-id="fe4b6-390">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="fe4b6-390">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="fe4b6-391">32 KB</span><span class="sxs-lookup"><span data-stu-id="fe4b6-391">32 KB</span></span> | <span data-ttu-id="fe4b6-392">Die maximale Anzahl von Bytes, die vom Client empfangen werden, der vom Server gepuffert wird.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-392">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="fe4b6-393">Das Erhöhen dieses Werts ermöglicht dem Server, größere Nachrichten zu empfangen, kann sich jedoch negativ auf die Arbeitsspeicher Auslastung auswirken.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-393">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="fe4b6-394">Automatisch aus den `Authorize` Attributen gesammelte Daten, die auf die Hub-Klasse angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-394">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="fe4b6-395">Eine Liste von [iautorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) -Objekten, die verwendet werden, um zu bestimmen, ob ein Client für die Verbindung mit dem Hub autorisiert ist.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-395">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="fe4b6-396">32 KB</span><span class="sxs-lookup"><span data-stu-id="fe4b6-396">32 KB</span></span> | <span data-ttu-id="fe4b6-397">Die maximale Anzahl von Bytes, die von der APP gesendet werden, die der Server puffert.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-397">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="fe4b6-398">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten senden, kann sich jedoch negativ auf die Arbeitsspeicher Auslastung auswirken.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-398">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="fe4b6-399">Alle Transporte sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-399">All Transports are enabled.</span></span> | <span data-ttu-id="fe4b6-400">Ein Bitflags-Enumeration von `HttpTransportType` Werten, mit denen die Transporte eingeschränkt werden können, die ein Client für die Verbindungs Herstellung verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-400">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="fe4b6-401">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-401">See below.</span></span> | <span data-ttu-id="fe4b6-402">Zusätzliche Optionen, die für den langen Abruf Transport spezifisch sind.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-402">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="fe4b6-403">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-403">See below.</span></span> | <span data-ttu-id="fe4b6-404">Zusätzliche Optionen für den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-404">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="fe4b6-405">Der lange Abruf Transport bietet zusätzliche Optionen, die mit der `LongPolling`-Eigenschaft konfiguriert werden können:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-405">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="fe4b6-406">Option</span><span class="sxs-lookup"><span data-stu-id="fe4b6-406">Option</span></span> | <span data-ttu-id="fe4b6-407">Standardwert</span><span class="sxs-lookup"><span data-stu-id="fe4b6-407">Default Value</span></span> | <span data-ttu-id="fe4b6-408">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="fe4b6-408">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="fe4b6-409">90 Sekunden</span><span class="sxs-lookup"><span data-stu-id="fe4b6-409">90 seconds</span></span> | <span data-ttu-id="fe4b6-410">Die maximale Zeitspanne, die der Server wartet, bis eine Nachricht an den Client gesendet wird, bevor eine einzelne Abruf Anforderung beendet wird.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-410">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="fe4b6-411">Das verringern dieses Werts bewirkt, dass der Client neue Abruf Anforderungen häufiger ausgibt.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-411">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="fe4b6-412">Der WebSocket-Transport verfügt über zusätzliche Optionen, die mit der `WebSockets`-Eigenschaft konfiguriert werden können:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-412">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="fe4b6-413">Option</span><span class="sxs-lookup"><span data-stu-id="fe4b6-413">Option</span></span> | <span data-ttu-id="fe4b6-414">Standardwert</span><span class="sxs-lookup"><span data-stu-id="fe4b6-414">Default Value</span></span> | <span data-ttu-id="fe4b6-415">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="fe4b6-415">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="fe4b6-416">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="fe4b6-416">5 seconds</span></span> | <span data-ttu-id="fe4b6-417">Wenn der Client nach dem Schließen des Servers nicht innerhalb dieses Zeitraums geschlossen werden kann, wird die Verbindung beendet.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-417">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="fe4b6-418">Ein Delegat, der verwendet werden kann, um den `Sec-WebSocket-Protocol`-Header auf einen benutzerdefinierten Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-418">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="fe4b6-419">Der Delegat empfängt die vom Client angeforderten Werte als Eingabe und erwartet, dass der gewünschte Wert zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-419">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="fe4b6-420">Konfigurieren von Client Optionen</span><span class="sxs-lookup"><span data-stu-id="fe4b6-420">Configure client options</span></span>

<span data-ttu-id="fe4b6-421">Client Optionen können für den `HubConnectionBuilder` Typ konfiguriert werden (verfügbar in den .net-und JavaScript-Clients).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-421">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="fe4b6-422">Sie steht auch im Java-Client zur Verfügung, aber die `HttpHubConnectionBuilder`-Unterklasse enthält die Generator-Konfigurationsoptionen sowie die `HubConnection` selbst.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-422">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="fe4b6-423">Konfigurieren der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="fe4b6-423">Configure logging</span></span>

<span data-ttu-id="fe4b6-424">Die Protokollierung wird mit der `ConfigureLogging`-Methode im .NET-Client konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-424">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="fe4b6-425">Protokollierungs Anbieter und Filter können auf die gleiche Weise wie auf dem Server registriert werden.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-425">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="fe4b6-426">Weitere Informationen finden Sie in der Dokumentation zur [Protokollierung ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="fe4b6-426">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="fe4b6-427">Um Protokollierungs Anbieter zu registrieren, müssen Sie die erforderlichen Pakete installieren.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-427">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="fe4b6-428">Eine vollständige Liste finden Sie im Abschnitt [integrierte Protokollierungs Anbieter](xref:fundamentals/logging/index#built-in-logging-providers) der Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-428">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="fe4b6-429">Wenn Sie z. b. die Konsolen Protokollierung aktivieren möchten, installieren Sie das nuget-Paket `Microsoft.Extensions.Logging.Console`.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-429">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="fe4b6-430">Nennen Sie die `AddConsole`-Erweiterungsmethode:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-430">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="fe4b6-431">Im JavaScript-Client ist eine ähnliche `configureLogging` Methode vorhanden.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-431">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="fe4b6-432">Geben Sie einen `LogLevel` Wert an, der die minimale Ebene der zu erstellenden Protokollmeldungen angibt.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-432">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="fe4b6-433">Protokolle werden in das Browser Konsolenfenster geschrieben.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-433">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="fe4b6-434">Um die Protokollierung vollständig zu deaktivieren, geben Sie `signalR.LogLevel.None` in der `configureLogging` Methode an.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-434">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="fe4b6-435">Weitere Informationen zur Protokollierung finden Sie in der [signalr-Diagnose Dokumentation](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-435">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="fe4b6-436">Der signalr-Java-Client verwendet die [SLF4J](https://www.slf4j.org/) -Bibliothek für die Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-436">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="fe4b6-437">Dabei handelt es sich um eine allgemeine Protokollierungs-API, die es Benutzern der Bibliothek ermöglicht, ihre eigene spezifische Protokollierungs Implementierung zu wählen, indem Sie eine bestimmte Protokollierungs Abhängigkeit einbinden.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-437">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="fe4b6-438">Der folgende Code Ausschnitt zeigt, wie `java.util.logging` mit dem signalr-Java-Client verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-438">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="fe4b6-439">Wenn Sie die Protokollierung in ihren Abhängigkeiten nicht konfigurieren, lädt SLF4J eine Standard Protokollierung ohne Vorgang mit folgender Warnmeldung:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-439">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="fe4b6-440">Dies kann sicher ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-440">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="fe4b6-441">Zulässige Transporte konfigurieren</span><span class="sxs-lookup"><span data-stu-id="fe4b6-441">Configure allowed transports</span></span>

<span data-ttu-id="fe4b6-442">Die von signalr verwendeten Transporte können im `WithUrl`-Befehl (`withUrl` in JavaScript) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-442">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="fe4b6-443">Eine bitweise OR-Angabe der Werte `HttpTransportType` kann verwendet werden, um den Client so einzuschränken, dass nur die angegebenen Transporte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-443">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="fe4b6-444">Alle Transporte sind standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-444">All transports are enabled by default.</span></span>

<span data-ttu-id="fe4b6-445">So können Sie beispielsweise den Transport von Server gesendeten Ereignissen deaktivieren, aber websockets und lange Abruf Verbindungen zulassen:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-445">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="fe4b6-446">Im JavaScript-Client werden Transporte durch Festlegen des `transport` Felds für das Options-Objekt konfiguriert, das `withUrl`bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-446">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="fe4b6-447">In dieser Version des Java-Clients ist websockets der einzige verfügbare Transport.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-447">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="fe4b6-448">Konfigurieren der bearerauthentifizierung</span><span class="sxs-lookup"><span data-stu-id="fe4b6-448">Configure bearer authentication</span></span>

<span data-ttu-id="fe4b6-449">Um Authentifizierungsdaten zusammen mit signalr-Anforderungen bereitzustellen, verwenden Sie die Option `AccessTokenProvider` (`accessTokenFactory` in JavaScript), um eine Funktion anzugeben, die das gewünschte Zugriffs Token zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-449">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="fe4b6-450">Im .NET-Client wird dieses Zugriffs Token als http-Token für die bearerauthentifizierung (unter Verwendung des `Authorization`-Headers mit dem Typ `Bearer`) übergebenen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-450">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="fe4b6-451">Im JavaScript-Client wird das Zugriffs Token als bearertoken verwendet, **außer** in einigen Fällen, in denen Browser-APIs die Fähigkeit zum Anwenden von Headern einschränken (insbesondere bei vom Server gesendeten Ereignissen und websockets-Anforderungen).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-451">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="fe4b6-452">In diesen Fällen wird das Zugriffs Token als Abfrage Zeichenfolgen-Wert `access_token`bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-452">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="fe4b6-453">Im .NET-Client kann die `AccessTokenProvider`-Option mit dem Options Delegaten in `WithUrl`angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-453">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="fe4b6-454">Im JavaScript-Client wird das Zugriffs Token konfiguriert, indem das `accessTokenFactory`-Feld für das Options-Objekt in `withUrl`festgelegt wird:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-454">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="fe4b6-455">Im signalr-Java-Client können Sie ein bearertoken für die Authentifizierung konfigurieren, indem Sie eine zugriffstokenfactory für " [httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)" bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-455">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="fe4b6-456">Verwenden Sie [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , um eine [rxjava](https://github.com/ReactiveX/RxJava) - [\<Zeichenfolge >](https://reactivex.io/documentation/single.html)bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-456">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="fe4b6-457">Beim Aufrufen von " [Single.](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)Write" können Sie Logik schreiben, um Zugriffs Token für den Client zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-457">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="fe4b6-458">Konfigurieren von Timeout-und Keep-Alive-Optionen</span><span class="sxs-lookup"><span data-stu-id="fe4b6-458">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="fe4b6-459">Zusätzliche Optionen zum Konfigurieren von Timeout und Keep-Alive-Verhalten sind im `HubConnection` Objekt selbst verfügbar:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-459">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="fe4b6-460">.NET</span><span class="sxs-lookup"><span data-stu-id="fe4b6-460">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="fe4b6-461">Option</span><span class="sxs-lookup"><span data-stu-id="fe4b6-461">Option</span></span> | <span data-ttu-id="fe4b6-462">Standardwert</span><span class="sxs-lookup"><span data-stu-id="fe4b6-462">Default value</span></span> | <span data-ttu-id="fe4b6-463">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="fe4b6-463">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="fe4b6-464">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="fe4b6-464">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="fe4b6-465">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-465">Timeout for server activity.</span></span> <span data-ttu-id="fe4b6-466">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `Closed` Ereignis aus (`onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-466">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="fe4b6-467">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-467">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="fe4b6-468">Der empfohlene Wert ist eine Zahl, die mindestens den `KeepAliveInterval` Wert des Servers verdoppelt, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-468">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="fe4b6-469">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="fe4b6-469">15 seconds</span></span> | <span data-ttu-id="fe4b6-470">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-470">Timeout for initial server handshake.</span></span> <span data-ttu-id="fe4b6-471">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `Closed` Ereignis aus (`onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-471">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="fe4b6-472">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-472">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="fe4b6-473">Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-473">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="fe4b6-474">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="fe4b6-474">15 seconds</span></span> | <span data-ttu-id="fe4b6-475">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-475">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="fe4b6-476">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-476">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="fe4b6-477">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` gesendet hat, die auf dem Server festgelegt wurde, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-477">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="fe4b6-478">Im .NET-Client werden Timeout Werte als `TimeSpan` Werte angegeben.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-478">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="fe4b6-479">JavaScript</span><span class="sxs-lookup"><span data-stu-id="fe4b6-479">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="fe4b6-480">Option</span><span class="sxs-lookup"><span data-stu-id="fe4b6-480">Option</span></span> | <span data-ttu-id="fe4b6-481">Standardwert</span><span class="sxs-lookup"><span data-stu-id="fe4b6-481">Default value</span></span> | <span data-ttu-id="fe4b6-482">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="fe4b6-482">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="fe4b6-483">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="fe4b6-483">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="fe4b6-484">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-484">Timeout for server activity.</span></span> <span data-ttu-id="fe4b6-485">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `onclose`-Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-485">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="fe4b6-486">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-486">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="fe4b6-487">Der empfohlene Wert ist eine Zahl, die mindestens den `KeepAliveInterval` Wert des Servers verdoppelt, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-487">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="fe4b6-488">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="fe4b6-488">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="fe4b6-489">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-489">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="fe4b6-490">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-490">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="fe4b6-491">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` gesendet hat, die auf dem Server festgelegt wurde, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-491">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="fe4b6-492">Java</span><span class="sxs-lookup"><span data-stu-id="fe4b6-492">Java</span></span>](#tab/java)

| <span data-ttu-id="fe4b6-493">Option</span><span class="sxs-lookup"><span data-stu-id="fe4b6-493">Option</span></span> | <span data-ttu-id="fe4b6-494">Standardwert</span><span class="sxs-lookup"><span data-stu-id="fe4b6-494">Default value</span></span> | <span data-ttu-id="fe4b6-495">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="fe4b6-495">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="fe4b6-496">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="fe4b6-496">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="fe4b6-497">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="fe4b6-497">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="fe4b6-498">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-498">Timeout for server activity.</span></span> <span data-ttu-id="fe4b6-499">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `onClose`-Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-499">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="fe4b6-500">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-500">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="fe4b6-501">Der empfohlene Wert ist eine Zahl, die mindestens den `KeepAliveInterval` Wert des Servers verdoppelt, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-501">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="fe4b6-502">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="fe4b6-502">15 seconds</span></span> | <span data-ttu-id="fe4b6-503">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-503">Timeout for initial server handshake.</span></span> <span data-ttu-id="fe4b6-504">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `onClose`-Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-504">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="fe4b6-505">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-505">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="fe4b6-506">Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-506">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="fe4b6-507">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="fe4b6-507">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="fe4b6-508">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="fe4b6-508">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="fe4b6-509">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-509">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="fe4b6-510">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-510">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="fe4b6-511">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` gesendet hat, die auf dem Server festgelegt wurde, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-511">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="fe4b6-512">Zusätzliche Optionen konfigurieren</span><span class="sxs-lookup"><span data-stu-id="fe4b6-512">Configure additional options</span></span>

<span data-ttu-id="fe4b6-513">Zusätzliche Optionen können in der `WithUrl`-Methode (`withUrl` in JavaScript) auf `HubConnectionBuilder` oder auf den verschiedenen Konfigurations-APIs auf dem `HttpHubConnectionBuilder` im Java-Client konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-513">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="fe4b6-514">.NET</span><span class="sxs-lookup"><span data-stu-id="fe4b6-514">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="fe4b6-515">.Net-Option</span><span class="sxs-lookup"><span data-stu-id="fe4b6-515">.NET Option</span></span> |  <span data-ttu-id="fe4b6-516">Standardwert</span><span class="sxs-lookup"><span data-stu-id="fe4b6-516">Default value</span></span> | <span data-ttu-id="fe4b6-517">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="fe4b6-517">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="fe4b6-518">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="fe4b6-518">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="fe4b6-519">Legen Sie diese Einstellung auf `true`, um den Aushandlungs Schritt auszulassen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-519">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="fe4b6-520">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-520">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="fe4b6-521">Diese Einstellung kann nicht aktiviert werden, wenn der Azure signalr-Dienst verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-521">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="fe4b6-522">Leer</span><span class="sxs-lookup"><span data-stu-id="fe4b6-522">Empty</span></span> | <span data-ttu-id="fe4b6-523">Eine Auflistung von TLS-Zertifikaten, die zum Authentifizieren von Anforderungen gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-523">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="fe4b6-524">Leer</span><span class="sxs-lookup"><span data-stu-id="fe4b6-524">Empty</span></span> | <span data-ttu-id="fe4b6-525">Eine Auflistung von HTTP-Cookies, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-525">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="fe4b6-526">Leer</span><span class="sxs-lookup"><span data-stu-id="fe4b6-526">Empty</span></span> | <span data-ttu-id="fe4b6-527">Anmelde Informationen, die mit jeder HTTP-Anforderung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-527">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="fe4b6-528">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="fe4b6-528">5 seconds</span></span> | <span data-ttu-id="fe4b6-529">Nur websockets.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-529">WebSockets only.</span></span> <span data-ttu-id="fe4b6-530">Die maximale Zeitspanne, die der Client nach dem Schließen des Servers wartet, um die Anforderung zum Schließen zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-530">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="fe4b6-531">Wenn der Server die Schließung innerhalb dieses Zeitraums nicht anerkennt, trennt der Client die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-531">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="fe4b6-532">Leer</span><span class="sxs-lookup"><span data-stu-id="fe4b6-532">Empty</span></span> | <span data-ttu-id="fe4b6-533">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-533">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="fe4b6-534">Ein Delegat, der verwendet werden kann, um die zum Senden von HTTP-Anforderungen verwendete `HttpMessageHandler` zu konfigurieren oder zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-534">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="fe4b6-535">Wird nicht für WebSocket-Verbindungen verwendet.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-535">Not used for WebSocket connections.</span></span> <span data-ttu-id="fe4b6-536">Dieser Delegat muss einen Wert zurückgeben, der nicht NULL ist, und erhält den Standardwert als Parameter.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-536">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="fe4b6-537">Ändern Sie die Einstellungen für diesen Standardwert, und geben Sie ihn zurück, oder geben Sie eine neue `HttpMessageHandler` Instanz zurück.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-537">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="fe4b6-538">**Wenn Sie den Handler ersetzen, stellen Sie sicher, dass die Einstellungen, die Sie beibehalten möchten, vom bereitgestellten Handler kopiert werden. Andernfalls gelten die konfigurierten Optionen (z. b. Cookies und Header) nicht für den neuen Handler.**</span><span class="sxs-lookup"><span data-stu-id="fe4b6-538">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="fe4b6-539">Ein HTTP-Proxy, der beim Senden von HTTP-Anforderungen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-539">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="fe4b6-540">Legen Sie diesen booleschen Wert fest, um die Standard Anmelde Informationen für http-und websockets-Anforderungen zu senden</span><span class="sxs-lookup"><span data-stu-id="fe4b6-540">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="fe4b6-541">Dies ermöglicht die Verwendung der Windows-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-541">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="fe4b6-542">Ein Delegat, der zum Konfigurieren zusätzlicher WebSocket-Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-542">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="fe4b6-543">Empfängt eine Instanz von [clientwebsocketoptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , die zum Konfigurieren der Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-543">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="fe4b6-544">JavaScript</span><span class="sxs-lookup"><span data-stu-id="fe4b6-544">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="fe4b6-545">JavaScript-Option</span><span class="sxs-lookup"><span data-stu-id="fe4b6-545">JavaScript Option</span></span> | <span data-ttu-id="fe4b6-546">Standardwert</span><span class="sxs-lookup"><span data-stu-id="fe4b6-546">Default Value</span></span> | <span data-ttu-id="fe4b6-547">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="fe4b6-547">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="fe4b6-548">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="fe4b6-548">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="fe4b6-549">Legen Sie diese Einstellung auf `true`, um den Aushandlungs Schritt auszulassen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-549">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="fe4b6-550">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-550">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="fe4b6-551">Diese Einstellung kann nicht aktiviert werden, wenn der Azure signalr-Dienst verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-551">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="fe4b6-552">Java</span><span class="sxs-lookup"><span data-stu-id="fe4b6-552">Java</span></span>](#tab/java)

| <span data-ttu-id="fe4b6-553">Java-Option</span><span class="sxs-lookup"><span data-stu-id="fe4b6-553">Java Option</span></span> | <span data-ttu-id="fe4b6-554">Standardwert</span><span class="sxs-lookup"><span data-stu-id="fe4b6-554">Default Value</span></span> | <span data-ttu-id="fe4b6-555">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="fe4b6-555">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="fe4b6-556">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="fe4b6-556">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="fe4b6-557">Legen Sie diese Einstellung auf `true`, um den Aushandlungs Schritt auszulassen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-557">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="fe4b6-558">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-558">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="fe4b6-559">Diese Einstellung kann nicht aktiviert werden, wenn der Azure signalr-Dienst verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-559">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="fe4b6-560">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="fe4b6-560">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="fe4b6-561">Leer</span><span class="sxs-lookup"><span data-stu-id="fe4b6-561">Empty</span></span> | <span data-ttu-id="fe4b6-562">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-562">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="fe4b6-563">Im .NET-Client können diese Optionen mit dem für `WithUrl`bereitgestellten Options Delegaten geändert werden:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-563">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="fe4b6-564">Im JavaScript-Client können diese Optionen in einem JavaScript-Objekt bereitgestellt werden, das für `withUrl`bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-564">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="fe4b6-565">Im Java-Client können diese Optionen mit den Methoden für die `HttpHubConnectionBuilder` konfiguriert werden, die vom `HubConnectionBuilder.create("HUB URL")` zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-565">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="fe4b6-566">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="fe4b6-566">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="fe4b6-567">JSON/messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="fe4b6-567">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="fe4b6-568">ASP.net Core signalr unterstützt zwei Protokolle für das Codieren von Nachrichten: [JSON](https://www.json.org/) und [messagepack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-568">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="fe4b6-569">Jedes Protokoll verfügt über Konfigurationsoptionen für die Serialisierung.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-569">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="fe4b6-570">Die JSON-Serialisierung kann mithilfe der [addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) -Erweiterungsmethode auf dem Server konfiguriert werden, die nach [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in ihrer `Startup.ConfigureServices`-Methode hinzugefügt werden kann.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-570">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="fe4b6-571">Die `AddJsonProtocol`-Methode nimmt einen Delegaten an, der ein `options` Objekt empfängt.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-571">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="fe4b6-572">Die [payloadserializersettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) -Eigenschaft für dieses Objekt ist ein JSON.net-`JsonSerializerSettings` Objekt, das zum Konfigurieren der Serialisierung von Argumenten und Rückgabe Werten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-572">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="fe4b6-573">Weitere Informationen finden Sie in der [JSON.NET-Dokumentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-573">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="fe4b6-574">Verwenden Sie z. b. den folgenden Code in `Startup.ConfigureServices`, um das Serialisierungsprogramm so zu konfigurieren, dass "PascalCase"-Eigenschaftsnamen anstelle der Standardnamen "CamelCase" verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-574">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="fe4b6-575">Im .NET-Client ist dieselbe `AddJsonProtocol` Erweiterungsmethode auf [hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)vorhanden.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-575">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="fe4b6-576">Der `Microsoft.Extensions.DependencyInjection`-Namespace muss importiert werden, um die Erweiterungsmethode aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-576">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="fe4b6-577">Zurzeit ist es nicht möglich, JSON-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-577">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="fe4b6-578">Messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="fe4b6-578">MessagePack serialization options</span></span>

<span data-ttu-id="fe4b6-579">Die messagepack-Serialisierung kann konfiguriert werden, indem ein Delegat für den [addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) -Aufruf bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-579">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="fe4b6-580">Weitere Informationen finden Sie [unter messagepack in signalr](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="fe4b6-580">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="fe4b6-581">Zurzeit ist es nicht möglich, die messagepack-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-581">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="fe4b6-582">Konfigurieren von Serveroptionen</span><span class="sxs-lookup"><span data-stu-id="fe4b6-582">Configure server options</span></span>

<span data-ttu-id="fe4b6-583">In der folgenden Tabelle werden die Optionen zum Konfigurieren von signalr Hubs beschrieben:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-583">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="fe4b6-584">Option</span><span class="sxs-lookup"><span data-stu-id="fe4b6-584">Option</span></span> | <span data-ttu-id="fe4b6-585">Standardwert</span><span class="sxs-lookup"><span data-stu-id="fe4b6-585">Default Value</span></span> | <span data-ttu-id="fe4b6-586">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="fe4b6-586">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="fe4b6-587">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="fe4b6-587">15 seconds</span></span> | <span data-ttu-id="fe4b6-588">Wenn der Client innerhalb dieses Zeitraums keine anfängliche Hand Shake Nachricht sendet, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-588">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="fe4b6-589">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-589">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="fe4b6-590">Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-590">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="fe4b6-591">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="fe4b6-591">15 seconds</span></span> | <span data-ttu-id="fe4b6-592">Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-592">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="fe4b6-593">Wenn Sie `KeepAliveInterval`ändern, ändern Sie die Einstellung `ServerTimeout`/`serverTimeoutInMilliseconds` auf dem Client.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-593">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="fe4b6-594">Die empfohlene `ServerTimeout`/`serverTimeoutInMilliseconds` Wert ist Double der `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-594">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="fe4b6-595">Alle installierten Protokolle</span><span class="sxs-lookup"><span data-stu-id="fe4b6-595">All installed protocols</span></span> | <span data-ttu-id="fe4b6-596">Protokolle, die von diesem Hub unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-596">Protocols supported by this hub.</span></span> <span data-ttu-id="fe4b6-597">Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, aber Protokolle können aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-597">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="fe4b6-598">Wenn `true`, werden detaillierte Ausnahme Meldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-598">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="fe4b6-599">Der Standardwert ist `false`, da diese Ausnahme Meldungen vertrauliche Informationen enthalten können.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-599">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="fe4b6-600">Optionen können für alle Hubs konfiguriert werden, indem ein Options Delegat für den `AddSignalR` Aufruf in `Startup.ConfigureServices`bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-600">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="fe4b6-601">Optionen für einen einzelnen Hub überschreiben die in `AddSignalR` bereitgestellten globalen Optionen und können mithilfe <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-601">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="fe4b6-602">Erweiterte http-Konfigurationsoptionen</span><span class="sxs-lookup"><span data-stu-id="fe4b6-602">Advanced HTTP configuration options</span></span>

<span data-ttu-id="fe4b6-603">Verwenden Sie `HttpConnectionDispatcherOptions`, um erweiterte Einstellungen im Zusammenhang mit Transporte und der Speicherpuffer Verwaltung zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-603">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="fe4b6-604">Diese Optionen werden durch Übergeben eines Delegaten an [maphub\<t->](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-604">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="fe4b6-605">In der folgenden Tabelle werden die Optionen zum Konfigurieren der erweiterten http-Optionen von ASP.net Core signalr beschrieben:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-605">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="fe4b6-606">Option</span><span class="sxs-lookup"><span data-stu-id="fe4b6-606">Option</span></span> | <span data-ttu-id="fe4b6-607">Standardwert</span><span class="sxs-lookup"><span data-stu-id="fe4b6-607">Default Value</span></span> | <span data-ttu-id="fe4b6-608">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="fe4b6-608">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="fe4b6-609">32 KB</span><span class="sxs-lookup"><span data-stu-id="fe4b6-609">32 KB</span></span> | <span data-ttu-id="fe4b6-610">Die maximale Anzahl von Bytes, die vom Client empfangen werden, der vom Server gepuffert wird.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-610">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="fe4b6-611">Das Erhöhen dieses Werts ermöglicht dem Server, größere Nachrichten zu empfangen, kann sich jedoch negativ auf die Arbeitsspeicher Auslastung auswirken.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-611">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="fe4b6-612">Automatisch aus den `Authorize` Attributen gesammelte Daten, die auf die Hub-Klasse angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-612">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="fe4b6-613">Eine Liste von [iautorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) -Objekten, die verwendet werden, um zu bestimmen, ob ein Client für die Verbindung mit dem Hub autorisiert ist.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-613">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="fe4b6-614">32 KB</span><span class="sxs-lookup"><span data-stu-id="fe4b6-614">32 KB</span></span> | <span data-ttu-id="fe4b6-615">Die maximale Anzahl von Bytes, die von der APP gesendet werden, die der Server puffert.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-615">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="fe4b6-616">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten senden, kann sich jedoch negativ auf die Arbeitsspeicher Auslastung auswirken.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-616">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="fe4b6-617">Alle Transporte sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-617">All Transports are enabled.</span></span> | <span data-ttu-id="fe4b6-618">Ein Bitflags-Enumeration von `HttpTransportType` Werten, mit denen die Transporte eingeschränkt werden können, die ein Client für die Verbindungs Herstellung verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-618">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="fe4b6-619">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-619">See below.</span></span> | <span data-ttu-id="fe4b6-620">Zusätzliche Optionen, die für den langen Abruf Transport spezifisch sind.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-620">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="fe4b6-621">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-621">See below.</span></span> | <span data-ttu-id="fe4b6-622">Zusätzliche Optionen für den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-622">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="fe4b6-623">Der lange Abruf Transport bietet zusätzliche Optionen, die mit der `LongPolling`-Eigenschaft konfiguriert werden können:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-623">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="fe4b6-624">Option</span><span class="sxs-lookup"><span data-stu-id="fe4b6-624">Option</span></span> | <span data-ttu-id="fe4b6-625">Standardwert</span><span class="sxs-lookup"><span data-stu-id="fe4b6-625">Default Value</span></span> | <span data-ttu-id="fe4b6-626">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="fe4b6-626">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="fe4b6-627">90 Sekunden</span><span class="sxs-lookup"><span data-stu-id="fe4b6-627">90 seconds</span></span> | <span data-ttu-id="fe4b6-628">Die maximale Zeitspanne, die der Server wartet, bis eine Nachricht an den Client gesendet wird, bevor eine einzelne Abruf Anforderung beendet wird.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-628">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="fe4b6-629">Das verringern dieses Werts bewirkt, dass der Client neue Abruf Anforderungen häufiger ausgibt.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-629">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="fe4b6-630">Der WebSocket-Transport verfügt über zusätzliche Optionen, die mit der `WebSockets`-Eigenschaft konfiguriert werden können:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-630">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="fe4b6-631">Option</span><span class="sxs-lookup"><span data-stu-id="fe4b6-631">Option</span></span> | <span data-ttu-id="fe4b6-632">Standardwert</span><span class="sxs-lookup"><span data-stu-id="fe4b6-632">Default Value</span></span> | <span data-ttu-id="fe4b6-633">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="fe4b6-633">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="fe4b6-634">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="fe4b6-634">5 seconds</span></span> | <span data-ttu-id="fe4b6-635">Wenn der Client nach dem Schließen des Servers nicht innerhalb dieses Zeitraums geschlossen werden kann, wird die Verbindung beendet.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-635">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="fe4b6-636">Ein Delegat, der verwendet werden kann, um den `Sec-WebSocket-Protocol`-Header auf einen benutzerdefinierten Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-636">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="fe4b6-637">Der Delegat empfängt die vom Client angeforderten Werte als Eingabe und erwartet, dass der gewünschte Wert zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-637">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="fe4b6-638">Konfigurieren von Client Optionen</span><span class="sxs-lookup"><span data-stu-id="fe4b6-638">Configure client options</span></span>

<span data-ttu-id="fe4b6-639">Client Optionen können für den `HubConnectionBuilder` Typ konfiguriert werden (verfügbar in den .net-und JavaScript-Clients).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-639">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="fe4b6-640">Sie steht auch im Java-Client zur Verfügung, aber die `HttpHubConnectionBuilder`-Unterklasse enthält die Generator-Konfigurationsoptionen sowie die `HubConnection` selbst.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-640">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="fe4b6-641">Konfigurieren der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="fe4b6-641">Configure logging</span></span>

<span data-ttu-id="fe4b6-642">Die Protokollierung wird mit der `ConfigureLogging`-Methode im .NET-Client konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-642">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="fe4b6-643">Protokollierungs Anbieter und Filter können auf die gleiche Weise wie auf dem Server registriert werden.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-643">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="fe4b6-644">Weitere Informationen finden Sie in der Dokumentation zur [Protokollierung ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="fe4b6-644">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="fe4b6-645">Um Protokollierungs Anbieter zu registrieren, müssen Sie die erforderlichen Pakete installieren.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-645">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="fe4b6-646">Eine vollständige Liste finden Sie im Abschnitt [integrierte Protokollierungs Anbieter](xref:fundamentals/logging/index#built-in-logging-providers) der Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-646">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="fe4b6-647">Wenn Sie z. b. die Konsolen Protokollierung aktivieren möchten, installieren Sie das nuget-Paket `Microsoft.Extensions.Logging.Console`.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-647">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="fe4b6-648">Nennen Sie die `AddConsole`-Erweiterungsmethode:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-648">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="fe4b6-649">Im JavaScript-Client ist eine ähnliche `configureLogging` Methode vorhanden.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-649">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="fe4b6-650">Geben Sie einen `LogLevel` Wert an, der die minimale Ebene der zu erstellenden Protokollmeldungen angibt.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-650">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="fe4b6-651">Protokolle werden in das Browser Konsolenfenster geschrieben.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-651">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="fe4b6-652">Um die Protokollierung vollständig zu deaktivieren, geben Sie `signalR.LogLevel.None` in der `configureLogging` Methode an.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-652">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="fe4b6-653">Weitere Informationen zur Protokollierung finden Sie in der [signalr-Diagnose Dokumentation](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-653">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="fe4b6-654">Der signalr-Java-Client verwendet die [SLF4J](https://www.slf4j.org/) -Bibliothek für die Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-654">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="fe4b6-655">Dabei handelt es sich um eine allgemeine Protokollierungs-API, die es Benutzern der Bibliothek ermöglicht, ihre eigene spezifische Protokollierungs Implementierung zu wählen, indem Sie eine bestimmte Protokollierungs Abhängigkeit einbinden.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-655">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="fe4b6-656">Der folgende Code Ausschnitt zeigt, wie `java.util.logging` mit dem signalr-Java-Client verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-656">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="fe4b6-657">Wenn Sie die Protokollierung in ihren Abhängigkeiten nicht konfigurieren, lädt SLF4J eine Standard Protokollierung ohne Vorgang mit folgender Warnmeldung:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-657">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="fe4b6-658">Dies kann sicher ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-658">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="fe4b6-659">Zulässige Transporte konfigurieren</span><span class="sxs-lookup"><span data-stu-id="fe4b6-659">Configure allowed transports</span></span>

<span data-ttu-id="fe4b6-660">Die von signalr verwendeten Transporte können im `WithUrl`-Befehl (`withUrl` in JavaScript) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-660">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="fe4b6-661">Eine bitweise OR-Angabe der Werte `HttpTransportType` kann verwendet werden, um den Client so einzuschränken, dass nur die angegebenen Transporte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-661">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="fe4b6-662">Alle Transporte sind standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-662">All transports are enabled by default.</span></span>

<span data-ttu-id="fe4b6-663">So können Sie beispielsweise den Transport von Server gesendeten Ereignissen deaktivieren, aber websockets und lange Abruf Verbindungen zulassen:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-663">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="fe4b6-664">Im JavaScript-Client werden Transporte durch Festlegen des `transport` Felds für das Options-Objekt konfiguriert, das `withUrl`bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-664">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="fe4b6-665">Konfigurieren der bearerauthentifizierung</span><span class="sxs-lookup"><span data-stu-id="fe4b6-665">Configure bearer authentication</span></span>

<span data-ttu-id="fe4b6-666">Um Authentifizierungsdaten zusammen mit signalr-Anforderungen bereitzustellen, verwenden Sie die Option `AccessTokenProvider` (`accessTokenFactory` in JavaScript), um eine Funktion anzugeben, die das gewünschte Zugriffs Token zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-666">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="fe4b6-667">Im .NET-Client wird dieses Zugriffs Token als http-Token für die bearerauthentifizierung (unter Verwendung des `Authorization`-Headers mit dem Typ `Bearer`) übergebenen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-667">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="fe4b6-668">Im JavaScript-Client wird das Zugriffs Token als bearertoken verwendet, **außer** in einigen Fällen, in denen Browser-APIs die Fähigkeit zum Anwenden von Headern einschränken (insbesondere bei vom Server gesendeten Ereignissen und websockets-Anforderungen).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-668">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="fe4b6-669">In diesen Fällen wird das Zugriffs Token als Abfrage Zeichenfolgen-Wert `access_token`bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-669">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="fe4b6-670">Im .NET-Client kann die `AccessTokenProvider`-Option mit dem Options Delegaten in `WithUrl`angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-670">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="fe4b6-671">Im JavaScript-Client wird das Zugriffs Token konfiguriert, indem das `accessTokenFactory`-Feld für das Options-Objekt in `withUrl`festgelegt wird:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-671">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="fe4b6-672">Im signalr-Java-Client können Sie ein bearertoken für die Authentifizierung konfigurieren, indem Sie eine zugriffstokenfactory für " [httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)" bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-672">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="fe4b6-673">Verwenden Sie [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , um eine [rxjava](https://github.com/ReactiveX/RxJava) - [\<Zeichenfolge >](https://reactivex.io/documentation/single.html)bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-673">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="fe4b6-674">Beim Aufrufen von " [Single.](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)Write" können Sie Logik schreiben, um Zugriffs Token für den Client zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-674">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="fe4b6-675">Konfigurieren von Timeout-und Keep-Alive-Optionen</span><span class="sxs-lookup"><span data-stu-id="fe4b6-675">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="fe4b6-676">Zusätzliche Optionen zum Konfigurieren von Timeout und Keep-Alive-Verhalten sind im `HubConnection` Objekt selbst verfügbar:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-676">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="fe4b6-677">.NET</span><span class="sxs-lookup"><span data-stu-id="fe4b6-677">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="fe4b6-678">Option</span><span class="sxs-lookup"><span data-stu-id="fe4b6-678">Option</span></span> | <span data-ttu-id="fe4b6-679">Standardwert</span><span class="sxs-lookup"><span data-stu-id="fe4b6-679">Default value</span></span> | <span data-ttu-id="fe4b6-680">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="fe4b6-680">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="fe4b6-681">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="fe4b6-681">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="fe4b6-682">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-682">Timeout for server activity.</span></span> <span data-ttu-id="fe4b6-683">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `Closed` Ereignis aus (`onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-683">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="fe4b6-684">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-684">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="fe4b6-685">Der empfohlene Wert ist eine Zahl, die mindestens den `KeepAliveInterval` Wert des Servers verdoppelt, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-685">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="fe4b6-686">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="fe4b6-686">15 seconds</span></span> | <span data-ttu-id="fe4b6-687">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-687">Timeout for initial server handshake.</span></span> <span data-ttu-id="fe4b6-688">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `Closed` Ereignis aus (`onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-688">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="fe4b6-689">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-689">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="fe4b6-690">Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-690">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="fe4b6-691">Im .NET-Client werden Timeout Werte als `TimeSpan` Werte angegeben.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-691">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="fe4b6-692">JavaScript</span><span class="sxs-lookup"><span data-stu-id="fe4b6-692">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="fe4b6-693">Option</span><span class="sxs-lookup"><span data-stu-id="fe4b6-693">Option</span></span> | <span data-ttu-id="fe4b6-694">Standardwert</span><span class="sxs-lookup"><span data-stu-id="fe4b6-694">Default value</span></span> | <span data-ttu-id="fe4b6-695">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="fe4b6-695">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="fe4b6-696">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="fe4b6-696">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="fe4b6-697">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-697">Timeout for server activity.</span></span> <span data-ttu-id="fe4b6-698">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `onclose`-Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-698">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="fe4b6-699">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-699">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="fe4b6-700">Der empfohlene Wert ist eine Zahl, die mindestens den `KeepAliveInterval` Wert des Servers verdoppelt, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-700">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="fe4b6-701">Java</span><span class="sxs-lookup"><span data-stu-id="fe4b6-701">Java</span></span>](#tab/java)

| <span data-ttu-id="fe4b6-702">Option</span><span class="sxs-lookup"><span data-stu-id="fe4b6-702">Option</span></span> | <span data-ttu-id="fe4b6-703">Standardwert</span><span class="sxs-lookup"><span data-stu-id="fe4b6-703">Default value</span></span> | <span data-ttu-id="fe4b6-704">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="fe4b6-704">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="fe4b6-705">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="fe4b6-705">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="fe4b6-706">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="fe4b6-706">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="fe4b6-707">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-707">Timeout for server activity.</span></span> <span data-ttu-id="fe4b6-708">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `onClose`-Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-708">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="fe4b6-709">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-709">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="fe4b6-710">Der empfohlene Wert ist eine Zahl, die mindestens dem `KeepAliveInterval` Wert des Servers entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-710">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="fe4b6-711">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="fe4b6-711">15 seconds</span></span> | <span data-ttu-id="fe4b6-712">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-712">Timeout for initial server handshake.</span></span> <span data-ttu-id="fe4b6-713">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `onClose`-Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-713">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="fe4b6-714">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-714">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="fe4b6-715">Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="fe4b6-715">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="fe4b6-716">Zusätzliche Optionen konfigurieren</span><span class="sxs-lookup"><span data-stu-id="fe4b6-716">Configure additional options</span></span>

<span data-ttu-id="fe4b6-717">Zusätzliche Optionen können in der `WithUrl`-Methode (`withUrl` in JavaScript) auf `HubConnectionBuilder` oder auf den verschiedenen Konfigurations-APIs auf dem `HttpHubConnectionBuilder` im Java-Client konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-717">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="fe4b6-718">.NET</span><span class="sxs-lookup"><span data-stu-id="fe4b6-718">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="fe4b6-719">.Net-Option</span><span class="sxs-lookup"><span data-stu-id="fe4b6-719">.NET Option</span></span> |  <span data-ttu-id="fe4b6-720">Standardwert</span><span class="sxs-lookup"><span data-stu-id="fe4b6-720">Default value</span></span> | <span data-ttu-id="fe4b6-721">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="fe4b6-721">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="fe4b6-722">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="fe4b6-722">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="fe4b6-723">Legen Sie diese Einstellung auf `true`, um den Aushandlungs Schritt auszulassen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-723">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="fe4b6-724">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-724">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="fe4b6-725">Diese Einstellung kann nicht aktiviert werden, wenn der Azure signalr-Dienst verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-725">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="fe4b6-726">Leer</span><span class="sxs-lookup"><span data-stu-id="fe4b6-726">Empty</span></span> | <span data-ttu-id="fe4b6-727">Eine Auflistung von TLS-Zertifikaten, die zum Authentifizieren von Anforderungen gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-727">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="fe4b6-728">Leer</span><span class="sxs-lookup"><span data-stu-id="fe4b6-728">Empty</span></span> | <span data-ttu-id="fe4b6-729">Eine Auflistung von HTTP-Cookies, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-729">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="fe4b6-730">Leer</span><span class="sxs-lookup"><span data-stu-id="fe4b6-730">Empty</span></span> | <span data-ttu-id="fe4b6-731">Anmelde Informationen, die mit jeder HTTP-Anforderung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-731">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="fe4b6-732">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="fe4b6-732">5 seconds</span></span> | <span data-ttu-id="fe4b6-733">Nur websockets.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-733">WebSockets only.</span></span> <span data-ttu-id="fe4b6-734">Die maximale Zeitspanne, die der Client nach dem Schließen des Servers wartet, um die Anforderung zum Schließen zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-734">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="fe4b6-735">Wenn der Server die Schließung innerhalb dieses Zeitraums nicht anerkennt, trennt der Client die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-735">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="fe4b6-736">Leer</span><span class="sxs-lookup"><span data-stu-id="fe4b6-736">Empty</span></span> | <span data-ttu-id="fe4b6-737">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-737">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="fe4b6-738">Ein Delegat, der verwendet werden kann, um die zum Senden von HTTP-Anforderungen verwendete `HttpMessageHandler` zu konfigurieren oder zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-738">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="fe4b6-739">Wird nicht für WebSocket-Verbindungen verwendet.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-739">Not used for WebSocket connections.</span></span> <span data-ttu-id="fe4b6-740">Dieser Delegat muss einen Wert zurückgeben, der nicht NULL ist, und erhält den Standardwert als Parameter.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-740">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="fe4b6-741">Ändern Sie die Einstellungen für diesen Standardwert, und geben Sie ihn zurück, oder geben Sie eine neue `HttpMessageHandler` Instanz zurück.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-741">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="fe4b6-742">**Wenn Sie den Handler ersetzen, stellen Sie sicher, dass die Einstellungen, die Sie beibehalten möchten, vom bereitgestellten Handler kopiert werden. Andernfalls gelten die konfigurierten Optionen (z. b. Cookies und Header) nicht für den neuen Handler.**</span><span class="sxs-lookup"><span data-stu-id="fe4b6-742">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="fe4b6-743">Ein HTTP-Proxy, der beim Senden von HTTP-Anforderungen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-743">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="fe4b6-744">Legen Sie diesen booleschen Wert fest, um die Standard Anmelde Informationen für http-und websockets-Anforderungen zu senden</span><span class="sxs-lookup"><span data-stu-id="fe4b6-744">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="fe4b6-745">Dies ermöglicht die Verwendung der Windows-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-745">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="fe4b6-746">Ein Delegat, der zum Konfigurieren zusätzlicher WebSocket-Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-746">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="fe4b6-747">Empfängt eine Instanz von [clientwebsocketoptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , die zum Konfigurieren der Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-747">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="fe4b6-748">JavaScript</span><span class="sxs-lookup"><span data-stu-id="fe4b6-748">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="fe4b6-749">JavaScript-Option</span><span class="sxs-lookup"><span data-stu-id="fe4b6-749">JavaScript Option</span></span> | <span data-ttu-id="fe4b6-750">Standardwert</span><span class="sxs-lookup"><span data-stu-id="fe4b6-750">Default Value</span></span> | <span data-ttu-id="fe4b6-751">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="fe4b6-751">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="fe4b6-752">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="fe4b6-752">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="fe4b6-753">Legen Sie diese Einstellung auf `true`, um den Aushandlungs Schritt auszulassen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-753">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="fe4b6-754">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-754">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="fe4b6-755">Diese Einstellung kann nicht aktiviert werden, wenn der Azure signalr-Dienst verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-755">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="fe4b6-756">Java</span><span class="sxs-lookup"><span data-stu-id="fe4b6-756">Java</span></span>](#tab/java)

| <span data-ttu-id="fe4b6-757">Java-Option</span><span class="sxs-lookup"><span data-stu-id="fe4b6-757">Java Option</span></span> | <span data-ttu-id="fe4b6-758">Standardwert</span><span class="sxs-lookup"><span data-stu-id="fe4b6-758">Default Value</span></span> | <span data-ttu-id="fe4b6-759">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="fe4b6-759">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="fe4b6-760">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="fe4b6-760">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="fe4b6-761">Legen Sie diese Einstellung auf `true`, um den Aushandlungs Schritt auszulassen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-761">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="fe4b6-762">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-762">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="fe4b6-763">Diese Einstellung kann nicht aktiviert werden, wenn der Azure signalr-Dienst verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-763">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="fe4b6-764">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="fe4b6-764">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="fe4b6-765">Leer</span><span class="sxs-lookup"><span data-stu-id="fe4b6-765">Empty</span></span> | <span data-ttu-id="fe4b6-766">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-766">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="fe4b6-767">Im .NET-Client können diese Optionen mit dem für `WithUrl`bereitgestellten Options Delegaten geändert werden:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-767">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="fe4b6-768">Im JavaScript-Client können diese Optionen in einem JavaScript-Objekt bereitgestellt werden, das für `withUrl`bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="fe4b6-768">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="fe4b6-769">Im Java-Client können diese Optionen mit den Methoden für die `HttpHubConnectionBuilder` konfiguriert werden, die vom `HubConnectionBuilder.create("HUB URL")` zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="fe4b6-769">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="fe4b6-770">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="fe4b6-770">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
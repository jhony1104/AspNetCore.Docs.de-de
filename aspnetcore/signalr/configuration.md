---
title: ASP.net Core SignalR Konfiguration
author: bradygaster
description: Erfahren Sie, wie Sie SignalR ASP.net Core-apps konfigurieren.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 7e0cd952fd152ff6adb6e0a7c56214d70d3c7b86
ms.sourcegitcommit: f9a5069577e8f7c53f8bcec9e13e117950f4f033
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82559000"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="856bf-103">ASP.NET Core SignalR-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="856bf-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="856bf-104">JSON/messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="856bf-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="856bf-105">ASP.net Core signalr unterstützt zwei Protokolle für das Codieren von Nachrichten: [JSON](https://www.json.org/) und [messagepack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="856bf-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="856bf-106">Jedes Protokoll verfügt über Konfigurationsoptionen für die Serialisierung.</span><span class="sxs-lookup"><span data-stu-id="856bf-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="856bf-107">Die JSON-Serialisierung kann mithilfe der [addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) -Erweiterungsmethode auf dem Server konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="856bf-108">`AddJsonProtocol`kann nach [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="856bf-109">Die `AddJsonProtocol` -Methode nimmt einen Delegaten an `options` , der ein-Objekt empfängt.</span><span class="sxs-lookup"><span data-stu-id="856bf-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="856bf-110">Die [payloadserializeroptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) -Eigenschaft für dieses Objekt ist `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> ein-Objekt, das zum Konfigurieren der Serialisierung von Argumenten und Rückgabe Werten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="856bf-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="856bf-111">Weitere Informationen finden Sie in der [System. Text. JSON-Dokumentation](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="856bf-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="856bf-112">Verwenden Sie beispielsweise den folgenden Code in, um das Serialisierungsprogramm so zu konfigurieren, dass die Groß-/Kleinschreibung von Eigenschaftsnamen nicht geändert wird. verwenden `Startup.ConfigureServices`Sie dazu den folgenden Code in:</span><span class="sxs-lookup"><span data-stu-id="856bf-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="856bf-113">Im .NET-Client ist dieselbe `AddJsonProtocol` Erweiterungsmethode auf [hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)vorhanden.</span><span class="sxs-lookup"><span data-stu-id="856bf-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="856bf-114">Der `Microsoft.Extensions.DependencyInjection` Namespace muss importiert werden, um die Erweiterungsmethode aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="856bf-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="856bf-115">Zurzeit ist es nicht möglich, JSON-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="856bf-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="856bf-116">Wechseln Sie zu "newtonsoft. JSON".</span><span class="sxs-lookup"><span data-stu-id="856bf-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="856bf-117">Wenn Sie Features von `Newtonsoft.Json` benötigen, die in `System.Text.Json`nicht unterstützt werden, finden Sie weitere Informationen unter [Wechseln zu Newton Soft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="856bf-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="856bf-118">Messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="856bf-118">MessagePack serialization options</span></span>

<span data-ttu-id="856bf-119">Die messagepack-Serialisierung kann konfiguriert werden, indem ein Delegat für den [addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) -Aufruf bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="856bf-120">Weitere Informationen finden Sie [unter messagepack in signalr](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="856bf-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="856bf-121">Zurzeit ist es nicht möglich, die messagepack-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="856bf-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="856bf-122">Konfigurieren von Serveroptionen</span><span class="sxs-lookup"><span data-stu-id="856bf-122">Configure server options</span></span>

<span data-ttu-id="856bf-123">In der folgenden Tabelle werden die Optionen zum Konfigurieren von signalr Hubs beschrieben:</span><span class="sxs-lookup"><span data-stu-id="856bf-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="856bf-124">Option</span><span class="sxs-lookup"><span data-stu-id="856bf-124">Option</span></span> | <span data-ttu-id="856bf-125">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-125">Default Value</span></span> | <span data-ttu-id="856bf-126">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="856bf-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="856bf-127">30 Sekunden</span><span class="sxs-lookup"><span data-stu-id="856bf-127">30 seconds</span></span> | <span data-ttu-id="856bf-128">Der Server wird vom-Client als getrennt betrachtet, wenn in diesem Intervall keine Nachricht (einschließlich Keep-Alive) empfangen wurde.</span><span class="sxs-lookup"><span data-stu-id="856bf-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="856bf-129">Es kann länger dauern, bis der Client als getrennt gekennzeichnet ist, weil dies implementiert ist.</span><span class="sxs-lookup"><span data-stu-id="856bf-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="856bf-130">Der empfohlene Wert ist Double- `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="856bf-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="856bf-131">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="856bf-131">15 seconds</span></span> | <span data-ttu-id="856bf-132">Wenn der Client innerhalb dieses Zeitraums keine anfängliche Hand Shake Nachricht sendet, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="856bf-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="856bf-133">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="856bf-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="856bf-134">Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="856bf-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="856bf-135">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="856bf-135">15 seconds</span></span> | <span data-ttu-id="856bf-136">Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten.</span><span class="sxs-lookup"><span data-stu-id="856bf-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="856bf-137">Ändern Sie `KeepAliveInterval`die `ServerTimeout` / `serverTimeoutInMilliseconds` Einstellung auf dem Client, wenn Sie sich ändern.</span><span class="sxs-lookup"><span data-stu-id="856bf-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="856bf-138">Der empfohlene `ServerTimeout` / `serverTimeoutInMilliseconds` Wert ist Double- `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="856bf-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="856bf-139">Alle installierten Protokolle</span><span class="sxs-lookup"><span data-stu-id="856bf-139">All installed protocols</span></span> | <span data-ttu-id="856bf-140">Protokolle, die von diesem Hub unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-140">Protocols supported by this hub.</span></span> <span data-ttu-id="856bf-141">Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, aber Protokolle können aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="856bf-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="856bf-142">Wenn `true`der Wert ist, werden ausführliche Ausnahme Meldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="856bf-143">Der Standardwert `false`ist, da diese Ausnahme Meldungen vertrauliche Informationen enthalten können.</span><span class="sxs-lookup"><span data-stu-id="856bf-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="856bf-144">Die maximale Anzahl von Elementen, die für Client-uploadstreams gepuffert werden können.</span><span class="sxs-lookup"><span data-stu-id="856bf-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="856bf-145">Wenn dieser Grenzwert erreicht wird, wird die Verarbeitung von Aufrufen blockiert, bis der Server streamingelemente verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="856bf-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="856bf-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="856bf-146">32 KB</span></span> | <span data-ttu-id="856bf-147">Maximale Größe einer einzelnen eingehenden Hub-Nachricht.</span><span class="sxs-lookup"><span data-stu-id="856bf-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="856bf-148">Optionen können für alle Hubs konfiguriert werden, indem ein Options Delegat für `AddSignalR` den Aufruf `Startup.ConfigureServices`in bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="856bf-149">Optionen für einen einzelnen Hub überschreiben die in `AddSignalR` bereitgestellten globalen Optionen und können mithilfe <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>von konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="856bf-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="856bf-150">Erweiterte http-Konfigurationsoptionen</span><span class="sxs-lookup"><span data-stu-id="856bf-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="856bf-151">Verwenden `HttpConnectionDispatcherOptions` Sie, um erweiterte Einstellungen im Zusammenhang mit Transporte und der Speicherpuffer Verwaltung zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="856bf-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="856bf-152">Diese Optionen werden konfiguriert, indem ein [Delegat an\<maphub T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="856bf-153">In der folgenden Tabelle werden die Optionen zum Konfigurieren der erweiterten http-Optionen von ASP.net Core signalr beschrieben:</span><span class="sxs-lookup"><span data-stu-id="856bf-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="856bf-154">Option</span><span class="sxs-lookup"><span data-stu-id="856bf-154">Option</span></span> | <span data-ttu-id="856bf-155">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-155">Default Value</span></span> | <span data-ttu-id="856bf-156">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="856bf-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="856bf-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="856bf-157">32 KB</span></span> | <span data-ttu-id="856bf-158">Die maximale Anzahl von Bytes, die vom Client vor dem Anwenden des backdrucks vom Server gepuffert werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="856bf-159">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten schneller empfangen, ohne dass ein Rückdruck angewendet wird, der Arbeitsspeicher Verbrauch kann jedoch gesteigert werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="856bf-160">Daten, die automatisch von `Authorize` den auf die Hub-Klasse angewendeten Attributen gesammelt werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="856bf-161">Eine Liste von [iautorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) -Objekten, die verwendet werden, um zu bestimmen, ob ein Client für die Verbindung mit dem Hub autorisiert ist.</span><span class="sxs-lookup"><span data-stu-id="856bf-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="856bf-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="856bf-162">32 KB</span></span> | <span data-ttu-id="856bf-163">Die maximale Anzahl von Bytes, die von der APP gesendet werden, die der Server puffert, bevor der Rückdruck beobachtet wird</span><span class="sxs-lookup"><span data-stu-id="856bf-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="856bf-164">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten schneller Puffern, ohne dass auf den Rückstand gewartet wird, aber die Arbeitsspeicher Nutzung erhöhen kann.</span><span class="sxs-lookup"><span data-stu-id="856bf-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="856bf-165">Alle Transporte sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="856bf-165">All Transports are enabled.</span></span> | <span data-ttu-id="856bf-166">Ein Bitflags-Enumeration `HttpTransportType` von Werten, die die Transporte einschränken können, die ein Client für die Verbindungs Herstellung verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="856bf-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="856bf-167">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="856bf-167">See below.</span></span> | <span data-ttu-id="856bf-168">Zusätzliche Optionen, die für den langen Abruf Transport spezifisch sind.</span><span class="sxs-lookup"><span data-stu-id="856bf-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="856bf-169">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="856bf-169">See below.</span></span> | <span data-ttu-id="856bf-170">Zusätzliche Optionen für den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="856bf-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="856bf-171">0</span><span class="sxs-lookup"><span data-stu-id="856bf-171">0</span></span> | <span data-ttu-id="856bf-172">Geben Sie die Mindestversion des Aushandlungs Protokolls an.</span><span class="sxs-lookup"><span data-stu-id="856bf-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="856bf-173">Hiermit werden Clients auf neuere Versionen beschränkt.</span><span class="sxs-lookup"><span data-stu-id="856bf-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="856bf-174">Der lange Abruf Transport bietet zusätzliche Optionen, die mit der `LongPolling` -Eigenschaft konfiguriert werden können:</span><span class="sxs-lookup"><span data-stu-id="856bf-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="856bf-175">Option</span><span class="sxs-lookup"><span data-stu-id="856bf-175">Option</span></span> | <span data-ttu-id="856bf-176">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-176">Default Value</span></span> | <span data-ttu-id="856bf-177">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="856bf-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="856bf-178">90 Sekunden</span><span class="sxs-lookup"><span data-stu-id="856bf-178">90 seconds</span></span> | <span data-ttu-id="856bf-179">Die maximale Zeitspanne, die der Server wartet, bis eine Nachricht an den Client gesendet wird, bevor eine einzelne Abruf Anforderung beendet wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="856bf-180">Das verringern dieses Werts bewirkt, dass der Client neue Abruf Anforderungen häufiger ausgibt.</span><span class="sxs-lookup"><span data-stu-id="856bf-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="856bf-181">Der WebSocket-Transport verfügt über zusätzliche Optionen, die mit der `WebSockets` -Eigenschaft konfiguriert werden können:</span><span class="sxs-lookup"><span data-stu-id="856bf-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="856bf-182">Option</span><span class="sxs-lookup"><span data-stu-id="856bf-182">Option</span></span> | <span data-ttu-id="856bf-183">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-183">Default Value</span></span> | <span data-ttu-id="856bf-184">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="856bf-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="856bf-185">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="856bf-185">5 seconds</span></span> | <span data-ttu-id="856bf-186">Wenn der Client nach dem Schließen des Servers nicht innerhalb dieses Zeitraums geschlossen werden kann, wird die Verbindung beendet.</span><span class="sxs-lookup"><span data-stu-id="856bf-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="856bf-187">Ein Delegat, der verwendet werden kann, `Sec-WebSocket-Protocol` um den-Header auf einen benutzerdefinierten Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="856bf-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="856bf-188">Der Delegat empfängt die vom Client angeforderten Werte als Eingabe und erwartet, dass der gewünschte Wert zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="856bf-189">Konfigurieren von Clientoptionen</span><span class="sxs-lookup"><span data-stu-id="856bf-189">Configure client options</span></span>

<span data-ttu-id="856bf-190">Client Optionen können für den `HubConnectionBuilder` Typ konfiguriert werden (verfügbar in den .net-und JavaScript-Clients).</span><span class="sxs-lookup"><span data-stu-id="856bf-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="856bf-191">Sie steht auch im Java-Client zur Verfügung, aber `HttpHubConnectionBuilder` die Unterklasse enthält die Generator-Konfigurationsoptionen sowie die `HubConnection` selbst.</span><span class="sxs-lookup"><span data-stu-id="856bf-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="856bf-192">Konfigurieren der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="856bf-192">Configure logging</span></span>

<span data-ttu-id="856bf-193">Die Protokollierung wird mithilfe der `ConfigureLogging` -Methode im .NET-Client konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="856bf-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="856bf-194">Protokollierungs Anbieter und Filter können auf die gleiche Weise wie auf dem Server registriert werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="856bf-195">Weitere Informationen finden Sie in der Dokumentation zur [Protokollierung ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="856bf-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="856bf-196">Um Protokollierungs Anbieter zu registrieren, müssen Sie die erforderlichen Pakete installieren.</span><span class="sxs-lookup"><span data-stu-id="856bf-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="856bf-197">Eine vollständige Liste finden Sie im Abschnitt [integrierte Protokollierungs Anbieter](xref:fundamentals/logging/index#built-in-logging-providers) der Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="856bf-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="856bf-198">Um z. b. die Konsolen Protokollierung zu `Microsoft.Extensions.Logging.Console` aktivieren, installieren Sie das nuget-Paket.</span><span class="sxs-lookup"><span data-stu-id="856bf-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="856bf-199">Die `AddConsole` Erweiterungsmethode aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="856bf-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="856bf-200">Im JavaScript-Client ist eine ähnliche `configureLogging` Methode vorhanden.</span><span class="sxs-lookup"><span data-stu-id="856bf-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="856bf-201">Geben Sie `LogLevel` einen Wert an, der die minimale Ebene der zu erstellenden Protokollmeldungen angibt.</span><span class="sxs-lookup"><span data-stu-id="856bf-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="856bf-202">Protokolle werden in das Browser Konsolenfenster geschrieben.</span><span class="sxs-lookup"><span data-stu-id="856bf-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="856bf-203">Anstelle eines `LogLevel` Werts können Sie auch einen `string` Wert angeben, der den Namen einer Protokollebene darstellt.</span><span class="sxs-lookup"><span data-stu-id="856bf-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="856bf-204">Dies ist hilfreich beim Konfigurieren der signalr-Protokollierung in Umgebungen, in denen Sie `LogLevel` keinen Zugriff auf die Konstanten haben.</span><span class="sxs-lookup"><span data-stu-id="856bf-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="856bf-205">In der folgenden Tabelle sind die verfügbaren Protokoll Ebenen aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="856bf-205">The following table lists the available log levels.</span></span> <span data-ttu-id="856bf-206">Der von Ihnen bereitgestellte `configureLogging` Wert, der die **minimale** Protokollierung festlegt, die protokolliert werden soll.</span><span class="sxs-lookup"><span data-stu-id="856bf-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="856bf-207">Nachrichten, die auf dieser Ebene protokolliert werden, **oder die nach der Tabelle aufgeführten Ebenen**werden protokolliert.</span><span class="sxs-lookup"><span data-stu-id="856bf-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="856bf-208">String</span><span class="sxs-lookup"><span data-stu-id="856bf-208">String</span></span>                      | <span data-ttu-id="856bf-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="856bf-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="856bf-210">`info` **oder** `information`</span><span class="sxs-lookup"><span data-stu-id="856bf-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="856bf-211">`warn` **oder** `warning`</span><span class="sxs-lookup"><span data-stu-id="856bf-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="856bf-212">Um die Protokollierung vollständig `signalR.LogLevel.None` zu deaktivieren `configureLogging` , geben Sie in der Methode an</span><span class="sxs-lookup"><span data-stu-id="856bf-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="856bf-213">Weitere Informationen zur Protokollierung finden Sie in der [signalr-Diagnose Dokumentation](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="856bf-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="856bf-214">Der signalr-Java-Client verwendet die [SLF4J](https://www.slf4j.org/) -Bibliothek für die Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="856bf-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="856bf-215">Dabei handelt es sich um eine allgemeine Protokollierungs-API, die es Benutzern der Bibliothek ermöglicht, ihre eigene spezifische Protokollierungs Implementierung zu wählen, indem Sie eine bestimmte Protokollierungs Abhängigkeit einbinden.</span><span class="sxs-lookup"><span data-stu-id="856bf-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="856bf-216">Der folgende Code Ausschnitt zeigt, wie mit dem `java.util.logging` signalr-Java-Client verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="856bf-217">Wenn Sie die Protokollierung in ihren Abhängigkeiten nicht konfigurieren, lädt SLF4J eine Standard Protokollierung ohne Vorgang mit folgender Warnmeldung:</span><span class="sxs-lookup"><span data-stu-id="856bf-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="856bf-218">Dies kann sicher ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="856bf-219">Zulässige Transporte konfigurieren</span><span class="sxs-lookup"><span data-stu-id="856bf-219">Configure allowed transports</span></span>

<span data-ttu-id="856bf-220">Die von signalr verwendeten Transporte können im `WithUrl` -Befehl (`withUrl` in JavaScript) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="856bf-221">Ein bitweises OR der Werte von `HttpTransportType` kann verwendet werden, um den Client so einzuschränken, dass nur die angegebenen Transporte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="856bf-222">Alle Transporte sind standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="856bf-222">All transports are enabled by default.</span></span>

<span data-ttu-id="856bf-223">So können Sie beispielsweise den Transport von Server gesendeten Ereignissen deaktivieren, aber websockets und lange Abruf Verbindungen zulassen:</span><span class="sxs-lookup"><span data-stu-id="856bf-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="856bf-224">Im JavaScript-Client werden Transporte durch Festlegen des-Felds `transport` für das Options-Objekt konfiguriert, `withUrl`das für bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="856bf-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="856bf-225">In dieser Version des Java-Clients ist websockets der einzige verfügbare Transport.</span><span class="sxs-lookup"><span data-stu-id="856bf-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="856bf-226">Im Java-Client wird der Transport mit der `withTransport` -Methode im ausgewählt. `HttpHubConnectionBuilder`</span><span class="sxs-lookup"><span data-stu-id="856bf-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="856bf-227">Der Java-Client verwendet standardmäßig den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="856bf-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="856bf-228">Der signalr-Java-Client unterstützt noch keinen Transport Fall Back.</span><span class="sxs-lookup"><span data-stu-id="856bf-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="856bf-229">Konfigurieren der bearerauthentifizierung</span><span class="sxs-lookup"><span data-stu-id="856bf-229">Configure bearer authentication</span></span>

<span data-ttu-id="856bf-230">Um Authentifizierungsdaten zusammen mit signalr-Anforderungen bereitzustellen, `AccessTokenProvider` verwenden Sie`accessTokenFactory` die-Option (in JavaScript), um eine Funktion anzugeben, die das gewünschte Zugriffs Token zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="856bf-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="856bf-231">Im .NET-Client wird dieses Zugriffs Token als http-tokenauthentifizierungstoken (mit dem- `Authorization` Header mit dem Typ `Bearer`) an das Token "bearerauthentifizierung" geleitet.</span><span class="sxs-lookup"><span data-stu-id="856bf-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="856bf-232">Im JavaScript-Client wird das Zugriffs Token als bearertoken verwendet, **außer** in einigen Fällen, in denen Browser-APIs die Fähigkeit zum Anwenden von Headern einschränken (insbesondere bei vom Server gesendeten Ereignissen und websockets-Anforderungen).</span><span class="sxs-lookup"><span data-stu-id="856bf-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="856bf-233">In diesen Fällen wird das Zugriffs Token als Abfrage Zeichenfolgen-Wert `access_token`bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="856bf-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="856bf-234">Im .NET-Client kann die `AccessTokenProvider` Option mithilfe des Options Delegaten in `WithUrl`angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="856bf-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="856bf-235">Im JavaScript-Client wird das Zugriffs Token konfiguriert, indem das `accessTokenFactory` -Feld für das Options-Objekt `withUrl`in festgelegt wird:</span><span class="sxs-lookup"><span data-stu-id="856bf-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="856bf-236">Im signalr-Java-Client können Sie ein bearertoken für die Authentifizierung konfigurieren, indem Sie eine zugriffstokenfactory für " [httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)" bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="856bf-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="856bf-237">Verwenden Sie [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , um eine einzelne [RxJava](https://github.com/ReactiveX/RxJava) [\< ](https://reactivex.io/documentation/single.html)rxjava-Zeichenfolge>bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="856bf-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="856bf-238">Beim Aufrufen von " [Single.](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)Write" können Sie Logik schreiben, um Zugriffs Token für den Client zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="856bf-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="856bf-239">Konfigurieren von Timeout-und Keep-Alive-Optionen</span><span class="sxs-lookup"><span data-stu-id="856bf-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="856bf-240">Zusätzliche Optionen zum Konfigurieren von Timeout und Keep-Alive-Verhalten sind für `HubConnection` das Objekt selbst verfügbar:</span><span class="sxs-lookup"><span data-stu-id="856bf-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="856bf-241">.NET</span><span class="sxs-lookup"><span data-stu-id="856bf-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="856bf-242">Option</span><span class="sxs-lookup"><span data-stu-id="856bf-242">Option</span></span> | <span data-ttu-id="856bf-243">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-243">Default value</span></span> | <span data-ttu-id="856bf-244">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="856bf-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="856bf-245">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="856bf-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="856bf-246">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="856bf-246">Timeout for server activity.</span></span> <span data-ttu-id="856bf-247">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst `Closed` das Ereignis`onclose` aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="856bf-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="856bf-248">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="856bf-249">Der empfohlene Wert ist eine Zahl, die mindestens dem `KeepAliveInterval` Wert des Servers entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="856bf-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="856bf-250">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="856bf-250">15 seconds</span></span> | <span data-ttu-id="856bf-251">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="856bf-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="856bf-252">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `Closed` Ereignis aus`onclose` (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="856bf-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="856bf-253">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="856bf-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="856bf-254">Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="856bf-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="856bf-255">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="856bf-255">15 seconds</span></span> | <span data-ttu-id="856bf-256">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="856bf-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="856bf-257">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="856bf-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="856bf-258">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="856bf-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="856bf-259">Im .NET-Client werden Timeout Werte als `TimeSpan` Werte angegeben.</span><span class="sxs-lookup"><span data-stu-id="856bf-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="856bf-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="856bf-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="856bf-261">Option</span><span class="sxs-lookup"><span data-stu-id="856bf-261">Option</span></span> | <span data-ttu-id="856bf-262">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-262">Default value</span></span> | <span data-ttu-id="856bf-263">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="856bf-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="856bf-264">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="856bf-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="856bf-265">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="856bf-265">Timeout for server activity.</span></span> <span data-ttu-id="856bf-266">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst `onclose` das Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="856bf-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="856bf-267">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="856bf-268">Der empfohlene Wert ist eine Zahl, die mindestens dem `KeepAliveInterval` Wert des Servers entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="856bf-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="856bf-269">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="856bf-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="856bf-270">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="856bf-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="856bf-271">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="856bf-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="856bf-272">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="856bf-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="856bf-273">Java</span><span class="sxs-lookup"><span data-stu-id="856bf-273">Java</span></span>](#tab/java)

| <span data-ttu-id="856bf-274">Option</span><span class="sxs-lookup"><span data-stu-id="856bf-274">Option</span></span> | <span data-ttu-id="856bf-275">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-275">Default value</span></span> | <span data-ttu-id="856bf-276">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="856bf-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="856bf-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="856bf-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="856bf-278">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="856bf-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="856bf-279">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="856bf-279">Timeout for server activity.</span></span> <span data-ttu-id="856bf-280">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst `onClose` das Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="856bf-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="856bf-281">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="856bf-282">Der empfohlene Wert ist eine Zahl, die mindestens dem `KeepAliveInterval` Wert des Servers entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="856bf-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="856bf-283">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="856bf-283">15 seconds</span></span> | <span data-ttu-id="856bf-284">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="856bf-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="856bf-285">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `onClose` -Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="856bf-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="856bf-286">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="856bf-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="856bf-287">Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="856bf-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="856bf-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="856bf-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="856bf-289">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="856bf-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="856bf-290">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="856bf-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="856bf-291">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="856bf-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="856bf-292">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="856bf-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="856bf-293">Konfigurieren zusätzlicher Optionen</span><span class="sxs-lookup"><span data-stu-id="856bf-293">Configure additional options</span></span>

<span data-ttu-id="856bf-294">`WithUrl` Zusätzliche Optionen können in der-Methode (`withUrl` in JavaScript) für `HubConnectionBuilder` oder für die verschiedenen Konfigurations-APIs auf dem `HttpHubConnectionBuilder` im Java-Client konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="856bf-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="856bf-295">.NET</span><span class="sxs-lookup"><span data-stu-id="856bf-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="856bf-296">.Net-Option</span><span class="sxs-lookup"><span data-stu-id="856bf-296">.NET Option</span></span> |  <span data-ttu-id="856bf-297">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-297">Default value</span></span> | <span data-ttu-id="856bf-298">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="856bf-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="856bf-299">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="856bf-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="856bf-300">Legen Sie dies `true` auf fest, um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="856bf-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="856bf-301">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="856bf-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="856bf-302">Diese Einstellung kann nicht aktiviert werden, wenn der Azure signalr-Dienst verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="856bf-303">Leer</span><span class="sxs-lookup"><span data-stu-id="856bf-303">Empty</span></span> | <span data-ttu-id="856bf-304">Eine Auflistung von TLS-Zertifikaten, die zum Authentifizieren von Anforderungen gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="856bf-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="856bf-305">Leer</span><span class="sxs-lookup"><span data-stu-id="856bf-305">Empty</span></span> | <span data-ttu-id="856bf-306">Eine Auflistung von HTTP-Cookies, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="856bf-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="856bf-307">Leer</span><span class="sxs-lookup"><span data-stu-id="856bf-307">Empty</span></span> | <span data-ttu-id="856bf-308">Anmelde Informationen, die mit jeder HTTP-Anforderung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="856bf-309">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="856bf-309">5 seconds</span></span> | <span data-ttu-id="856bf-310">Nur websockets.</span><span class="sxs-lookup"><span data-stu-id="856bf-310">WebSockets only.</span></span> <span data-ttu-id="856bf-311">Die maximale Zeitspanne, die der Client nach dem Schließen des Servers wartet, um die Anforderung zum Schließen zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="856bf-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="856bf-312">Wenn der Server die Schließung innerhalb dieses Zeitraums nicht anerkennt, trennt der Client die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="856bf-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="856bf-313">Leer</span><span class="sxs-lookup"><span data-stu-id="856bf-313">Empty</span></span> | <span data-ttu-id="856bf-314">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="856bf-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="856bf-315">Ein Delegat, der verwendet werden kann, um den `HttpMessageHandler` zum Senden von HTTP-Anforderungen verwendeten zu konfigurieren oder zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="856bf-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="856bf-316">Wird nicht für WebSocket-Verbindungen verwendet.</span><span class="sxs-lookup"><span data-stu-id="856bf-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="856bf-317">Dieser Delegat muss einen Wert zurückgeben, der nicht NULL ist, und erhält den Standardwert als Parameter.</span><span class="sxs-lookup"><span data-stu-id="856bf-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="856bf-318">Ändern Sie die Einstellungen für diesen Standardwert, und geben Sie ihn zurück, `HttpMessageHandler` oder geben Sie eine neue Instanz zurück.</span><span class="sxs-lookup"><span data-stu-id="856bf-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="856bf-319">**Wenn Sie den Handler ersetzen, stellen Sie sicher, dass die Einstellungen, die Sie beibehalten möchten, vom bereitgestellten Handler kopiert werden. Andernfalls gelten die konfigurierten Optionen (z. b. Cookies und Header) nicht für den neuen Handler.**</span><span class="sxs-lookup"><span data-stu-id="856bf-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="856bf-320">Ein HTTP-Proxy, der beim Senden von HTTP-Anforderungen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="856bf-321">Legen Sie diesen booleschen Wert fest, um die Standard Anmelde Informationen für http-und websockets-Anforderungen zu senden</span><span class="sxs-lookup"><span data-stu-id="856bf-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="856bf-322">Dies ermöglicht die Verwendung der Windows-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="856bf-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="856bf-323">Ein Delegat, der zum Konfigurieren zusätzlicher WebSocket-Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="856bf-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="856bf-324">Empfängt eine Instanz von [clientwebsocketoptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , die zum Konfigurieren der Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="856bf-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="856bf-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="856bf-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="856bf-326">JavaScript-Option</span><span class="sxs-lookup"><span data-stu-id="856bf-326">JavaScript Option</span></span> | <span data-ttu-id="856bf-327">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-327">Default Value</span></span> | <span data-ttu-id="856bf-328">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="856bf-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="856bf-329">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="856bf-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="856bf-330">Legen Sie dies `true` auf fest, um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="856bf-330">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="856bf-331">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="856bf-331">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="856bf-332">Diese Einstellung kann nicht aktiviert werden, wenn der Azure signalr-Dienst verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-332">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="856bf-333">Gibt an, ob Anmelde Informationen mit der cors-Anforderung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-333">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="856bf-334">Azure App Service verwendet Cookies für persistente Sitzungen und erfordert, dass diese Option ordnungsgemäß funktioniert.</span><span class="sxs-lookup"><span data-stu-id="856bf-334">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="856bf-335">Weitere Informationen zu cors mit signalr finden <xref:signalr/security#cross-origin-resource-sharing>Sie unter.</span><span class="sxs-lookup"><span data-stu-id="856bf-335">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="856bf-336">Java</span><span class="sxs-lookup"><span data-stu-id="856bf-336">Java</span></span>](#tab/java)

| <span data-ttu-id="856bf-337">Java-Option</span><span class="sxs-lookup"><span data-stu-id="856bf-337">Java Option</span></span> | <span data-ttu-id="856bf-338">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-338">Default Value</span></span> | <span data-ttu-id="856bf-339">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="856bf-339">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="856bf-340">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="856bf-340">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="856bf-341">Legen Sie dies `true` auf fest, um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="856bf-341">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="856bf-342">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="856bf-342">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="856bf-343">Diese Einstellung kann nicht aktiviert werden, wenn der Azure signalr-Dienst verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-343">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="856bf-344">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="856bf-344">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="856bf-345">Leer</span><span class="sxs-lookup"><span data-stu-id="856bf-345">Empty</span></span> | <span data-ttu-id="856bf-346">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="856bf-346">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="856bf-347">Im .NET-Client können diese Optionen durch den für `WithUrl`bereitgestellten Options Delegaten geändert werden:</span><span class="sxs-lookup"><span data-stu-id="856bf-347">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="856bf-348">Im JavaScript-Client können diese Optionen in einem JavaScript-Objekt bereitgestellt werden, `withUrl`das für Folgendes bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="856bf-348">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="856bf-349">Im Java-Client können diese Optionen mit den Methoden auf der `HttpHubConnectionBuilder` konfiguriert werden, die von der`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="856bf-349">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="856bf-350">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="856bf-350">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="856bf-351">JSON/messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="856bf-351">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="856bf-352">ASP.net Core signalr unterstützt zwei Protokolle für das Codieren von Nachrichten: [JSON](https://www.json.org/) und [messagepack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="856bf-352">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="856bf-353">Jedes Protokoll verfügt über Konfigurationsoptionen für die Serialisierung.</span><span class="sxs-lookup"><span data-stu-id="856bf-353">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="856bf-354">Die JSON-Serialisierung kann mithilfe der [addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) -Erweiterungsmethode auf dem Server konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-354">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="856bf-355">`AddJsonProtocol`kann nach [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-355">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="856bf-356">Die `AddJsonProtocol` -Methode nimmt einen Delegaten an `options` , der ein-Objekt empfängt.</span><span class="sxs-lookup"><span data-stu-id="856bf-356">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="856bf-357">Die [payloadserializeroptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) -Eigenschaft für dieses Objekt ist `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> ein-Objekt, das zum Konfigurieren der Serialisierung von Argumenten und Rückgabe Werten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="856bf-357">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="856bf-358">Weitere Informationen finden Sie in der [System. Text. JSON-Dokumentation](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="856bf-358">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="856bf-359">Verwenden Sie beispielsweise den folgenden Code in, um das Serialisierungsprogramm so zu konfigurieren, dass die Groß-/Kleinschreibung von Eigenschaftsnamen nicht geändert wird. verwenden `Startup.ConfigureServices`Sie dazu den folgenden Code in:</span><span class="sxs-lookup"><span data-stu-id="856bf-359">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="856bf-360">Im .NET-Client ist dieselbe `AddJsonProtocol` Erweiterungsmethode auf [hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)vorhanden.</span><span class="sxs-lookup"><span data-stu-id="856bf-360">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="856bf-361">Der `Microsoft.Extensions.DependencyInjection` Namespace muss importiert werden, um die Erweiterungsmethode aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="856bf-361">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="856bf-362">Zurzeit ist es nicht möglich, JSON-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="856bf-362">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="856bf-363">Wechseln Sie zu "newtonsoft. JSON".</span><span class="sxs-lookup"><span data-stu-id="856bf-363">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="856bf-364">Wenn Sie Features von `Newtonsoft.Json` benötigen, die in `System.Text.Json`nicht unterstützt werden, finden Sie weitere Informationen unter [Wechseln zu Newton Soft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="856bf-364">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="856bf-365">Messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="856bf-365">MessagePack serialization options</span></span>

<span data-ttu-id="856bf-366">Die messagepack-Serialisierung kann konfiguriert werden, indem ein Delegat für den [addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) -Aufruf bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-366">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="856bf-367">Weitere Informationen finden Sie [unter messagepack in signalr](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="856bf-367">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="856bf-368">Zurzeit ist es nicht möglich, die messagepack-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="856bf-368">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="856bf-369">Konfigurieren von Serveroptionen</span><span class="sxs-lookup"><span data-stu-id="856bf-369">Configure server options</span></span>

<span data-ttu-id="856bf-370">In der folgenden Tabelle werden die Optionen zum Konfigurieren von signalr Hubs beschrieben:</span><span class="sxs-lookup"><span data-stu-id="856bf-370">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="856bf-371">Option</span><span class="sxs-lookup"><span data-stu-id="856bf-371">Option</span></span> | <span data-ttu-id="856bf-372">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-372">Default Value</span></span> | <span data-ttu-id="856bf-373">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="856bf-373">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="856bf-374">30 Sekunden</span><span class="sxs-lookup"><span data-stu-id="856bf-374">30 seconds</span></span> | <span data-ttu-id="856bf-375">Der Server wird vom-Client als getrennt betrachtet, wenn in diesem Intervall keine Nachricht (einschließlich Keep-Alive) empfangen wurde.</span><span class="sxs-lookup"><span data-stu-id="856bf-375">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="856bf-376">Es kann länger dauern, bis der Client als getrennt gekennzeichnet ist, weil dies implementiert ist.</span><span class="sxs-lookup"><span data-stu-id="856bf-376">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="856bf-377">Der empfohlene Wert ist Double- `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="856bf-377">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="856bf-378">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="856bf-378">15 seconds</span></span> | <span data-ttu-id="856bf-379">Wenn der Client innerhalb dieses Zeitraums keine anfängliche Hand Shake Nachricht sendet, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="856bf-379">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="856bf-380">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="856bf-380">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="856bf-381">Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="856bf-381">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="856bf-382">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="856bf-382">15 seconds</span></span> | <span data-ttu-id="856bf-383">Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten.</span><span class="sxs-lookup"><span data-stu-id="856bf-383">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="856bf-384">Ändern Sie `KeepAliveInterval`die `ServerTimeout` / `serverTimeoutInMilliseconds` Einstellung auf dem Client, wenn Sie sich ändern.</span><span class="sxs-lookup"><span data-stu-id="856bf-384">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="856bf-385">Der empfohlene `ServerTimeout` / `serverTimeoutInMilliseconds` Wert ist Double- `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="856bf-385">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="856bf-386">Alle installierten Protokolle</span><span class="sxs-lookup"><span data-stu-id="856bf-386">All installed protocols</span></span> | <span data-ttu-id="856bf-387">Protokolle, die von diesem Hub unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-387">Protocols supported by this hub.</span></span> <span data-ttu-id="856bf-388">Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, aber Protokolle können aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="856bf-388">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="856bf-389">Wenn `true`der Wert ist, werden ausführliche Ausnahme Meldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-389">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="856bf-390">Der Standardwert `false`ist, da diese Ausnahme Meldungen vertrauliche Informationen enthalten können.</span><span class="sxs-lookup"><span data-stu-id="856bf-390">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="856bf-391">Die maximale Anzahl von Elementen, die für Client-uploadstreams gepuffert werden können.</span><span class="sxs-lookup"><span data-stu-id="856bf-391">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="856bf-392">Wenn dieser Grenzwert erreicht wird, wird die Verarbeitung von Aufrufen blockiert, bis der Server streamingelemente verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="856bf-392">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="856bf-393">32 KB</span><span class="sxs-lookup"><span data-stu-id="856bf-393">32 KB</span></span> | <span data-ttu-id="856bf-394">Maximale Größe einer einzelnen eingehenden Hub-Nachricht.</span><span class="sxs-lookup"><span data-stu-id="856bf-394">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="856bf-395">Optionen können für alle Hubs konfiguriert werden, indem ein Options Delegat für `AddSignalR` den Aufruf `Startup.ConfigureServices`in bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-395">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="856bf-396">Optionen für einen einzelnen Hub überschreiben die in `AddSignalR` bereitgestellten globalen Optionen und können mithilfe <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>von konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="856bf-396">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="856bf-397">Erweiterte http-Konfigurationsoptionen</span><span class="sxs-lookup"><span data-stu-id="856bf-397">Advanced HTTP configuration options</span></span>

<span data-ttu-id="856bf-398">Verwenden `HttpConnectionDispatcherOptions` Sie, um erweiterte Einstellungen im Zusammenhang mit Transporte und der Speicherpuffer Verwaltung zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="856bf-398">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="856bf-399">Diese Optionen werden konfiguriert, indem ein [Delegat an\<maphub T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-399">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="856bf-400">In der folgenden Tabelle werden die Optionen zum Konfigurieren der erweiterten http-Optionen von ASP.net Core signalr beschrieben:</span><span class="sxs-lookup"><span data-stu-id="856bf-400">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="856bf-401">Option</span><span class="sxs-lookup"><span data-stu-id="856bf-401">Option</span></span> | <span data-ttu-id="856bf-402">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-402">Default Value</span></span> | <span data-ttu-id="856bf-403">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="856bf-403">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="856bf-404">32 KB</span><span class="sxs-lookup"><span data-stu-id="856bf-404">32 KB</span></span> | <span data-ttu-id="856bf-405">Die maximale Anzahl von Bytes, die vom Client vor dem Anwenden des backdrucks vom Server gepuffert werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-405">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="856bf-406">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten schneller empfangen, ohne dass ein Rückdruck angewendet wird, der Arbeitsspeicher Verbrauch kann jedoch gesteigert werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-406">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="856bf-407">Daten, die automatisch von `Authorize` den auf die Hub-Klasse angewendeten Attributen gesammelt werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-407">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="856bf-408">Eine Liste von [iautorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) -Objekten, die verwendet werden, um zu bestimmen, ob ein Client für die Verbindung mit dem Hub autorisiert ist.</span><span class="sxs-lookup"><span data-stu-id="856bf-408">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="856bf-409">32 KB</span><span class="sxs-lookup"><span data-stu-id="856bf-409">32 KB</span></span> | <span data-ttu-id="856bf-410">Die maximale Anzahl von Bytes, die von der APP gesendet werden, die der Server puffert, bevor der Rückdruck beobachtet wird</span><span class="sxs-lookup"><span data-stu-id="856bf-410">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="856bf-411">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten schneller Puffern, ohne dass auf den Rückstand gewartet wird, aber die Arbeitsspeicher Nutzung erhöhen kann.</span><span class="sxs-lookup"><span data-stu-id="856bf-411">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="856bf-412">Alle Transporte sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="856bf-412">All Transports are enabled.</span></span> | <span data-ttu-id="856bf-413">Ein Bitflags-Enumeration `HttpTransportType` von Werten, die die Transporte einschränken können, die ein Client für die Verbindungs Herstellung verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="856bf-413">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="856bf-414">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="856bf-414">See below.</span></span> | <span data-ttu-id="856bf-415">Zusätzliche Optionen, die für den langen Abruf Transport spezifisch sind.</span><span class="sxs-lookup"><span data-stu-id="856bf-415">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="856bf-416">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="856bf-416">See below.</span></span> | <span data-ttu-id="856bf-417">Zusätzliche Optionen für den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="856bf-417">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="856bf-418">0</span><span class="sxs-lookup"><span data-stu-id="856bf-418">0</span></span> | <span data-ttu-id="856bf-419">Geben Sie die Mindestversion des Aushandlungs Protokolls an.</span><span class="sxs-lookup"><span data-stu-id="856bf-419">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="856bf-420">Hiermit werden Clients auf neuere Versionen beschränkt.</span><span class="sxs-lookup"><span data-stu-id="856bf-420">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="856bf-421">Der lange Abruf Transport bietet zusätzliche Optionen, die mit der `LongPolling` -Eigenschaft konfiguriert werden können:</span><span class="sxs-lookup"><span data-stu-id="856bf-421">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="856bf-422">Option</span><span class="sxs-lookup"><span data-stu-id="856bf-422">Option</span></span> | <span data-ttu-id="856bf-423">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-423">Default Value</span></span> | <span data-ttu-id="856bf-424">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="856bf-424">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="856bf-425">90 Sekunden</span><span class="sxs-lookup"><span data-stu-id="856bf-425">90 seconds</span></span> | <span data-ttu-id="856bf-426">Die maximale Zeitspanne, die der Server wartet, bis eine Nachricht an den Client gesendet wird, bevor eine einzelne Abruf Anforderung beendet wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-426">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="856bf-427">Das verringern dieses Werts bewirkt, dass der Client neue Abruf Anforderungen häufiger ausgibt.</span><span class="sxs-lookup"><span data-stu-id="856bf-427">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="856bf-428">Der WebSocket-Transport verfügt über zusätzliche Optionen, die mit der `WebSockets` -Eigenschaft konfiguriert werden können:</span><span class="sxs-lookup"><span data-stu-id="856bf-428">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="856bf-429">Option</span><span class="sxs-lookup"><span data-stu-id="856bf-429">Option</span></span> | <span data-ttu-id="856bf-430">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-430">Default Value</span></span> | <span data-ttu-id="856bf-431">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="856bf-431">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="856bf-432">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="856bf-432">5 seconds</span></span> | <span data-ttu-id="856bf-433">Wenn der Client nach dem Schließen des Servers nicht innerhalb dieses Zeitraums geschlossen werden kann, wird die Verbindung beendet.</span><span class="sxs-lookup"><span data-stu-id="856bf-433">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="856bf-434">Ein Delegat, der verwendet werden kann, `Sec-WebSocket-Protocol` um den-Header auf einen benutzerdefinierten Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="856bf-434">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="856bf-435">Der Delegat empfängt die vom Client angeforderten Werte als Eingabe und erwartet, dass der gewünschte Wert zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-435">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="856bf-436">Konfigurieren von Clientoptionen</span><span class="sxs-lookup"><span data-stu-id="856bf-436">Configure client options</span></span>

<span data-ttu-id="856bf-437">Client Optionen können für den `HubConnectionBuilder` Typ konfiguriert werden (verfügbar in den .net-und JavaScript-Clients).</span><span class="sxs-lookup"><span data-stu-id="856bf-437">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="856bf-438">Sie steht auch im Java-Client zur Verfügung, aber `HttpHubConnectionBuilder` die Unterklasse enthält die Generator-Konfigurationsoptionen sowie die `HubConnection` selbst.</span><span class="sxs-lookup"><span data-stu-id="856bf-438">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="856bf-439">Konfigurieren der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="856bf-439">Configure logging</span></span>

<span data-ttu-id="856bf-440">Die Protokollierung wird mithilfe der `ConfigureLogging` -Methode im .NET-Client konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="856bf-440">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="856bf-441">Protokollierungs Anbieter und Filter können auf die gleiche Weise wie auf dem Server registriert werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-441">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="856bf-442">Weitere Informationen finden Sie in der Dokumentation zur [Protokollierung ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="856bf-442">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="856bf-443">Um Protokollierungs Anbieter zu registrieren, müssen Sie die erforderlichen Pakete installieren.</span><span class="sxs-lookup"><span data-stu-id="856bf-443">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="856bf-444">Eine vollständige Liste finden Sie im Abschnitt [integrierte Protokollierungs Anbieter](xref:fundamentals/logging/index#built-in-logging-providers) der Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="856bf-444">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="856bf-445">Um z. b. die Konsolen Protokollierung zu `Microsoft.Extensions.Logging.Console` aktivieren, installieren Sie das nuget-Paket.</span><span class="sxs-lookup"><span data-stu-id="856bf-445">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="856bf-446">Die `AddConsole` Erweiterungsmethode aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="856bf-446">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="856bf-447">Im JavaScript-Client ist eine ähnliche `configureLogging` Methode vorhanden.</span><span class="sxs-lookup"><span data-stu-id="856bf-447">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="856bf-448">Geben Sie `LogLevel` einen Wert an, der die minimale Ebene der zu erstellenden Protokollmeldungen angibt.</span><span class="sxs-lookup"><span data-stu-id="856bf-448">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="856bf-449">Protokolle werden in das Browser Konsolenfenster geschrieben.</span><span class="sxs-lookup"><span data-stu-id="856bf-449">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="856bf-450">Anstelle eines `LogLevel` Werts können Sie auch einen `string` Wert angeben, der den Namen einer Protokollebene darstellt.</span><span class="sxs-lookup"><span data-stu-id="856bf-450">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="856bf-451">Dies ist hilfreich beim Konfigurieren der signalr-Protokollierung in Umgebungen, in denen Sie `LogLevel` keinen Zugriff auf die Konstanten haben.</span><span class="sxs-lookup"><span data-stu-id="856bf-451">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="856bf-452">In der folgenden Tabelle sind die verfügbaren Protokoll Ebenen aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="856bf-452">The following table lists the available log levels.</span></span> <span data-ttu-id="856bf-453">Der von Ihnen bereitgestellte `configureLogging` Wert, der die **minimale** Protokollierung festlegt, die protokolliert werden soll.</span><span class="sxs-lookup"><span data-stu-id="856bf-453">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="856bf-454">Nachrichten, die auf dieser Ebene protokolliert werden, **oder die nach der Tabelle aufgeführten Ebenen**werden protokolliert.</span><span class="sxs-lookup"><span data-stu-id="856bf-454">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="856bf-455">String</span><span class="sxs-lookup"><span data-stu-id="856bf-455">String</span></span>                      | <span data-ttu-id="856bf-456">LogLevel</span><span class="sxs-lookup"><span data-stu-id="856bf-456">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="856bf-457">`info` **oder** `information`</span><span class="sxs-lookup"><span data-stu-id="856bf-457">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="856bf-458">`warn` **oder** `warning`</span><span class="sxs-lookup"><span data-stu-id="856bf-458">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="856bf-459">Um die Protokollierung vollständig `signalR.LogLevel.None` zu deaktivieren `configureLogging` , geben Sie in der Methode an</span><span class="sxs-lookup"><span data-stu-id="856bf-459">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="856bf-460">Weitere Informationen zur Protokollierung finden Sie in der [signalr-Diagnose Dokumentation](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="856bf-460">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="856bf-461">Der signalr-Java-Client verwendet die [SLF4J](https://www.slf4j.org/) -Bibliothek für die Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="856bf-461">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="856bf-462">Dabei handelt es sich um eine allgemeine Protokollierungs-API, die es Benutzern der Bibliothek ermöglicht, ihre eigene spezifische Protokollierungs Implementierung zu wählen, indem Sie eine bestimmte Protokollierungs Abhängigkeit einbinden.</span><span class="sxs-lookup"><span data-stu-id="856bf-462">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="856bf-463">Der folgende Code Ausschnitt zeigt, wie mit dem `java.util.logging` signalr-Java-Client verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-463">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="856bf-464">Wenn Sie die Protokollierung in ihren Abhängigkeiten nicht konfigurieren, lädt SLF4J eine Standard Protokollierung ohne Vorgang mit folgender Warnmeldung:</span><span class="sxs-lookup"><span data-stu-id="856bf-464">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="856bf-465">Dies kann sicher ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-465">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="856bf-466">Zulässige Transporte konfigurieren</span><span class="sxs-lookup"><span data-stu-id="856bf-466">Configure allowed transports</span></span>

<span data-ttu-id="856bf-467">Die von signalr verwendeten Transporte können im `WithUrl` -Befehl (`withUrl` in JavaScript) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-467">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="856bf-468">Ein bitweises OR der Werte von `HttpTransportType` kann verwendet werden, um den Client so einzuschränken, dass nur die angegebenen Transporte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-468">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="856bf-469">Alle Transporte sind standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="856bf-469">All transports are enabled by default.</span></span>

<span data-ttu-id="856bf-470">So können Sie beispielsweise den Transport von Server gesendeten Ereignissen deaktivieren, aber websockets und lange Abruf Verbindungen zulassen:</span><span class="sxs-lookup"><span data-stu-id="856bf-470">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="856bf-471">Im JavaScript-Client werden Transporte durch Festlegen des-Felds `transport` für das Options-Objekt konfiguriert, `withUrl`das für bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="856bf-471">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="856bf-472">In dieser Version des Java-Clients ist websockets der einzige verfügbare Transport.</span><span class="sxs-lookup"><span data-stu-id="856bf-472">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="856bf-473">Im Java-Client wird der Transport mit der `withTransport` -Methode im ausgewählt. `HttpHubConnectionBuilder`</span><span class="sxs-lookup"><span data-stu-id="856bf-473">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="856bf-474">Der Java-Client verwendet standardmäßig den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="856bf-474">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="856bf-475">Der signalr-Java-Client unterstützt noch keinen Transport Fall Back.</span><span class="sxs-lookup"><span data-stu-id="856bf-475">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="856bf-476">Konfigurieren der bearerauthentifizierung</span><span class="sxs-lookup"><span data-stu-id="856bf-476">Configure bearer authentication</span></span>

<span data-ttu-id="856bf-477">Um Authentifizierungsdaten zusammen mit signalr-Anforderungen bereitzustellen, `AccessTokenProvider` verwenden Sie`accessTokenFactory` die-Option (in JavaScript), um eine Funktion anzugeben, die das gewünschte Zugriffs Token zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="856bf-477">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="856bf-478">Im .NET-Client wird dieses Zugriffs Token als http-tokenauthentifizierungstoken (mit dem- `Authorization` Header mit dem Typ `Bearer`) an das Token "bearerauthentifizierung" geleitet.</span><span class="sxs-lookup"><span data-stu-id="856bf-478">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="856bf-479">Im JavaScript-Client wird das Zugriffs Token als bearertoken verwendet, **außer** in einigen Fällen, in denen Browser-APIs die Fähigkeit zum Anwenden von Headern einschränken (insbesondere bei vom Server gesendeten Ereignissen und websockets-Anforderungen).</span><span class="sxs-lookup"><span data-stu-id="856bf-479">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="856bf-480">In diesen Fällen wird das Zugriffs Token als Abfrage Zeichenfolgen-Wert `access_token`bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="856bf-480">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="856bf-481">Im .NET-Client kann die `AccessTokenProvider` Option mithilfe des Options Delegaten in `WithUrl`angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="856bf-481">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="856bf-482">Im JavaScript-Client wird das Zugriffs Token konfiguriert, indem das `accessTokenFactory` -Feld für das Options-Objekt `withUrl`in festgelegt wird:</span><span class="sxs-lookup"><span data-stu-id="856bf-482">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="856bf-483">Im signalr-Java-Client können Sie ein bearertoken für die Authentifizierung konfigurieren, indem Sie eine zugriffstokenfactory für " [httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)" bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="856bf-483">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="856bf-484">Verwenden Sie [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , um eine einzelne [RxJava](https://github.com/ReactiveX/RxJava) [\< ](https://reactivex.io/documentation/single.html)rxjava-Zeichenfolge>bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="856bf-484">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="856bf-485">Beim Aufrufen von " [Single.](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)Write" können Sie Logik schreiben, um Zugriffs Token für den Client zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="856bf-485">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="856bf-486">Konfigurieren von Timeout-und Keep-Alive-Optionen</span><span class="sxs-lookup"><span data-stu-id="856bf-486">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="856bf-487">Zusätzliche Optionen zum Konfigurieren von Timeout und Keep-Alive-Verhalten sind für `HubConnection` das Objekt selbst verfügbar:</span><span class="sxs-lookup"><span data-stu-id="856bf-487">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="856bf-488">.NET</span><span class="sxs-lookup"><span data-stu-id="856bf-488">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="856bf-489">Option</span><span class="sxs-lookup"><span data-stu-id="856bf-489">Option</span></span> | <span data-ttu-id="856bf-490">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-490">Default value</span></span> | <span data-ttu-id="856bf-491">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="856bf-491">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="856bf-492">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="856bf-492">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="856bf-493">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="856bf-493">Timeout for server activity.</span></span> <span data-ttu-id="856bf-494">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst `Closed` das Ereignis`onclose` aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="856bf-494">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="856bf-495">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-495">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="856bf-496">Der empfohlene Wert ist eine Zahl, die mindestens dem `KeepAliveInterval` Wert des Servers entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="856bf-496">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="856bf-497">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="856bf-497">15 seconds</span></span> | <span data-ttu-id="856bf-498">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="856bf-498">Timeout for initial server handshake.</span></span> <span data-ttu-id="856bf-499">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `Closed` Ereignis aus`onclose` (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="856bf-499">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="856bf-500">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="856bf-500">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="856bf-501">Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="856bf-501">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="856bf-502">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="856bf-502">15 seconds</span></span> | <span data-ttu-id="856bf-503">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="856bf-503">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="856bf-504">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="856bf-504">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="856bf-505">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="856bf-505">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="856bf-506">Im .NET-Client werden Timeout Werte als `TimeSpan` Werte angegeben.</span><span class="sxs-lookup"><span data-stu-id="856bf-506">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="856bf-507">JavaScript</span><span class="sxs-lookup"><span data-stu-id="856bf-507">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="856bf-508">Option</span><span class="sxs-lookup"><span data-stu-id="856bf-508">Option</span></span> | <span data-ttu-id="856bf-509">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-509">Default value</span></span> | <span data-ttu-id="856bf-510">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="856bf-510">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="856bf-511">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="856bf-511">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="856bf-512">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="856bf-512">Timeout for server activity.</span></span> <span data-ttu-id="856bf-513">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst `onclose` das Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="856bf-513">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="856bf-514">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-514">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="856bf-515">Der empfohlene Wert ist eine Zahl, die mindestens dem `KeepAliveInterval` Wert des Servers entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="856bf-515">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="856bf-516">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="856bf-516">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="856bf-517">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="856bf-517">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="856bf-518">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="856bf-518">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="856bf-519">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="856bf-519">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="856bf-520">Java</span><span class="sxs-lookup"><span data-stu-id="856bf-520">Java</span></span>](#tab/java)

| <span data-ttu-id="856bf-521">Option</span><span class="sxs-lookup"><span data-stu-id="856bf-521">Option</span></span> | <span data-ttu-id="856bf-522">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-522">Default value</span></span> | <span data-ttu-id="856bf-523">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="856bf-523">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="856bf-524">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="856bf-524">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="856bf-525">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="856bf-525">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="856bf-526">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="856bf-526">Timeout for server activity.</span></span> <span data-ttu-id="856bf-527">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst `onClose` das Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="856bf-527">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="856bf-528">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-528">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="856bf-529">Der empfohlene Wert ist eine Zahl, die mindestens dem `KeepAliveInterval` Wert des Servers entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="856bf-529">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="856bf-530">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="856bf-530">15 seconds</span></span> | <span data-ttu-id="856bf-531">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="856bf-531">Timeout for initial server handshake.</span></span> <span data-ttu-id="856bf-532">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `onClose` -Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="856bf-532">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="856bf-533">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="856bf-533">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="856bf-534">Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="856bf-534">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="856bf-535">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="856bf-535">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="856bf-536">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="856bf-536">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="856bf-537">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="856bf-537">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="856bf-538">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="856bf-538">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="856bf-539">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="856bf-539">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="856bf-540">Konfigurieren zusätzlicher Optionen</span><span class="sxs-lookup"><span data-stu-id="856bf-540">Configure additional options</span></span>

<span data-ttu-id="856bf-541">`WithUrl` Zusätzliche Optionen können in der-Methode (`withUrl` in JavaScript) für `HubConnectionBuilder` oder für die verschiedenen Konfigurations-APIs auf dem `HttpHubConnectionBuilder` im Java-Client konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="856bf-541">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="856bf-542">.NET</span><span class="sxs-lookup"><span data-stu-id="856bf-542">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="856bf-543">.Net-Option</span><span class="sxs-lookup"><span data-stu-id="856bf-543">.NET Option</span></span> |  <span data-ttu-id="856bf-544">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-544">Default value</span></span> | <span data-ttu-id="856bf-545">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="856bf-545">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="856bf-546">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="856bf-546">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="856bf-547">Legen Sie dies `true` auf fest, um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="856bf-547">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="856bf-548">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="856bf-548">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="856bf-549">Diese Einstellung kann nicht aktiviert werden, wenn der Azure signalr-Dienst verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-549">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="856bf-550">Leer</span><span class="sxs-lookup"><span data-stu-id="856bf-550">Empty</span></span> | <span data-ttu-id="856bf-551">Eine Auflistung von TLS-Zertifikaten, die zum Authentifizieren von Anforderungen gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="856bf-551">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="856bf-552">Leer</span><span class="sxs-lookup"><span data-stu-id="856bf-552">Empty</span></span> | <span data-ttu-id="856bf-553">Eine Auflistung von HTTP-Cookies, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="856bf-553">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="856bf-554">Leer</span><span class="sxs-lookup"><span data-stu-id="856bf-554">Empty</span></span> | <span data-ttu-id="856bf-555">Anmelde Informationen, die mit jeder HTTP-Anforderung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-555">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="856bf-556">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="856bf-556">5 seconds</span></span> | <span data-ttu-id="856bf-557">Nur websockets.</span><span class="sxs-lookup"><span data-stu-id="856bf-557">WebSockets only.</span></span> <span data-ttu-id="856bf-558">Die maximale Zeitspanne, die der Client nach dem Schließen des Servers wartet, um die Anforderung zum Schließen zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="856bf-558">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="856bf-559">Wenn der Server die Schließung innerhalb dieses Zeitraums nicht anerkennt, trennt der Client die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="856bf-559">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="856bf-560">Leer</span><span class="sxs-lookup"><span data-stu-id="856bf-560">Empty</span></span> | <span data-ttu-id="856bf-561">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="856bf-561">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="856bf-562">Ein Delegat, der verwendet werden kann, um den `HttpMessageHandler` zum Senden von HTTP-Anforderungen verwendeten zu konfigurieren oder zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="856bf-562">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="856bf-563">Wird nicht für WebSocket-Verbindungen verwendet.</span><span class="sxs-lookup"><span data-stu-id="856bf-563">Not used for WebSocket connections.</span></span> <span data-ttu-id="856bf-564">Dieser Delegat muss einen Wert zurückgeben, der nicht NULL ist, und erhält den Standardwert als Parameter.</span><span class="sxs-lookup"><span data-stu-id="856bf-564">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="856bf-565">Ändern Sie die Einstellungen für diesen Standardwert, und geben Sie ihn zurück, `HttpMessageHandler` oder geben Sie eine neue Instanz zurück.</span><span class="sxs-lookup"><span data-stu-id="856bf-565">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="856bf-566">**Wenn Sie den Handler ersetzen, stellen Sie sicher, dass die Einstellungen, die Sie beibehalten möchten, vom bereitgestellten Handler kopiert werden. Andernfalls gelten die konfigurierten Optionen (z. b. Cookies und Header) nicht für den neuen Handler.**</span><span class="sxs-lookup"><span data-stu-id="856bf-566">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="856bf-567">Ein HTTP-Proxy, der beim Senden von HTTP-Anforderungen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-567">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="856bf-568">Legen Sie diesen booleschen Wert fest, um die Standard Anmelde Informationen für http-und websockets-Anforderungen zu senden</span><span class="sxs-lookup"><span data-stu-id="856bf-568">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="856bf-569">Dies ermöglicht die Verwendung der Windows-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="856bf-569">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="856bf-570">Ein Delegat, der zum Konfigurieren zusätzlicher WebSocket-Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="856bf-570">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="856bf-571">Empfängt eine Instanz von [clientwebsocketoptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , die zum Konfigurieren der Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="856bf-571">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="856bf-572">JavaScript</span><span class="sxs-lookup"><span data-stu-id="856bf-572">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="856bf-573">JavaScript-Option</span><span class="sxs-lookup"><span data-stu-id="856bf-573">JavaScript Option</span></span> | <span data-ttu-id="856bf-574">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-574">Default Value</span></span> | <span data-ttu-id="856bf-575">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="856bf-575">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="856bf-576">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="856bf-576">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="856bf-577">Legen Sie dies `true` auf fest, um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="856bf-577">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="856bf-578">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="856bf-578">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="856bf-579">Diese Einstellung kann nicht aktiviert werden, wenn der Azure signalr-Dienst verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-579">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="856bf-580">Java</span><span class="sxs-lookup"><span data-stu-id="856bf-580">Java</span></span>](#tab/java)

| <span data-ttu-id="856bf-581">Java-Option</span><span class="sxs-lookup"><span data-stu-id="856bf-581">Java Option</span></span> | <span data-ttu-id="856bf-582">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-582">Default Value</span></span> | <span data-ttu-id="856bf-583">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="856bf-583">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="856bf-584">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="856bf-584">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="856bf-585">Legen Sie dies `true` auf fest, um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="856bf-585">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="856bf-586">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="856bf-586">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="856bf-587">Diese Einstellung kann nicht aktiviert werden, wenn der Azure signalr-Dienst verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-587">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="856bf-588">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="856bf-588">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="856bf-589">Leer</span><span class="sxs-lookup"><span data-stu-id="856bf-589">Empty</span></span> | <span data-ttu-id="856bf-590">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="856bf-590">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="856bf-591">Im .NET-Client können diese Optionen durch den für `WithUrl`bereitgestellten Options Delegaten geändert werden:</span><span class="sxs-lookup"><span data-stu-id="856bf-591">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="856bf-592">Im JavaScript-Client können diese Optionen in einem JavaScript-Objekt bereitgestellt werden, `withUrl`das für Folgendes bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="856bf-592">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="856bf-593">Im Java-Client können diese Optionen mit den Methoden auf der `HttpHubConnectionBuilder` konfiguriert werden, die von der`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="856bf-593">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="856bf-594">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="856bf-594">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="856bf-595">JSON/messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="856bf-595">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="856bf-596">ASP.net Core signalr unterstützt zwei Protokolle für das Codieren von Nachrichten: [JSON](https://www.json.org/) und [messagepack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="856bf-596">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="856bf-597">Jedes Protokoll verfügt über Konfigurationsoptionen für die Serialisierung.</span><span class="sxs-lookup"><span data-stu-id="856bf-597">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="856bf-598">Die JSON-Serialisierung kann mithilfe der [addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) -Erweiterungsmethode auf dem Server konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-598">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="856bf-599">`AddJsonProtocol`kann nach [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-599">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="856bf-600">Die `AddJsonProtocol` -Methode nimmt einen Delegaten an `options` , der ein-Objekt empfängt.</span><span class="sxs-lookup"><span data-stu-id="856bf-600">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="856bf-601">Die [payloadserializeroptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) -Eigenschaft für dieses Objekt ist `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> ein-Objekt, das zum Konfigurieren der Serialisierung von Argumenten und Rückgabe Werten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="856bf-601">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="856bf-602">Weitere Informationen finden Sie in der [System. Text. JSON-Dokumentation](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="856bf-602">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="856bf-603">Verwenden Sie beispielsweise den folgenden Code in, um das Serialisierungsprogramm so zu konfigurieren, dass die Groß-/Kleinschreibung von Eigenschaftsnamen nicht geändert wird. verwenden `Startup.ConfigureServices`Sie dazu den folgenden Code in:</span><span class="sxs-lookup"><span data-stu-id="856bf-603">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="856bf-604">Im .NET-Client ist dieselbe `AddJsonProtocol` Erweiterungsmethode auf [hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)vorhanden.</span><span class="sxs-lookup"><span data-stu-id="856bf-604">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="856bf-605">Der `Microsoft.Extensions.DependencyInjection` Namespace muss importiert werden, um die Erweiterungsmethode aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="856bf-605">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="856bf-606">Zurzeit ist es nicht möglich, JSON-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="856bf-606">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="856bf-607">Wechseln Sie zu "newtonsoft. JSON".</span><span class="sxs-lookup"><span data-stu-id="856bf-607">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="856bf-608">Wenn Sie Features von `Newtonsoft.Json` benötigen, die in `System.Text.Json`nicht unterstützt werden, finden Sie weitere Informationen unter [Wechseln zu Newton Soft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="856bf-608">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="856bf-609">Messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="856bf-609">MessagePack serialization options</span></span>

<span data-ttu-id="856bf-610">Die messagepack-Serialisierung kann konfiguriert werden, indem ein Delegat für den [addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) -Aufruf bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-610">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="856bf-611">Weitere Informationen finden Sie [unter messagepack in signalr](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="856bf-611">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="856bf-612">Zurzeit ist es nicht möglich, die messagepack-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="856bf-612">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="856bf-613">Konfigurieren von Serveroptionen</span><span class="sxs-lookup"><span data-stu-id="856bf-613">Configure server options</span></span>

<span data-ttu-id="856bf-614">In der folgenden Tabelle werden die Optionen zum Konfigurieren von signalr Hubs beschrieben:</span><span class="sxs-lookup"><span data-stu-id="856bf-614">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="856bf-615">Option</span><span class="sxs-lookup"><span data-stu-id="856bf-615">Option</span></span> | <span data-ttu-id="856bf-616">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-616">Default Value</span></span> | <span data-ttu-id="856bf-617">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="856bf-617">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="856bf-618">30 Sekunden</span><span class="sxs-lookup"><span data-stu-id="856bf-618">30 seconds</span></span> | <span data-ttu-id="856bf-619">Der Server wird vom-Client als getrennt betrachtet, wenn in diesem Intervall keine Nachricht (einschließlich Keep-Alive) empfangen wurde.</span><span class="sxs-lookup"><span data-stu-id="856bf-619">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="856bf-620">Es kann länger dauern, bis der Client als getrennt gekennzeichnet ist, weil dies implementiert ist.</span><span class="sxs-lookup"><span data-stu-id="856bf-620">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="856bf-621">Der empfohlene Wert ist Double- `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="856bf-621">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="856bf-622">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="856bf-622">15 seconds</span></span> | <span data-ttu-id="856bf-623">Wenn der Client innerhalb dieses Zeitraums keine anfängliche Hand Shake Nachricht sendet, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="856bf-623">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="856bf-624">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="856bf-624">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="856bf-625">Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="856bf-625">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="856bf-626">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="856bf-626">15 seconds</span></span> | <span data-ttu-id="856bf-627">Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten.</span><span class="sxs-lookup"><span data-stu-id="856bf-627">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="856bf-628">Ändern Sie `KeepAliveInterval`die `ServerTimeout` / `serverTimeoutInMilliseconds` Einstellung auf dem Client, wenn Sie sich ändern.</span><span class="sxs-lookup"><span data-stu-id="856bf-628">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="856bf-629">Der empfohlene `ServerTimeout` / `serverTimeoutInMilliseconds` Wert ist Double- `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="856bf-629">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="856bf-630">Alle installierten Protokolle</span><span class="sxs-lookup"><span data-stu-id="856bf-630">All installed protocols</span></span> | <span data-ttu-id="856bf-631">Protokolle, die von diesem Hub unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-631">Protocols supported by this hub.</span></span> <span data-ttu-id="856bf-632">Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, aber Protokolle können aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="856bf-632">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="856bf-633">Wenn `true`der Wert ist, werden ausführliche Ausnahme Meldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-633">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="856bf-634">Der Standardwert `false`ist, da diese Ausnahme Meldungen vertrauliche Informationen enthalten können.</span><span class="sxs-lookup"><span data-stu-id="856bf-634">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="856bf-635">Die maximale Anzahl von Elementen, die für Client-uploadstreams gepuffert werden können.</span><span class="sxs-lookup"><span data-stu-id="856bf-635">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="856bf-636">Wenn dieser Grenzwert erreicht wird, wird die Verarbeitung von Aufrufen blockiert, bis der Server streamingelemente verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="856bf-636">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="856bf-637">32 KB</span><span class="sxs-lookup"><span data-stu-id="856bf-637">32 KB</span></span> | <span data-ttu-id="856bf-638">Maximale Größe einer einzelnen eingehenden Hub-Nachricht.</span><span class="sxs-lookup"><span data-stu-id="856bf-638">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="856bf-639">Optionen können für alle Hubs konfiguriert werden, indem ein Options Delegat für `AddSignalR` den Aufruf `Startup.ConfigureServices`in bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-639">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="856bf-640">Optionen für einen einzelnen Hub überschreiben die in `AddSignalR` bereitgestellten globalen Optionen und können mithilfe <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>von konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="856bf-640">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="856bf-641">Erweiterte http-Konfigurationsoptionen</span><span class="sxs-lookup"><span data-stu-id="856bf-641">Advanced HTTP configuration options</span></span>

<span data-ttu-id="856bf-642">Verwenden `HttpConnectionDispatcherOptions` Sie, um erweiterte Einstellungen im Zusammenhang mit Transporte und der Speicherpuffer Verwaltung zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="856bf-642">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="856bf-643">Diese Optionen werden konfiguriert, indem ein [Delegat an\<maphub T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-643">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="856bf-644">In der folgenden Tabelle werden die Optionen zum Konfigurieren der erweiterten http-Optionen von ASP.net Core signalr beschrieben:</span><span class="sxs-lookup"><span data-stu-id="856bf-644">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="856bf-645">Option</span><span class="sxs-lookup"><span data-stu-id="856bf-645">Option</span></span> | <span data-ttu-id="856bf-646">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-646">Default Value</span></span> | <span data-ttu-id="856bf-647">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="856bf-647">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="856bf-648">32 KB</span><span class="sxs-lookup"><span data-stu-id="856bf-648">32 KB</span></span> | <span data-ttu-id="856bf-649">Die maximale Anzahl von Bytes, die vom Client vor dem Anwenden des backdrucks vom Server gepuffert werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-649">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="856bf-650">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten schneller empfangen, ohne dass ein Rückdruck angewendet wird, der Arbeitsspeicher Verbrauch kann jedoch gesteigert werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-650">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="856bf-651">Daten, die automatisch von `Authorize` den auf die Hub-Klasse angewendeten Attributen gesammelt werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-651">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="856bf-652">Eine Liste von [iautorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) -Objekten, die verwendet werden, um zu bestimmen, ob ein Client für die Verbindung mit dem Hub autorisiert ist.</span><span class="sxs-lookup"><span data-stu-id="856bf-652">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="856bf-653">32 KB</span><span class="sxs-lookup"><span data-stu-id="856bf-653">32 KB</span></span> | <span data-ttu-id="856bf-654">Die maximale Anzahl von Bytes, die von der APP gesendet werden, die der Server puffert, bevor der Rückdruck beobachtet wird</span><span class="sxs-lookup"><span data-stu-id="856bf-654">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="856bf-655">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten schneller Puffern, ohne dass auf den Rückstand gewartet wird, aber die Arbeitsspeicher Nutzung erhöhen kann.</span><span class="sxs-lookup"><span data-stu-id="856bf-655">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="856bf-656">Alle Transporte sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="856bf-656">All Transports are enabled.</span></span> | <span data-ttu-id="856bf-657">Ein Bitflags-Enumeration `HttpTransportType` von Werten, die die Transporte einschränken können, die ein Client für die Verbindungs Herstellung verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="856bf-657">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="856bf-658">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="856bf-658">See below.</span></span> | <span data-ttu-id="856bf-659">Zusätzliche Optionen, die für den langen Abruf Transport spezifisch sind.</span><span class="sxs-lookup"><span data-stu-id="856bf-659">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="856bf-660">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="856bf-660">See below.</span></span> | <span data-ttu-id="856bf-661">Zusätzliche Optionen für den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="856bf-661">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="856bf-662">Der lange Abruf Transport bietet zusätzliche Optionen, die mit der `LongPolling` -Eigenschaft konfiguriert werden können:</span><span class="sxs-lookup"><span data-stu-id="856bf-662">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="856bf-663">Option</span><span class="sxs-lookup"><span data-stu-id="856bf-663">Option</span></span> | <span data-ttu-id="856bf-664">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-664">Default Value</span></span> | <span data-ttu-id="856bf-665">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="856bf-665">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="856bf-666">90 Sekunden</span><span class="sxs-lookup"><span data-stu-id="856bf-666">90 seconds</span></span> | <span data-ttu-id="856bf-667">Die maximale Zeitspanne, die der Server wartet, bis eine Nachricht an den Client gesendet wird, bevor eine einzelne Abruf Anforderung beendet wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-667">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="856bf-668">Das verringern dieses Werts bewirkt, dass der Client neue Abruf Anforderungen häufiger ausgibt.</span><span class="sxs-lookup"><span data-stu-id="856bf-668">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="856bf-669">Der WebSocket-Transport verfügt über zusätzliche Optionen, die mit der `WebSockets` -Eigenschaft konfiguriert werden können:</span><span class="sxs-lookup"><span data-stu-id="856bf-669">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="856bf-670">Option</span><span class="sxs-lookup"><span data-stu-id="856bf-670">Option</span></span> | <span data-ttu-id="856bf-671">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-671">Default Value</span></span> | <span data-ttu-id="856bf-672">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="856bf-672">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="856bf-673">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="856bf-673">5 seconds</span></span> | <span data-ttu-id="856bf-674">Wenn der Client nach dem Schließen des Servers nicht innerhalb dieses Zeitraums geschlossen werden kann, wird die Verbindung beendet.</span><span class="sxs-lookup"><span data-stu-id="856bf-674">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="856bf-675">Ein Delegat, der verwendet werden kann, `Sec-WebSocket-Protocol` um den-Header auf einen benutzerdefinierten Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="856bf-675">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="856bf-676">Der Delegat empfängt die vom Client angeforderten Werte als Eingabe und erwartet, dass der gewünschte Wert zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-676">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="856bf-677">Konfigurieren von Clientoptionen</span><span class="sxs-lookup"><span data-stu-id="856bf-677">Configure client options</span></span>

<span data-ttu-id="856bf-678">Client Optionen können für den `HubConnectionBuilder` Typ konfiguriert werden (verfügbar in den .net-und JavaScript-Clients).</span><span class="sxs-lookup"><span data-stu-id="856bf-678">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="856bf-679">Sie steht auch im Java-Client zur Verfügung, aber `HttpHubConnectionBuilder` die Unterklasse enthält die Generator-Konfigurationsoptionen sowie die `HubConnection` selbst.</span><span class="sxs-lookup"><span data-stu-id="856bf-679">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="856bf-680">Konfigurieren der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="856bf-680">Configure logging</span></span>

<span data-ttu-id="856bf-681">Die Protokollierung wird mithilfe der `ConfigureLogging` -Methode im .NET-Client konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="856bf-681">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="856bf-682">Protokollierungs Anbieter und Filter können auf die gleiche Weise wie auf dem Server registriert werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-682">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="856bf-683">Weitere Informationen finden Sie in der Dokumentation zur [Protokollierung ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="856bf-683">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="856bf-684">Um Protokollierungs Anbieter zu registrieren, müssen Sie die erforderlichen Pakete installieren.</span><span class="sxs-lookup"><span data-stu-id="856bf-684">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="856bf-685">Eine vollständige Liste finden Sie im Abschnitt [integrierte Protokollierungs Anbieter](xref:fundamentals/logging/index#built-in-logging-providers) der Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="856bf-685">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="856bf-686">Um z. b. die Konsolen Protokollierung zu `Microsoft.Extensions.Logging.Console` aktivieren, installieren Sie das nuget-Paket.</span><span class="sxs-lookup"><span data-stu-id="856bf-686">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="856bf-687">Die `AddConsole` Erweiterungsmethode aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="856bf-687">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="856bf-688">Im JavaScript-Client ist eine ähnliche `configureLogging` Methode vorhanden.</span><span class="sxs-lookup"><span data-stu-id="856bf-688">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="856bf-689">Geben Sie `LogLevel` einen Wert an, der die minimale Ebene der zu erstellenden Protokollmeldungen angibt.</span><span class="sxs-lookup"><span data-stu-id="856bf-689">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="856bf-690">Protokolle werden in das Browser Konsolenfenster geschrieben.</span><span class="sxs-lookup"><span data-stu-id="856bf-690">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="856bf-691">Anstelle eines `LogLevel` Werts können Sie auch einen `string` Wert angeben, der den Namen einer Protokollebene darstellt.</span><span class="sxs-lookup"><span data-stu-id="856bf-691">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="856bf-692">Dies ist hilfreich beim Konfigurieren der signalr-Protokollierung in Umgebungen, in denen Sie `LogLevel` keinen Zugriff auf die Konstanten haben.</span><span class="sxs-lookup"><span data-stu-id="856bf-692">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="856bf-693">In der folgenden Tabelle sind die verfügbaren Protokoll Ebenen aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="856bf-693">The following table lists the available log levels.</span></span> <span data-ttu-id="856bf-694">Der von Ihnen bereitgestellte `configureLogging` Wert, der die **minimale** Protokollierung festlegt, die protokolliert werden soll.</span><span class="sxs-lookup"><span data-stu-id="856bf-694">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="856bf-695">Nachrichten, die auf dieser Ebene protokolliert werden, **oder die nach der Tabelle aufgeführten Ebenen**werden protokolliert.</span><span class="sxs-lookup"><span data-stu-id="856bf-695">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="856bf-696">String</span><span class="sxs-lookup"><span data-stu-id="856bf-696">String</span></span>                      | <span data-ttu-id="856bf-697">LogLevel</span><span class="sxs-lookup"><span data-stu-id="856bf-697">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="856bf-698">`info` **oder** `information`</span><span class="sxs-lookup"><span data-stu-id="856bf-698">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="856bf-699">`warn` **oder** `warning`</span><span class="sxs-lookup"><span data-stu-id="856bf-699">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="856bf-700">Um die Protokollierung vollständig `signalR.LogLevel.None` zu deaktivieren `configureLogging` , geben Sie in der Methode an</span><span class="sxs-lookup"><span data-stu-id="856bf-700">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="856bf-701">Weitere Informationen zur Protokollierung finden Sie in der [signalr-Diagnose Dokumentation](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="856bf-701">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="856bf-702">Der signalr-Java-Client verwendet die [SLF4J](https://www.slf4j.org/) -Bibliothek für die Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="856bf-702">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="856bf-703">Dabei handelt es sich um eine allgemeine Protokollierungs-API, die es Benutzern der Bibliothek ermöglicht, ihre eigene spezifische Protokollierungs Implementierung zu wählen, indem Sie eine bestimmte Protokollierungs Abhängigkeit einbinden.</span><span class="sxs-lookup"><span data-stu-id="856bf-703">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="856bf-704">Der folgende Code Ausschnitt zeigt, wie mit dem `java.util.logging` signalr-Java-Client verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-704">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="856bf-705">Wenn Sie die Protokollierung in ihren Abhängigkeiten nicht konfigurieren, lädt SLF4J eine Standard Protokollierung ohne Vorgang mit folgender Warnmeldung:</span><span class="sxs-lookup"><span data-stu-id="856bf-705">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="856bf-706">Dies kann sicher ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-706">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="856bf-707">Zulässige Transporte konfigurieren</span><span class="sxs-lookup"><span data-stu-id="856bf-707">Configure allowed transports</span></span>

<span data-ttu-id="856bf-708">Die von signalr verwendeten Transporte können im `WithUrl` -Befehl (`withUrl` in JavaScript) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-708">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="856bf-709">Ein bitweises OR der Werte von `HttpTransportType` kann verwendet werden, um den Client so einzuschränken, dass nur die angegebenen Transporte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-709">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="856bf-710">Alle Transporte sind standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="856bf-710">All transports are enabled by default.</span></span>

<span data-ttu-id="856bf-711">So können Sie beispielsweise den Transport von Server gesendeten Ereignissen deaktivieren, aber websockets und lange Abruf Verbindungen zulassen:</span><span class="sxs-lookup"><span data-stu-id="856bf-711">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="856bf-712">Im JavaScript-Client werden Transporte durch Festlegen des-Felds `transport` für das Options-Objekt konfiguriert, `withUrl`das für bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="856bf-712">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="856bf-713">In dieser Version des Java-Clients ist websockets der einzige verfügbare Transport.</span><span class="sxs-lookup"><span data-stu-id="856bf-713">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="856bf-714">Im Java-Client wird der Transport mit der `withTransport` -Methode im ausgewählt. `HttpHubConnectionBuilder`</span><span class="sxs-lookup"><span data-stu-id="856bf-714">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="856bf-715">Der Java-Client verwendet standardmäßig den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="856bf-715">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="856bf-716">Der signalr-Java-Client unterstützt noch keinen Transport Fall Back.</span><span class="sxs-lookup"><span data-stu-id="856bf-716">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="856bf-717">Konfigurieren der bearerauthentifizierung</span><span class="sxs-lookup"><span data-stu-id="856bf-717">Configure bearer authentication</span></span>

<span data-ttu-id="856bf-718">Um Authentifizierungsdaten zusammen mit signalr-Anforderungen bereitzustellen, `AccessTokenProvider` verwenden Sie`accessTokenFactory` die-Option (in JavaScript), um eine Funktion anzugeben, die das gewünschte Zugriffs Token zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="856bf-718">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="856bf-719">Im .NET-Client wird dieses Zugriffs Token als http-tokenauthentifizierungstoken (mit dem- `Authorization` Header mit dem Typ `Bearer`) an das Token "bearerauthentifizierung" geleitet.</span><span class="sxs-lookup"><span data-stu-id="856bf-719">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="856bf-720">Im JavaScript-Client wird das Zugriffs Token als bearertoken verwendet, **außer** in einigen Fällen, in denen Browser-APIs die Fähigkeit zum Anwenden von Headern einschränken (insbesondere bei vom Server gesendeten Ereignissen und websockets-Anforderungen).</span><span class="sxs-lookup"><span data-stu-id="856bf-720">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="856bf-721">In diesen Fällen wird das Zugriffs Token als Abfrage Zeichenfolgen-Wert `access_token`bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="856bf-721">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="856bf-722">Im .NET-Client kann die `AccessTokenProvider` Option mithilfe des Options Delegaten in `WithUrl`angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="856bf-722">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="856bf-723">Im JavaScript-Client wird das Zugriffs Token konfiguriert, indem das `accessTokenFactory` -Feld für das Options-Objekt `withUrl`in festgelegt wird:</span><span class="sxs-lookup"><span data-stu-id="856bf-723">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="856bf-724">Im signalr-Java-Client können Sie ein bearertoken für die Authentifizierung konfigurieren, indem Sie eine zugriffstokenfactory für " [httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)" bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="856bf-724">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="856bf-725">Verwenden Sie [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , um eine einzelne [RxJava](https://github.com/ReactiveX/RxJava) [\< ](https://reactivex.io/documentation/single.html)rxjava-Zeichenfolge>bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="856bf-725">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="856bf-726">Beim Aufrufen von " [Single.](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)Write" können Sie Logik schreiben, um Zugriffs Token für den Client zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="856bf-726">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="856bf-727">Konfigurieren von Timeout-und Keep-Alive-Optionen</span><span class="sxs-lookup"><span data-stu-id="856bf-727">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="856bf-728">Zusätzliche Optionen zum Konfigurieren von Timeout und Keep-Alive-Verhalten sind für `HubConnection` das Objekt selbst verfügbar:</span><span class="sxs-lookup"><span data-stu-id="856bf-728">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="856bf-729">.NET</span><span class="sxs-lookup"><span data-stu-id="856bf-729">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="856bf-730">Option</span><span class="sxs-lookup"><span data-stu-id="856bf-730">Option</span></span> | <span data-ttu-id="856bf-731">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-731">Default value</span></span> | <span data-ttu-id="856bf-732">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="856bf-732">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="856bf-733">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="856bf-733">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="856bf-734">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="856bf-734">Timeout for server activity.</span></span> <span data-ttu-id="856bf-735">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst `Closed` das Ereignis`onclose` aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="856bf-735">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="856bf-736">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-736">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="856bf-737">Der empfohlene Wert ist eine Zahl, die mindestens dem `KeepAliveInterval` Wert des Servers entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="856bf-737">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="856bf-738">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="856bf-738">15 seconds</span></span> | <span data-ttu-id="856bf-739">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="856bf-739">Timeout for initial server handshake.</span></span> <span data-ttu-id="856bf-740">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `Closed` Ereignis aus`onclose` (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="856bf-740">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="856bf-741">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="856bf-741">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="856bf-742">Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="856bf-742">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="856bf-743">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="856bf-743">15 seconds</span></span> | <span data-ttu-id="856bf-744">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="856bf-744">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="856bf-745">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="856bf-745">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="856bf-746">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="856bf-746">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="856bf-747">Im .NET-Client werden Timeout Werte als `TimeSpan` Werte angegeben.</span><span class="sxs-lookup"><span data-stu-id="856bf-747">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="856bf-748">JavaScript</span><span class="sxs-lookup"><span data-stu-id="856bf-748">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="856bf-749">Option</span><span class="sxs-lookup"><span data-stu-id="856bf-749">Option</span></span> | <span data-ttu-id="856bf-750">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-750">Default value</span></span> | <span data-ttu-id="856bf-751">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="856bf-751">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="856bf-752">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="856bf-752">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="856bf-753">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="856bf-753">Timeout for server activity.</span></span> <span data-ttu-id="856bf-754">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst `onclose` das Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="856bf-754">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="856bf-755">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-755">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="856bf-756">Der empfohlene Wert ist eine Zahl, die mindestens dem `KeepAliveInterval` Wert des Servers entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="856bf-756">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="856bf-757">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="856bf-757">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="856bf-758">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="856bf-758">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="856bf-759">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="856bf-759">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="856bf-760">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="856bf-760">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="856bf-761">Java</span><span class="sxs-lookup"><span data-stu-id="856bf-761">Java</span></span>](#tab/java)

| <span data-ttu-id="856bf-762">Option</span><span class="sxs-lookup"><span data-stu-id="856bf-762">Option</span></span> | <span data-ttu-id="856bf-763">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-763">Default value</span></span> | <span data-ttu-id="856bf-764">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="856bf-764">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="856bf-765">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="856bf-765">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="856bf-766">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="856bf-766">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="856bf-767">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="856bf-767">Timeout for server activity.</span></span> <span data-ttu-id="856bf-768">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst `onClose` das Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="856bf-768">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="856bf-769">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-769">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="856bf-770">Der empfohlene Wert ist eine Zahl, die mindestens dem `KeepAliveInterval` Wert des Servers entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="856bf-770">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="856bf-771">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="856bf-771">15 seconds</span></span> | <span data-ttu-id="856bf-772">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="856bf-772">Timeout for initial server handshake.</span></span> <span data-ttu-id="856bf-773">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `onClose` -Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="856bf-773">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="856bf-774">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="856bf-774">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="856bf-775">Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="856bf-775">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="856bf-776">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="856bf-776">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="856bf-777">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="856bf-777">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="856bf-778">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="856bf-778">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="856bf-779">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="856bf-779">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="856bf-780">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="856bf-780">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="856bf-781">Konfigurieren zusätzlicher Optionen</span><span class="sxs-lookup"><span data-stu-id="856bf-781">Configure additional options</span></span>

<span data-ttu-id="856bf-782">`WithUrl` Zusätzliche Optionen können in der-Methode (`withUrl` in JavaScript) für `HubConnectionBuilder` oder für die verschiedenen Konfigurations-APIs auf dem `HttpHubConnectionBuilder` im Java-Client konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="856bf-782">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="856bf-783">.NET</span><span class="sxs-lookup"><span data-stu-id="856bf-783">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="856bf-784">.Net-Option</span><span class="sxs-lookup"><span data-stu-id="856bf-784">.NET Option</span></span> |  <span data-ttu-id="856bf-785">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-785">Default value</span></span> | <span data-ttu-id="856bf-786">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="856bf-786">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="856bf-787">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="856bf-787">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="856bf-788">Legen Sie dies `true` auf fest, um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="856bf-788">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="856bf-789">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="856bf-789">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="856bf-790">Diese Einstellung kann nicht aktiviert werden, wenn der Azure signalr-Dienst verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-790">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="856bf-791">Leer</span><span class="sxs-lookup"><span data-stu-id="856bf-791">Empty</span></span> | <span data-ttu-id="856bf-792">Eine Auflistung von TLS-Zertifikaten, die zum Authentifizieren von Anforderungen gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="856bf-792">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="856bf-793">Leer</span><span class="sxs-lookup"><span data-stu-id="856bf-793">Empty</span></span> | <span data-ttu-id="856bf-794">Eine Auflistung von HTTP-Cookies, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="856bf-794">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="856bf-795">Leer</span><span class="sxs-lookup"><span data-stu-id="856bf-795">Empty</span></span> | <span data-ttu-id="856bf-796">Anmelde Informationen, die mit jeder HTTP-Anforderung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-796">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="856bf-797">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="856bf-797">5 seconds</span></span> | <span data-ttu-id="856bf-798">Nur websockets.</span><span class="sxs-lookup"><span data-stu-id="856bf-798">WebSockets only.</span></span> <span data-ttu-id="856bf-799">Die maximale Zeitspanne, die der Client nach dem Schließen des Servers wartet, um die Anforderung zum Schließen zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="856bf-799">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="856bf-800">Wenn der Server die Schließung innerhalb dieses Zeitraums nicht anerkennt, trennt der Client die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="856bf-800">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="856bf-801">Leer</span><span class="sxs-lookup"><span data-stu-id="856bf-801">Empty</span></span> | <span data-ttu-id="856bf-802">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="856bf-802">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="856bf-803">Ein Delegat, der verwendet werden kann, um den `HttpMessageHandler` zum Senden von HTTP-Anforderungen verwendeten zu konfigurieren oder zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="856bf-803">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="856bf-804">Wird nicht für WebSocket-Verbindungen verwendet.</span><span class="sxs-lookup"><span data-stu-id="856bf-804">Not used for WebSocket connections.</span></span> <span data-ttu-id="856bf-805">Dieser Delegat muss einen Wert zurückgeben, der nicht NULL ist, und erhält den Standardwert als Parameter.</span><span class="sxs-lookup"><span data-stu-id="856bf-805">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="856bf-806">Ändern Sie die Einstellungen für diesen Standardwert, und geben Sie ihn zurück, `HttpMessageHandler` oder geben Sie eine neue Instanz zurück.</span><span class="sxs-lookup"><span data-stu-id="856bf-806">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="856bf-807">**Wenn Sie den Handler ersetzen, stellen Sie sicher, dass die Einstellungen, die Sie beibehalten möchten, vom bereitgestellten Handler kopiert werden. Andernfalls gelten die konfigurierten Optionen (z. b. Cookies und Header) nicht für den neuen Handler.**</span><span class="sxs-lookup"><span data-stu-id="856bf-807">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="856bf-808">Ein HTTP-Proxy, der beim Senden von HTTP-Anforderungen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-808">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="856bf-809">Legen Sie diesen booleschen Wert fest, um die Standard Anmelde Informationen für http-und websockets-Anforderungen zu senden</span><span class="sxs-lookup"><span data-stu-id="856bf-809">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="856bf-810">Dies ermöglicht die Verwendung der Windows-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="856bf-810">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="856bf-811">Ein Delegat, der zum Konfigurieren zusätzlicher WebSocket-Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="856bf-811">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="856bf-812">Empfängt eine Instanz von [clientwebsocketoptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , die zum Konfigurieren der Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="856bf-812">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="856bf-813">JavaScript</span><span class="sxs-lookup"><span data-stu-id="856bf-813">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="856bf-814">JavaScript-Option</span><span class="sxs-lookup"><span data-stu-id="856bf-814">JavaScript Option</span></span> | <span data-ttu-id="856bf-815">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-815">Default Value</span></span> | <span data-ttu-id="856bf-816">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="856bf-816">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="856bf-817">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="856bf-817">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="856bf-818">Legen Sie dies `true` auf fest, um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="856bf-818">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="856bf-819">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="856bf-819">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="856bf-820">Diese Einstellung kann nicht aktiviert werden, wenn der Azure signalr-Dienst verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-820">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="856bf-821">Java</span><span class="sxs-lookup"><span data-stu-id="856bf-821">Java</span></span>](#tab/java)

| <span data-ttu-id="856bf-822">Java-Option</span><span class="sxs-lookup"><span data-stu-id="856bf-822">Java Option</span></span> | <span data-ttu-id="856bf-823">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-823">Default Value</span></span> | <span data-ttu-id="856bf-824">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="856bf-824">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="856bf-825">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="856bf-825">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="856bf-826">Legen Sie dies `true` auf fest, um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="856bf-826">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="856bf-827">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="856bf-827">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="856bf-828">Diese Einstellung kann nicht aktiviert werden, wenn der Azure signalr-Dienst verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-828">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="856bf-829">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="856bf-829">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="856bf-830">Leer</span><span class="sxs-lookup"><span data-stu-id="856bf-830">Empty</span></span> | <span data-ttu-id="856bf-831">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="856bf-831">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="856bf-832">Im .NET-Client können diese Optionen durch den für `WithUrl`bereitgestellten Options Delegaten geändert werden:</span><span class="sxs-lookup"><span data-stu-id="856bf-832">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="856bf-833">Im JavaScript-Client können diese Optionen in einem JavaScript-Objekt bereitgestellt werden, `withUrl`das für Folgendes bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="856bf-833">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="856bf-834">Im Java-Client können diese Optionen mit den Methoden auf der `HttpHubConnectionBuilder` konfiguriert werden, die von der`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="856bf-834">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="856bf-835">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="856bf-835">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="856bf-836">JSON/messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="856bf-836">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="856bf-837">ASP.net Core signalr unterstützt zwei Protokolle für das Codieren von Nachrichten: [JSON](https://www.json.org/) und [messagepack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="856bf-837">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="856bf-838">Jedes Protokoll verfügt über Konfigurationsoptionen für die Serialisierung.</span><span class="sxs-lookup"><span data-stu-id="856bf-838">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="856bf-839">Die JSON-Serialisierung kann mithilfe der [addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) -Erweiterungsmethode auf dem Server konfiguriert werden, die nach [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in der `Startup.ConfigureServices` -Methode hinzugefügt werden kann.</span><span class="sxs-lookup"><span data-stu-id="856bf-839">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="856bf-840">Die `AddJsonProtocol` -Methode nimmt einen Delegaten an `options` , der ein-Objekt empfängt.</span><span class="sxs-lookup"><span data-stu-id="856bf-840">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="856bf-841">Die [payloadserializersettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) -Eigenschaft für dieses Objekt ist ein `JsonSerializerSettings` JSON.NET-Objekt, das zum Konfigurieren der Serialisierung von Argumenten und Rückgabe Werten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="856bf-841">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="856bf-842">Weitere Informationen finden Sie in der [JSON.NET-Dokumentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="856bf-842">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="856bf-843">Verwenden Sie beispielsweise den folgenden Code in `Startup.ConfigureServices`, um das Serialisierungsprogramm so zu konfigurieren, dass "PascalCase"-Eigenschaftsnamen anstelle der Standardnamen "CamelCase" verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="856bf-843">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="856bf-844">Im .NET-Client ist dieselbe `AddJsonProtocol` Erweiterungsmethode auf [hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)vorhanden.</span><span class="sxs-lookup"><span data-stu-id="856bf-844">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="856bf-845">Der `Microsoft.Extensions.DependencyInjection` Namespace muss importiert werden, um die Erweiterungsmethode aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="856bf-845">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="856bf-846">Zurzeit ist es nicht möglich, JSON-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="856bf-846">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="856bf-847">Messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="856bf-847">MessagePack serialization options</span></span>

<span data-ttu-id="856bf-848">Die messagepack-Serialisierung kann konfiguriert werden, indem ein Delegat für den [addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) -Aufruf bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-848">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="856bf-849">Weitere Informationen finden Sie [unter messagepack in signalr](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="856bf-849">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="856bf-850">Zurzeit ist es nicht möglich, die messagepack-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="856bf-850">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="856bf-851">Konfigurieren von Serveroptionen</span><span class="sxs-lookup"><span data-stu-id="856bf-851">Configure server options</span></span>

<span data-ttu-id="856bf-852">In der folgenden Tabelle werden die Optionen zum Konfigurieren von signalr Hubs beschrieben:</span><span class="sxs-lookup"><span data-stu-id="856bf-852">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="856bf-853">Option</span><span class="sxs-lookup"><span data-stu-id="856bf-853">Option</span></span> | <span data-ttu-id="856bf-854">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-854">Default Value</span></span> | <span data-ttu-id="856bf-855">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="856bf-855">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="856bf-856">30 Sekunden</span><span class="sxs-lookup"><span data-stu-id="856bf-856">30 seconds</span></span> | <span data-ttu-id="856bf-857">Der Server wird vom-Client als getrennt betrachtet, wenn in diesem Intervall keine Nachricht (einschließlich Keep-Alive) empfangen wurde.</span><span class="sxs-lookup"><span data-stu-id="856bf-857">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="856bf-858">Es kann länger dauern, bis der Client als getrennt gekennzeichnet ist, weil dies implementiert ist.</span><span class="sxs-lookup"><span data-stu-id="856bf-858">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="856bf-859">Der empfohlene Wert ist Double- `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="856bf-859">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="856bf-860">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="856bf-860">15 seconds</span></span> | <span data-ttu-id="856bf-861">Wenn der Client innerhalb dieses Zeitraums keine anfängliche Hand Shake Nachricht sendet, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="856bf-861">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="856bf-862">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="856bf-862">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="856bf-863">Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="856bf-863">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="856bf-864">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="856bf-864">15 seconds</span></span> | <span data-ttu-id="856bf-865">Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten.</span><span class="sxs-lookup"><span data-stu-id="856bf-865">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="856bf-866">Ändern Sie `KeepAliveInterval`die `ServerTimeout` / `serverTimeoutInMilliseconds` Einstellung auf dem Client, wenn Sie sich ändern.</span><span class="sxs-lookup"><span data-stu-id="856bf-866">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="856bf-867">Der empfohlene `ServerTimeout` / `serverTimeoutInMilliseconds` Wert ist Double- `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="856bf-867">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="856bf-868">Alle installierten Protokolle</span><span class="sxs-lookup"><span data-stu-id="856bf-868">All installed protocols</span></span> | <span data-ttu-id="856bf-869">Protokolle, die von diesem Hub unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-869">Protocols supported by this hub.</span></span> <span data-ttu-id="856bf-870">Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, aber Protokolle können aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="856bf-870">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="856bf-871">Wenn `true`der Wert ist, werden ausführliche Ausnahme Meldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-871">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="856bf-872">Der Standardwert `false`ist, da diese Ausnahme Meldungen vertrauliche Informationen enthalten können.</span><span class="sxs-lookup"><span data-stu-id="856bf-872">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="856bf-873">Optionen können für alle Hubs konfiguriert werden, indem ein Options Delegat für `AddSignalR` den Aufruf `Startup.ConfigureServices`in bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-873">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="856bf-874">Optionen für einen einzelnen Hub überschreiben die in `AddSignalR` bereitgestellten globalen Optionen und können mithilfe <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>von konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="856bf-874">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="856bf-875">Erweiterte http-Konfigurationsoptionen</span><span class="sxs-lookup"><span data-stu-id="856bf-875">Advanced HTTP configuration options</span></span>

<span data-ttu-id="856bf-876">Verwenden `HttpConnectionDispatcherOptions` Sie, um erweiterte Einstellungen im Zusammenhang mit Transporte und der Speicherpuffer Verwaltung zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="856bf-876">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="856bf-877">Diese Optionen werden konfiguriert, indem ein [Delegat an\<maphub T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-877">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="856bf-878">In der folgenden Tabelle werden die Optionen zum Konfigurieren der erweiterten http-Optionen von ASP.net Core signalr beschrieben:</span><span class="sxs-lookup"><span data-stu-id="856bf-878">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="856bf-879">Option</span><span class="sxs-lookup"><span data-stu-id="856bf-879">Option</span></span> | <span data-ttu-id="856bf-880">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-880">Default Value</span></span> | <span data-ttu-id="856bf-881">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="856bf-881">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="856bf-882">32 KB</span><span class="sxs-lookup"><span data-stu-id="856bf-882">32 KB</span></span> | <span data-ttu-id="856bf-883">Die maximale Anzahl von Bytes, die vom Client empfangen werden, der vom Server gepuffert wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-883">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="856bf-884">Eine Erhöhung dieses Werts ermöglicht es dem Server, größere Nachrichten zu empfangen, kann sich jedoch negativ auf den Arbeitsspeicherverbrauch auswirken.</span><span class="sxs-lookup"><span data-stu-id="856bf-884">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="856bf-885">Daten, die automatisch von `Authorize` den auf die Hub-Klasse angewendeten Attributen gesammelt werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-885">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="856bf-886">Eine Liste von [iautorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) -Objekten, die verwendet werden, um zu bestimmen, ob ein Client für die Verbindung mit dem Hub autorisiert ist.</span><span class="sxs-lookup"><span data-stu-id="856bf-886">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="856bf-887">32 KB</span><span class="sxs-lookup"><span data-stu-id="856bf-887">32 KB</span></span> | <span data-ttu-id="856bf-888">Die maximale Anzahl von Bytes, die von der APP gesendet werden, die der Server puffert.</span><span class="sxs-lookup"><span data-stu-id="856bf-888">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="856bf-889">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten senden, kann sich jedoch negativ auf die Arbeitsspeicher Auslastung auswirken.</span><span class="sxs-lookup"><span data-stu-id="856bf-889">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="856bf-890">Alle Transporte sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="856bf-890">All Transports are enabled.</span></span> | <span data-ttu-id="856bf-891">Ein Bitflags-Enumeration `HttpTransportType` von Werten, die die Transporte einschränken können, die ein Client für die Verbindungs Herstellung verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="856bf-891">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="856bf-892">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="856bf-892">See below.</span></span> | <span data-ttu-id="856bf-893">Zusätzliche Optionen, die für den langen Abruf Transport spezifisch sind.</span><span class="sxs-lookup"><span data-stu-id="856bf-893">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="856bf-894">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="856bf-894">See below.</span></span> | <span data-ttu-id="856bf-895">Zusätzliche Optionen für den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="856bf-895">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="856bf-896">Der lange Abruf Transport bietet zusätzliche Optionen, die mit der `LongPolling` -Eigenschaft konfiguriert werden können:</span><span class="sxs-lookup"><span data-stu-id="856bf-896">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="856bf-897">Option</span><span class="sxs-lookup"><span data-stu-id="856bf-897">Option</span></span> | <span data-ttu-id="856bf-898">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-898">Default Value</span></span> | <span data-ttu-id="856bf-899">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="856bf-899">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="856bf-900">90 Sekunden</span><span class="sxs-lookup"><span data-stu-id="856bf-900">90 seconds</span></span> | <span data-ttu-id="856bf-901">Die maximale Zeitspanne, die der Server wartet, bis eine Nachricht an den Client gesendet wird, bevor eine einzelne Abruf Anforderung beendet wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-901">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="856bf-902">Das verringern dieses Werts bewirkt, dass der Client neue Abruf Anforderungen häufiger ausgibt.</span><span class="sxs-lookup"><span data-stu-id="856bf-902">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="856bf-903">Der WebSocket-Transport verfügt über zusätzliche Optionen, die mit der `WebSockets` -Eigenschaft konfiguriert werden können:</span><span class="sxs-lookup"><span data-stu-id="856bf-903">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="856bf-904">Option</span><span class="sxs-lookup"><span data-stu-id="856bf-904">Option</span></span> | <span data-ttu-id="856bf-905">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-905">Default Value</span></span> | <span data-ttu-id="856bf-906">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="856bf-906">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="856bf-907">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="856bf-907">5 seconds</span></span> | <span data-ttu-id="856bf-908">Wenn der Client nach dem Schließen des Servers nicht innerhalb dieses Zeitraums geschlossen werden kann, wird die Verbindung beendet.</span><span class="sxs-lookup"><span data-stu-id="856bf-908">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="856bf-909">Ein Delegat, der verwendet werden kann, `Sec-WebSocket-Protocol` um den-Header auf einen benutzerdefinierten Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="856bf-909">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="856bf-910">Der Delegat empfängt die vom Client angeforderten Werte als Eingabe und erwartet, dass der gewünschte Wert zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-910">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="856bf-911">Konfigurieren von Clientoptionen</span><span class="sxs-lookup"><span data-stu-id="856bf-911">Configure client options</span></span>

<span data-ttu-id="856bf-912">Client Optionen können für den `HubConnectionBuilder` Typ konfiguriert werden (verfügbar in den .net-und JavaScript-Clients).</span><span class="sxs-lookup"><span data-stu-id="856bf-912">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="856bf-913">Sie steht auch im Java-Client zur Verfügung, aber `HttpHubConnectionBuilder` die Unterklasse enthält die Generator-Konfigurationsoptionen sowie die `HubConnection` selbst.</span><span class="sxs-lookup"><span data-stu-id="856bf-913">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="856bf-914">Konfigurieren der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="856bf-914">Configure logging</span></span>

<span data-ttu-id="856bf-915">Die Protokollierung wird mithilfe der `ConfigureLogging` -Methode im .NET-Client konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="856bf-915">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="856bf-916">Protokollierungs Anbieter und Filter können auf die gleiche Weise wie auf dem Server registriert werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-916">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="856bf-917">Weitere Informationen finden Sie in der Dokumentation zur [Protokollierung ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="856bf-917">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="856bf-918">Um Protokollierungs Anbieter zu registrieren, müssen Sie die erforderlichen Pakete installieren.</span><span class="sxs-lookup"><span data-stu-id="856bf-918">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="856bf-919">Eine vollständige Liste finden Sie im Abschnitt [integrierte Protokollierungs Anbieter](xref:fundamentals/logging/index#built-in-logging-providers) der Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="856bf-919">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="856bf-920">Um z. b. die Konsolen Protokollierung zu `Microsoft.Extensions.Logging.Console` aktivieren, installieren Sie das nuget-Paket.</span><span class="sxs-lookup"><span data-stu-id="856bf-920">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="856bf-921">Die `AddConsole` Erweiterungsmethode aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="856bf-921">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="856bf-922">Im JavaScript-Client ist eine ähnliche `configureLogging` Methode vorhanden.</span><span class="sxs-lookup"><span data-stu-id="856bf-922">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="856bf-923">Geben Sie `LogLevel` einen Wert an, der die minimale Ebene der zu erstellenden Protokollmeldungen angibt.</span><span class="sxs-lookup"><span data-stu-id="856bf-923">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="856bf-924">Protokolle werden in das Browser Konsolenfenster geschrieben.</span><span class="sxs-lookup"><span data-stu-id="856bf-924">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="856bf-925">Um die Protokollierung vollständig `signalR.LogLevel.None` zu deaktivieren `configureLogging` , geben Sie in der Methode an</span><span class="sxs-lookup"><span data-stu-id="856bf-925">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="856bf-926">Weitere Informationen zur Protokollierung finden Sie in der [signalr-Diagnose Dokumentation](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="856bf-926">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="856bf-927">Der signalr-Java-Client verwendet die [SLF4J](https://www.slf4j.org/) -Bibliothek für die Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="856bf-927">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="856bf-928">Dabei handelt es sich um eine allgemeine Protokollierungs-API, die es Benutzern der Bibliothek ermöglicht, ihre eigene spezifische Protokollierungs Implementierung zu wählen, indem Sie eine bestimmte Protokollierungs Abhängigkeit einbinden.</span><span class="sxs-lookup"><span data-stu-id="856bf-928">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="856bf-929">Der folgende Code Ausschnitt zeigt, wie mit dem `java.util.logging` signalr-Java-Client verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-929">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="856bf-930">Wenn Sie die Protokollierung in ihren Abhängigkeiten nicht konfigurieren, lädt SLF4J eine Standard Protokollierung ohne Vorgang mit folgender Warnmeldung:</span><span class="sxs-lookup"><span data-stu-id="856bf-930">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="856bf-931">Dies kann sicher ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-931">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="856bf-932">Zulässige Transporte konfigurieren</span><span class="sxs-lookup"><span data-stu-id="856bf-932">Configure allowed transports</span></span>

<span data-ttu-id="856bf-933">Die von signalr verwendeten Transporte können im `WithUrl` -Befehl (`withUrl` in JavaScript) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-933">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="856bf-934">Ein bitweises OR der Werte von `HttpTransportType` kann verwendet werden, um den Client so einzuschränken, dass nur die angegebenen Transporte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-934">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="856bf-935">Alle Transporte sind standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="856bf-935">All transports are enabled by default.</span></span>

<span data-ttu-id="856bf-936">So können Sie beispielsweise den Transport von Server gesendeten Ereignissen deaktivieren, aber websockets und lange Abruf Verbindungen zulassen:</span><span class="sxs-lookup"><span data-stu-id="856bf-936">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="856bf-937">Im JavaScript-Client werden Transporte durch Festlegen des-Felds `transport` für das Options-Objekt konfiguriert, `withUrl`das für bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="856bf-937">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="856bf-938">In dieser Version des Java-Clients ist websockets der einzige verfügbare Transport.</span><span class="sxs-lookup"><span data-stu-id="856bf-938">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="856bf-939">Konfigurieren der bearerauthentifizierung</span><span class="sxs-lookup"><span data-stu-id="856bf-939">Configure bearer authentication</span></span>

<span data-ttu-id="856bf-940">Um Authentifizierungsdaten zusammen mit signalr-Anforderungen bereitzustellen, `AccessTokenProvider` verwenden Sie`accessTokenFactory` die-Option (in JavaScript), um eine Funktion anzugeben, die das gewünschte Zugriffs Token zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="856bf-940">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="856bf-941">Im .NET-Client wird dieses Zugriffs Token als http-tokenauthentifizierungstoken (mit dem- `Authorization` Header mit dem Typ `Bearer`) an das Token "bearerauthentifizierung" geleitet.</span><span class="sxs-lookup"><span data-stu-id="856bf-941">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="856bf-942">Im JavaScript-Client wird das Zugriffs Token als bearertoken verwendet, **außer** in einigen Fällen, in denen Browser-APIs die Fähigkeit zum Anwenden von Headern einschränken (insbesondere bei vom Server gesendeten Ereignissen und websockets-Anforderungen).</span><span class="sxs-lookup"><span data-stu-id="856bf-942">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="856bf-943">In diesen Fällen wird das Zugriffs Token als Abfrage Zeichenfolgen-Wert `access_token`bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="856bf-943">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="856bf-944">Im .NET-Client kann die `AccessTokenProvider` Option mithilfe des Options Delegaten in `WithUrl`angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="856bf-944">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="856bf-945">Im JavaScript-Client wird das Zugriffs Token konfiguriert, indem das `accessTokenFactory` -Feld für das Options-Objekt `withUrl`in festgelegt wird:</span><span class="sxs-lookup"><span data-stu-id="856bf-945">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="856bf-946">Im signalr-Java-Client können Sie ein bearertoken für die Authentifizierung konfigurieren, indem Sie eine zugriffstokenfactory für " [httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)" bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="856bf-946">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="856bf-947">Verwenden Sie [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , um eine einzelne [RxJava](https://github.com/ReactiveX/RxJava) [\< ](https://reactivex.io/documentation/single.html)rxjava-Zeichenfolge>bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="856bf-947">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="856bf-948">Beim Aufrufen von " [Single.](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)Write" können Sie Logik schreiben, um Zugriffs Token für den Client zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="856bf-948">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="856bf-949">Konfigurieren von Timeout-und Keep-Alive-Optionen</span><span class="sxs-lookup"><span data-stu-id="856bf-949">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="856bf-950">Zusätzliche Optionen zum Konfigurieren von Timeout und Keep-Alive-Verhalten sind für `HubConnection` das Objekt selbst verfügbar:</span><span class="sxs-lookup"><span data-stu-id="856bf-950">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="856bf-951">.NET</span><span class="sxs-lookup"><span data-stu-id="856bf-951">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="856bf-952">Option</span><span class="sxs-lookup"><span data-stu-id="856bf-952">Option</span></span> | <span data-ttu-id="856bf-953">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-953">Default value</span></span> | <span data-ttu-id="856bf-954">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="856bf-954">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="856bf-955">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="856bf-955">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="856bf-956">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="856bf-956">Timeout for server activity.</span></span> <span data-ttu-id="856bf-957">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst `Closed` das Ereignis`onclose` aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="856bf-957">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="856bf-958">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-958">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="856bf-959">Der empfohlene Wert ist eine Zahl, die mindestens dem `KeepAliveInterval` Wert des Servers entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="856bf-959">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="856bf-960">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="856bf-960">15 seconds</span></span> | <span data-ttu-id="856bf-961">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="856bf-961">Timeout for initial server handshake.</span></span> <span data-ttu-id="856bf-962">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `Closed` Ereignis aus`onclose` (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="856bf-962">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="856bf-963">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="856bf-963">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="856bf-964">Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="856bf-964">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="856bf-965">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="856bf-965">15 seconds</span></span> | <span data-ttu-id="856bf-966">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="856bf-966">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="856bf-967">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="856bf-967">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="856bf-968">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="856bf-968">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="856bf-969">Im .NET-Client werden Timeout Werte als `TimeSpan` Werte angegeben.</span><span class="sxs-lookup"><span data-stu-id="856bf-969">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="856bf-970">JavaScript</span><span class="sxs-lookup"><span data-stu-id="856bf-970">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="856bf-971">Option</span><span class="sxs-lookup"><span data-stu-id="856bf-971">Option</span></span> | <span data-ttu-id="856bf-972">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-972">Default value</span></span> | <span data-ttu-id="856bf-973">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="856bf-973">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="856bf-974">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="856bf-974">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="856bf-975">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="856bf-975">Timeout for server activity.</span></span> <span data-ttu-id="856bf-976">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst `onclose` das Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="856bf-976">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="856bf-977">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-977">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="856bf-978">Der empfohlene Wert ist eine Zahl, die mindestens dem `KeepAliveInterval` Wert des Servers entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="856bf-978">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="856bf-979">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="856bf-979">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="856bf-980">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="856bf-980">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="856bf-981">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="856bf-981">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="856bf-982">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="856bf-982">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="856bf-983">Java</span><span class="sxs-lookup"><span data-stu-id="856bf-983">Java</span></span>](#tab/java)

| <span data-ttu-id="856bf-984">Option</span><span class="sxs-lookup"><span data-stu-id="856bf-984">Option</span></span> | <span data-ttu-id="856bf-985">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-985">Default value</span></span> | <span data-ttu-id="856bf-986">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="856bf-986">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="856bf-987">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="856bf-987">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="856bf-988">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="856bf-988">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="856bf-989">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="856bf-989">Timeout for server activity.</span></span> <span data-ttu-id="856bf-990">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst `onClose` das Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="856bf-990">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="856bf-991">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-991">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="856bf-992">Der empfohlene Wert ist eine Zahl, die mindestens dem `KeepAliveInterval` Wert des Servers entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="856bf-992">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="856bf-993">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="856bf-993">15 seconds</span></span> | <span data-ttu-id="856bf-994">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="856bf-994">Timeout for initial server handshake.</span></span> <span data-ttu-id="856bf-995">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `onClose` -Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="856bf-995">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="856bf-996">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="856bf-996">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="856bf-997">Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="856bf-997">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="856bf-998">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="856bf-998">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="856bf-999">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="856bf-999">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="856bf-1000">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="856bf-1000">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="856bf-1001">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="856bf-1001">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="856bf-1002">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="856bf-1002">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="856bf-1003">Konfigurieren zusätzlicher Optionen</span><span class="sxs-lookup"><span data-stu-id="856bf-1003">Configure additional options</span></span>

<span data-ttu-id="856bf-1004">`WithUrl` Zusätzliche Optionen können in der-Methode (`withUrl` in JavaScript) für `HubConnectionBuilder` oder für die verschiedenen Konfigurations-APIs auf dem `HttpHubConnectionBuilder` im Java-Client konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="856bf-1004">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="856bf-1005">.NET</span><span class="sxs-lookup"><span data-stu-id="856bf-1005">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="856bf-1006">.Net-Option</span><span class="sxs-lookup"><span data-stu-id="856bf-1006">.NET Option</span></span> |  <span data-ttu-id="856bf-1007">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-1007">Default value</span></span> | <span data-ttu-id="856bf-1008">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="856bf-1008">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="856bf-1009">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="856bf-1009">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="856bf-1010">Legen Sie dies `true` auf fest, um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="856bf-1010">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="856bf-1011">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="856bf-1011">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="856bf-1012">Diese Einstellung kann nicht aktiviert werden, wenn der Azure signalr-Dienst verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-1012">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="856bf-1013">Leer</span><span class="sxs-lookup"><span data-stu-id="856bf-1013">Empty</span></span> | <span data-ttu-id="856bf-1014">Eine Auflistung von TLS-Zertifikaten, die zum Authentifizieren von Anforderungen gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="856bf-1014">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="856bf-1015">Leer</span><span class="sxs-lookup"><span data-stu-id="856bf-1015">Empty</span></span> | <span data-ttu-id="856bf-1016">Eine Auflistung von HTTP-Cookies, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="856bf-1016">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="856bf-1017">Leer</span><span class="sxs-lookup"><span data-stu-id="856bf-1017">Empty</span></span> | <span data-ttu-id="856bf-1018">Anmelde Informationen, die mit jeder HTTP-Anforderung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-1018">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="856bf-1019">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="856bf-1019">5 seconds</span></span> | <span data-ttu-id="856bf-1020">Nur websockets.</span><span class="sxs-lookup"><span data-stu-id="856bf-1020">WebSockets only.</span></span> <span data-ttu-id="856bf-1021">Die maximale Zeitspanne, die der Client nach dem Schließen des Servers wartet, um die Anforderung zum Schließen zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="856bf-1021">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="856bf-1022">Wenn der Server die Schließung innerhalb dieses Zeitraums nicht anerkennt, trennt der Client die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="856bf-1022">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="856bf-1023">Leer</span><span class="sxs-lookup"><span data-stu-id="856bf-1023">Empty</span></span> | <span data-ttu-id="856bf-1024">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="856bf-1024">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="856bf-1025">Ein Delegat, der verwendet werden kann, um den `HttpMessageHandler` zum Senden von HTTP-Anforderungen verwendeten zu konfigurieren oder zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="856bf-1025">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="856bf-1026">Wird nicht für WebSocket-Verbindungen verwendet.</span><span class="sxs-lookup"><span data-stu-id="856bf-1026">Not used for WebSocket connections.</span></span> <span data-ttu-id="856bf-1027">Dieser Delegat muss einen Wert zurückgeben, der nicht NULL ist, und erhält den Standardwert als Parameter.</span><span class="sxs-lookup"><span data-stu-id="856bf-1027">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="856bf-1028">Ändern Sie die Einstellungen für diesen Standardwert, und geben Sie ihn zurück, `HttpMessageHandler` oder geben Sie eine neue Instanz zurück.</span><span class="sxs-lookup"><span data-stu-id="856bf-1028">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="856bf-1029">**Wenn Sie den Handler ersetzen, stellen Sie sicher, dass die Einstellungen, die Sie beibehalten möchten, vom bereitgestellten Handler kopiert werden. Andernfalls gelten die konfigurierten Optionen (z. b. Cookies und Header) nicht für den neuen Handler.**</span><span class="sxs-lookup"><span data-stu-id="856bf-1029">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="856bf-1030">Ein HTTP-Proxy, der beim Senden von HTTP-Anforderungen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-1030">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="856bf-1031">Legen Sie diesen booleschen Wert fest, um die Standard Anmelde Informationen für http-und websockets-Anforderungen zu senden</span><span class="sxs-lookup"><span data-stu-id="856bf-1031">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="856bf-1032">Dies ermöglicht die Verwendung der Windows-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="856bf-1032">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="856bf-1033">Ein Delegat, der zum Konfigurieren zusätzlicher WebSocket-Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="856bf-1033">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="856bf-1034">Empfängt eine Instanz von [clientwebsocketoptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , die zum Konfigurieren der Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="856bf-1034">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="856bf-1035">JavaScript</span><span class="sxs-lookup"><span data-stu-id="856bf-1035">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="856bf-1036">JavaScript-Option</span><span class="sxs-lookup"><span data-stu-id="856bf-1036">JavaScript Option</span></span> | <span data-ttu-id="856bf-1037">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-1037">Default Value</span></span> | <span data-ttu-id="856bf-1038">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="856bf-1038">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="856bf-1039">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="856bf-1039">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="856bf-1040">Legen Sie dies `true` auf fest, um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="856bf-1040">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="856bf-1041">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="856bf-1041">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="856bf-1042">Diese Einstellung kann nicht aktiviert werden, wenn der Azure signalr-Dienst verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-1042">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="856bf-1043">Java</span><span class="sxs-lookup"><span data-stu-id="856bf-1043">Java</span></span>](#tab/java)

| <span data-ttu-id="856bf-1044">Java-Option</span><span class="sxs-lookup"><span data-stu-id="856bf-1044">Java Option</span></span> | <span data-ttu-id="856bf-1045">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-1045">Default Value</span></span> | <span data-ttu-id="856bf-1046">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="856bf-1046">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="856bf-1047">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="856bf-1047">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="856bf-1048">Legen Sie dies `true` auf fest, um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="856bf-1048">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="856bf-1049">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="856bf-1049">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="856bf-1050">Diese Einstellung kann nicht aktiviert werden, wenn der Azure signalr-Dienst verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-1050">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="856bf-1051">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="856bf-1051">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="856bf-1052">Leer</span><span class="sxs-lookup"><span data-stu-id="856bf-1052">Empty</span></span> | <span data-ttu-id="856bf-1053">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="856bf-1053">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="856bf-1054">Im .NET-Client können diese Optionen durch den für `WithUrl`bereitgestellten Options Delegaten geändert werden:</span><span class="sxs-lookup"><span data-stu-id="856bf-1054">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="856bf-1055">Im JavaScript-Client können diese Optionen in einem JavaScript-Objekt bereitgestellt werden, `withUrl`das für Folgendes bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="856bf-1055">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="856bf-1056">Im Java-Client können diese Optionen mit den Methoden auf der `HttpHubConnectionBuilder` konfiguriert werden, die von der`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="856bf-1056">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="856bf-1057">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="856bf-1057">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="856bf-1058">JSON/messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="856bf-1058">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="856bf-1059">ASP.net Core signalr unterstützt zwei Protokolle für das Codieren von Nachrichten: [JSON](https://www.json.org/) und [messagepack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="856bf-1059">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="856bf-1060">Jedes Protokoll verfügt über Konfigurationsoptionen für die Serialisierung.</span><span class="sxs-lookup"><span data-stu-id="856bf-1060">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="856bf-1061">Die JSON-Serialisierung kann mithilfe der [addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) -Erweiterungsmethode auf dem Server konfiguriert werden, die nach [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in der `Startup.ConfigureServices` -Methode hinzugefügt werden kann.</span><span class="sxs-lookup"><span data-stu-id="856bf-1061">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="856bf-1062">Die `AddJsonProtocol` -Methode nimmt einen Delegaten an `options` , der ein-Objekt empfängt.</span><span class="sxs-lookup"><span data-stu-id="856bf-1062">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="856bf-1063">Die [payloadserializersettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) -Eigenschaft für dieses Objekt ist ein `JsonSerializerSettings` JSON.NET-Objekt, das zum Konfigurieren der Serialisierung von Argumenten und Rückgabe Werten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="856bf-1063">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="856bf-1064">Weitere Informationen finden Sie in der [JSON.NET-Dokumentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="856bf-1064">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="856bf-1065">Verwenden Sie beispielsweise den folgenden Code in `Startup.ConfigureServices`, um das Serialisierungsprogramm so zu konfigurieren, dass "PascalCase"-Eigenschaftsnamen anstelle der Standardnamen "CamelCase" verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="856bf-1065">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="856bf-1066">Im .NET-Client ist dieselbe `AddJsonProtocol` Erweiterungsmethode auf [hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)vorhanden.</span><span class="sxs-lookup"><span data-stu-id="856bf-1066">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="856bf-1067">Der `Microsoft.Extensions.DependencyInjection` Namespace muss importiert werden, um die Erweiterungsmethode aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="856bf-1067">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="856bf-1068">Zurzeit ist es nicht möglich, JSON-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="856bf-1068">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="856bf-1069">Messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="856bf-1069">MessagePack serialization options</span></span>

<span data-ttu-id="856bf-1070">Die messagepack-Serialisierung kann konfiguriert werden, indem ein Delegat für den [addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) -Aufruf bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-1070">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="856bf-1071">Weitere Informationen finden Sie [unter messagepack in signalr](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="856bf-1071">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="856bf-1072">Zurzeit ist es nicht möglich, die messagepack-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="856bf-1072">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="856bf-1073">Konfigurieren von Serveroptionen</span><span class="sxs-lookup"><span data-stu-id="856bf-1073">Configure server options</span></span>

<span data-ttu-id="856bf-1074">In der folgenden Tabelle werden die Optionen zum Konfigurieren von signalr Hubs beschrieben:</span><span class="sxs-lookup"><span data-stu-id="856bf-1074">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="856bf-1075">Option</span><span class="sxs-lookup"><span data-stu-id="856bf-1075">Option</span></span> | <span data-ttu-id="856bf-1076">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-1076">Default Value</span></span> | <span data-ttu-id="856bf-1077">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="856bf-1077">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="856bf-1078">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="856bf-1078">15 seconds</span></span> | <span data-ttu-id="856bf-1079">Wenn der Client innerhalb dieses Zeitraums keine anfängliche Hand Shake Nachricht sendet, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="856bf-1079">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="856bf-1080">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="856bf-1080">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="856bf-1081">Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="856bf-1081">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="856bf-1082">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="856bf-1082">15 seconds</span></span> | <span data-ttu-id="856bf-1083">Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten.</span><span class="sxs-lookup"><span data-stu-id="856bf-1083">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="856bf-1084">Ändern Sie `KeepAliveInterval`die `ServerTimeout` / `serverTimeoutInMilliseconds` Einstellung auf dem Client, wenn Sie sich ändern.</span><span class="sxs-lookup"><span data-stu-id="856bf-1084">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="856bf-1085">Der empfohlene `ServerTimeout` / `serverTimeoutInMilliseconds` Wert ist Double- `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="856bf-1085">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="856bf-1086">Alle installierten Protokolle</span><span class="sxs-lookup"><span data-stu-id="856bf-1086">All installed protocols</span></span> | <span data-ttu-id="856bf-1087">Protokolle, die von diesem Hub unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-1087">Protocols supported by this hub.</span></span> <span data-ttu-id="856bf-1088">Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, aber Protokolle können aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="856bf-1088">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="856bf-1089">Wenn `true`der Wert ist, werden ausführliche Ausnahme Meldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-1089">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="856bf-1090">Der Standardwert `false`ist, da diese Ausnahme Meldungen vertrauliche Informationen enthalten können.</span><span class="sxs-lookup"><span data-stu-id="856bf-1090">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="856bf-1091">Optionen können für alle Hubs konfiguriert werden, indem ein Options Delegat für `AddSignalR` den Aufruf `Startup.ConfigureServices`in bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-1091">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="856bf-1092">Optionen für einen einzelnen Hub überschreiben die in `AddSignalR` bereitgestellten globalen Optionen und können mithilfe <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>von konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="856bf-1092">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="856bf-1093">Erweiterte http-Konfigurationsoptionen</span><span class="sxs-lookup"><span data-stu-id="856bf-1093">Advanced HTTP configuration options</span></span>

<span data-ttu-id="856bf-1094">Verwenden `HttpConnectionDispatcherOptions` Sie, um erweiterte Einstellungen im Zusammenhang mit Transporte und der Speicherpuffer Verwaltung zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="856bf-1094">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="856bf-1095">Diese Optionen werden konfiguriert, indem ein [Delegat an\<maphub T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-1095">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="856bf-1096">In der folgenden Tabelle werden die Optionen zum Konfigurieren der erweiterten http-Optionen von ASP.net Core signalr beschrieben:</span><span class="sxs-lookup"><span data-stu-id="856bf-1096">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="856bf-1097">Option</span><span class="sxs-lookup"><span data-stu-id="856bf-1097">Option</span></span> | <span data-ttu-id="856bf-1098">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-1098">Default Value</span></span> | <span data-ttu-id="856bf-1099">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="856bf-1099">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="856bf-1100">32 KB</span><span class="sxs-lookup"><span data-stu-id="856bf-1100">32 KB</span></span> | <span data-ttu-id="856bf-1101">Die maximale Anzahl von Bytes, die vom Client empfangen werden, der vom Server gepuffert wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-1101">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="856bf-1102">Eine Erhöhung dieses Werts ermöglicht es dem Server, größere Nachrichten zu empfangen, kann sich jedoch negativ auf den Arbeitsspeicherverbrauch auswirken.</span><span class="sxs-lookup"><span data-stu-id="856bf-1102">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="856bf-1103">Daten, die automatisch von `Authorize` den auf die Hub-Klasse angewendeten Attributen gesammelt werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-1103">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="856bf-1104">Eine Liste von [iautorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) -Objekten, die verwendet werden, um zu bestimmen, ob ein Client für die Verbindung mit dem Hub autorisiert ist.</span><span class="sxs-lookup"><span data-stu-id="856bf-1104">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="856bf-1105">32 KB</span><span class="sxs-lookup"><span data-stu-id="856bf-1105">32 KB</span></span> | <span data-ttu-id="856bf-1106">Die maximale Anzahl von Bytes, die von der APP gesendet werden, die der Server puffert.</span><span class="sxs-lookup"><span data-stu-id="856bf-1106">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="856bf-1107">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten senden, kann sich jedoch negativ auf die Arbeitsspeicher Auslastung auswirken.</span><span class="sxs-lookup"><span data-stu-id="856bf-1107">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="856bf-1108">Alle Transporte sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="856bf-1108">All Transports are enabled.</span></span> | <span data-ttu-id="856bf-1109">Ein Bitflags-Enumeration `HttpTransportType` von Werten, die die Transporte einschränken können, die ein Client für die Verbindungs Herstellung verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="856bf-1109">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="856bf-1110">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="856bf-1110">See below.</span></span> | <span data-ttu-id="856bf-1111">Zusätzliche Optionen, die für den langen Abruf Transport spezifisch sind.</span><span class="sxs-lookup"><span data-stu-id="856bf-1111">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="856bf-1112">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="856bf-1112">See below.</span></span> | <span data-ttu-id="856bf-1113">Zusätzliche Optionen für den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="856bf-1113">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="856bf-1114">Der lange Abruf Transport bietet zusätzliche Optionen, die mit der `LongPolling` -Eigenschaft konfiguriert werden können:</span><span class="sxs-lookup"><span data-stu-id="856bf-1114">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="856bf-1115">Option</span><span class="sxs-lookup"><span data-stu-id="856bf-1115">Option</span></span> | <span data-ttu-id="856bf-1116">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-1116">Default Value</span></span> | <span data-ttu-id="856bf-1117">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="856bf-1117">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="856bf-1118">90 Sekunden</span><span class="sxs-lookup"><span data-stu-id="856bf-1118">90 seconds</span></span> | <span data-ttu-id="856bf-1119">Die maximale Zeitspanne, die der Server wartet, bis eine Nachricht an den Client gesendet wird, bevor eine einzelne Abruf Anforderung beendet wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-1119">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="856bf-1120">Das verringern dieses Werts bewirkt, dass der Client neue Abruf Anforderungen häufiger ausgibt.</span><span class="sxs-lookup"><span data-stu-id="856bf-1120">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="856bf-1121">Der WebSocket-Transport verfügt über zusätzliche Optionen, die mit der `WebSockets` -Eigenschaft konfiguriert werden können:</span><span class="sxs-lookup"><span data-stu-id="856bf-1121">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="856bf-1122">Option</span><span class="sxs-lookup"><span data-stu-id="856bf-1122">Option</span></span> | <span data-ttu-id="856bf-1123">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-1123">Default Value</span></span> | <span data-ttu-id="856bf-1124">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="856bf-1124">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="856bf-1125">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="856bf-1125">5 seconds</span></span> | <span data-ttu-id="856bf-1126">Wenn der Client nach dem Schließen des Servers nicht innerhalb dieses Zeitraums geschlossen werden kann, wird die Verbindung beendet.</span><span class="sxs-lookup"><span data-stu-id="856bf-1126">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="856bf-1127">Ein Delegat, der verwendet werden kann, `Sec-WebSocket-Protocol` um den-Header auf einen benutzerdefinierten Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="856bf-1127">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="856bf-1128">Der Delegat empfängt die vom Client angeforderten Werte als Eingabe und erwartet, dass der gewünschte Wert zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-1128">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="856bf-1129">Konfigurieren von Clientoptionen</span><span class="sxs-lookup"><span data-stu-id="856bf-1129">Configure client options</span></span>

<span data-ttu-id="856bf-1130">Client Optionen können für den `HubConnectionBuilder` Typ konfiguriert werden (verfügbar in den .net-und JavaScript-Clients).</span><span class="sxs-lookup"><span data-stu-id="856bf-1130">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="856bf-1131">Sie steht auch im Java-Client zur Verfügung, aber `HttpHubConnectionBuilder` die Unterklasse enthält die Generator-Konfigurationsoptionen sowie die `HubConnection` selbst.</span><span class="sxs-lookup"><span data-stu-id="856bf-1131">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="856bf-1132">Konfigurieren der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="856bf-1132">Configure logging</span></span>

<span data-ttu-id="856bf-1133">Die Protokollierung wird mithilfe der `ConfigureLogging` -Methode im .NET-Client konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="856bf-1133">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="856bf-1134">Protokollierungs Anbieter und Filter können auf die gleiche Weise wie auf dem Server registriert werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-1134">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="856bf-1135">Weitere Informationen finden Sie in der Dokumentation zur [Protokollierung ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="856bf-1135">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="856bf-1136">Um Protokollierungs Anbieter zu registrieren, müssen Sie die erforderlichen Pakete installieren.</span><span class="sxs-lookup"><span data-stu-id="856bf-1136">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="856bf-1137">Eine vollständige Liste finden Sie im Abschnitt [integrierte Protokollierungs Anbieter](xref:fundamentals/logging/index#built-in-logging-providers) der Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="856bf-1137">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="856bf-1138">Um z. b. die Konsolen Protokollierung zu `Microsoft.Extensions.Logging.Console` aktivieren, installieren Sie das nuget-Paket.</span><span class="sxs-lookup"><span data-stu-id="856bf-1138">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="856bf-1139">Die `AddConsole` Erweiterungsmethode aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="856bf-1139">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="856bf-1140">Im JavaScript-Client ist eine ähnliche `configureLogging` Methode vorhanden.</span><span class="sxs-lookup"><span data-stu-id="856bf-1140">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="856bf-1141">Geben Sie `LogLevel` einen Wert an, der die minimale Ebene der zu erstellenden Protokollmeldungen angibt.</span><span class="sxs-lookup"><span data-stu-id="856bf-1141">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="856bf-1142">Protokolle werden in das Browser Konsolenfenster geschrieben.</span><span class="sxs-lookup"><span data-stu-id="856bf-1142">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="856bf-1143">Um die Protokollierung vollständig `signalR.LogLevel.None` zu deaktivieren `configureLogging` , geben Sie in der Methode an</span><span class="sxs-lookup"><span data-stu-id="856bf-1143">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="856bf-1144">Weitere Informationen zur Protokollierung finden Sie in der [signalr-Diagnose Dokumentation](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="856bf-1144">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="856bf-1145">Der signalr-Java-Client verwendet die [SLF4J](https://www.slf4j.org/) -Bibliothek für die Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="856bf-1145">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="856bf-1146">Dabei handelt es sich um eine allgemeine Protokollierungs-API, die es Benutzern der Bibliothek ermöglicht, ihre eigene spezifische Protokollierungs Implementierung zu wählen, indem Sie eine bestimmte Protokollierungs Abhängigkeit einbinden.</span><span class="sxs-lookup"><span data-stu-id="856bf-1146">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="856bf-1147">Der folgende Code Ausschnitt zeigt, wie mit dem `java.util.logging` signalr-Java-Client verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-1147">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="856bf-1148">Wenn Sie die Protokollierung in ihren Abhängigkeiten nicht konfigurieren, lädt SLF4J eine Standard Protokollierung ohne Vorgang mit folgender Warnmeldung:</span><span class="sxs-lookup"><span data-stu-id="856bf-1148">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="856bf-1149">Dies kann sicher ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-1149">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="856bf-1150">Zulässige Transporte konfigurieren</span><span class="sxs-lookup"><span data-stu-id="856bf-1150">Configure allowed transports</span></span>

<span data-ttu-id="856bf-1151">Die von signalr verwendeten Transporte können im `WithUrl` -Befehl (`withUrl` in JavaScript) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-1151">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="856bf-1152">Ein bitweises OR der Werte von `HttpTransportType` kann verwendet werden, um den Client so einzuschränken, dass nur die angegebenen Transporte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-1152">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="856bf-1153">Alle Transporte sind standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="856bf-1153">All transports are enabled by default.</span></span>

<span data-ttu-id="856bf-1154">So können Sie beispielsweise den Transport von Server gesendeten Ereignissen deaktivieren, aber websockets und lange Abruf Verbindungen zulassen:</span><span class="sxs-lookup"><span data-stu-id="856bf-1154">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="856bf-1155">Im JavaScript-Client werden Transporte durch Festlegen des-Felds `transport` für das Options-Objekt konfiguriert, `withUrl`das für bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="856bf-1155">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="856bf-1156">Konfigurieren der bearerauthentifizierung</span><span class="sxs-lookup"><span data-stu-id="856bf-1156">Configure bearer authentication</span></span>

<span data-ttu-id="856bf-1157">Um Authentifizierungsdaten zusammen mit signalr-Anforderungen bereitzustellen, `AccessTokenProvider` verwenden Sie`accessTokenFactory` die-Option (in JavaScript), um eine Funktion anzugeben, die das gewünschte Zugriffs Token zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="856bf-1157">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="856bf-1158">Im .NET-Client wird dieses Zugriffs Token als http-tokenauthentifizierungstoken (mit dem- `Authorization` Header mit dem Typ `Bearer`) an das Token "bearerauthentifizierung" geleitet.</span><span class="sxs-lookup"><span data-stu-id="856bf-1158">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="856bf-1159">Im JavaScript-Client wird das Zugriffs Token als bearertoken verwendet, **außer** in einigen Fällen, in denen Browser-APIs die Fähigkeit zum Anwenden von Headern einschränken (insbesondere bei vom Server gesendeten Ereignissen und websockets-Anforderungen).</span><span class="sxs-lookup"><span data-stu-id="856bf-1159">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="856bf-1160">In diesen Fällen wird das Zugriffs Token als Abfrage Zeichenfolgen-Wert `access_token`bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="856bf-1160">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="856bf-1161">Im .NET-Client kann die `AccessTokenProvider` Option mithilfe des Options Delegaten in `WithUrl`angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="856bf-1161">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="856bf-1162">Im JavaScript-Client wird das Zugriffs Token konfiguriert, indem das `accessTokenFactory` -Feld für das Options-Objekt `withUrl`in festgelegt wird:</span><span class="sxs-lookup"><span data-stu-id="856bf-1162">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="856bf-1163">Im signalr-Java-Client können Sie ein bearertoken für die Authentifizierung konfigurieren, indem Sie eine zugriffstokenfactory für " [httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)" bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="856bf-1163">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="856bf-1164">Verwenden Sie [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , um eine einzelne [RxJava](https://github.com/ReactiveX/RxJava) [\< ](https://reactivex.io/documentation/single.html)rxjava-Zeichenfolge>bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="856bf-1164">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="856bf-1165">Beim Aufrufen von " [Single.](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)Write" können Sie Logik schreiben, um Zugriffs Token für den Client zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="856bf-1165">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="856bf-1166">Konfigurieren von Timeout-und Keep-Alive-Optionen</span><span class="sxs-lookup"><span data-stu-id="856bf-1166">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="856bf-1167">Zusätzliche Optionen zum Konfigurieren von Timeout und Keep-Alive-Verhalten sind für `HubConnection` das Objekt selbst verfügbar:</span><span class="sxs-lookup"><span data-stu-id="856bf-1167">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="856bf-1168">.NET</span><span class="sxs-lookup"><span data-stu-id="856bf-1168">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="856bf-1169">Option</span><span class="sxs-lookup"><span data-stu-id="856bf-1169">Option</span></span> | <span data-ttu-id="856bf-1170">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-1170">Default value</span></span> | <span data-ttu-id="856bf-1171">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="856bf-1171">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="856bf-1172">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="856bf-1172">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="856bf-1173">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="856bf-1173">Timeout for server activity.</span></span> <span data-ttu-id="856bf-1174">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst `Closed` das Ereignis`onclose` aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="856bf-1174">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="856bf-1175">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-1175">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="856bf-1176">Der empfohlene Wert ist eine Zahl, die mindestens dem `KeepAliveInterval` Wert des Servers entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="856bf-1176">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="856bf-1177">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="856bf-1177">15 seconds</span></span> | <span data-ttu-id="856bf-1178">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="856bf-1178">Timeout for initial server handshake.</span></span> <span data-ttu-id="856bf-1179">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `Closed` Ereignis aus`onclose` (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="856bf-1179">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="856bf-1180">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="856bf-1180">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="856bf-1181">Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="856bf-1181">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="856bf-1182">Im .NET-Client werden Timeout Werte als `TimeSpan` Werte angegeben.</span><span class="sxs-lookup"><span data-stu-id="856bf-1182">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="856bf-1183">JavaScript</span><span class="sxs-lookup"><span data-stu-id="856bf-1183">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="856bf-1184">Option</span><span class="sxs-lookup"><span data-stu-id="856bf-1184">Option</span></span> | <span data-ttu-id="856bf-1185">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-1185">Default value</span></span> | <span data-ttu-id="856bf-1186">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="856bf-1186">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="856bf-1187">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="856bf-1187">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="856bf-1188">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="856bf-1188">Timeout for server activity.</span></span> <span data-ttu-id="856bf-1189">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst `onclose` das Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="856bf-1189">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="856bf-1190">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-1190">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="856bf-1191">Der empfohlene Wert ist eine Zahl, die mindestens dem `KeepAliveInterval` Wert des Servers entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="856bf-1191">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="856bf-1192">Java</span><span class="sxs-lookup"><span data-stu-id="856bf-1192">Java</span></span>](#tab/java)

| <span data-ttu-id="856bf-1193">Option</span><span class="sxs-lookup"><span data-stu-id="856bf-1193">Option</span></span> | <span data-ttu-id="856bf-1194">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-1194">Default value</span></span> | <span data-ttu-id="856bf-1195">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="856bf-1195">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="856bf-1196">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="856bf-1196">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="856bf-1197">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="856bf-1197">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="856bf-1198">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="856bf-1198">Timeout for server activity.</span></span> <span data-ttu-id="856bf-1199">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst `onClose` das Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="856bf-1199">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="856bf-1200">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-1200">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="856bf-1201">Der empfohlene Wert ist eine Zahl, die mindestens dem `KeepAliveInterval` Wert des Servers entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="856bf-1201">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="856bf-1202">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="856bf-1202">15 seconds</span></span> | <span data-ttu-id="856bf-1203">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="856bf-1203">Timeout for initial server handshake.</span></span> <span data-ttu-id="856bf-1204">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `onClose` -Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="856bf-1204">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="856bf-1205">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="856bf-1205">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="856bf-1206">Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="856bf-1206">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="856bf-1207">Konfigurieren zusätzlicher Optionen</span><span class="sxs-lookup"><span data-stu-id="856bf-1207">Configure additional options</span></span>

<span data-ttu-id="856bf-1208">`WithUrl` Zusätzliche Optionen können in der-Methode (`withUrl` in JavaScript) für `HubConnectionBuilder` oder für die verschiedenen Konfigurations-APIs auf dem `HttpHubConnectionBuilder` im Java-Client konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="856bf-1208">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="856bf-1209">.NET</span><span class="sxs-lookup"><span data-stu-id="856bf-1209">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="856bf-1210">.Net-Option</span><span class="sxs-lookup"><span data-stu-id="856bf-1210">.NET Option</span></span> |  <span data-ttu-id="856bf-1211">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-1211">Default value</span></span> | <span data-ttu-id="856bf-1212">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="856bf-1212">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="856bf-1213">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="856bf-1213">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="856bf-1214">Legen Sie dies `true` auf fest, um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="856bf-1214">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="856bf-1215">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="856bf-1215">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="856bf-1216">Diese Einstellung kann nicht aktiviert werden, wenn der Azure signalr-Dienst verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-1216">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="856bf-1217">Leer</span><span class="sxs-lookup"><span data-stu-id="856bf-1217">Empty</span></span> | <span data-ttu-id="856bf-1218">Eine Auflistung von TLS-Zertifikaten, die zum Authentifizieren von Anforderungen gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="856bf-1218">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="856bf-1219">Leer</span><span class="sxs-lookup"><span data-stu-id="856bf-1219">Empty</span></span> | <span data-ttu-id="856bf-1220">Eine Auflistung von HTTP-Cookies, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="856bf-1220">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="856bf-1221">Leer</span><span class="sxs-lookup"><span data-stu-id="856bf-1221">Empty</span></span> | <span data-ttu-id="856bf-1222">Anmelde Informationen, die mit jeder HTTP-Anforderung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="856bf-1222">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="856bf-1223">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="856bf-1223">5 seconds</span></span> | <span data-ttu-id="856bf-1224">Nur websockets.</span><span class="sxs-lookup"><span data-stu-id="856bf-1224">WebSockets only.</span></span> <span data-ttu-id="856bf-1225">Die maximale Zeitspanne, die der Client nach dem Schließen des Servers wartet, um die Anforderung zum Schließen zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="856bf-1225">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="856bf-1226">Wenn der Server die Schließung innerhalb dieses Zeitraums nicht anerkennt, trennt der Client die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="856bf-1226">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="856bf-1227">Leer</span><span class="sxs-lookup"><span data-stu-id="856bf-1227">Empty</span></span> | <span data-ttu-id="856bf-1228">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="856bf-1228">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="856bf-1229">Ein Delegat, der verwendet werden kann, um den `HttpMessageHandler` zum Senden von HTTP-Anforderungen verwendeten zu konfigurieren oder zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="856bf-1229">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="856bf-1230">Wird nicht für WebSocket-Verbindungen verwendet.</span><span class="sxs-lookup"><span data-stu-id="856bf-1230">Not used for WebSocket connections.</span></span> <span data-ttu-id="856bf-1231">Dieser Delegat muss einen Wert zurückgeben, der nicht NULL ist, und erhält den Standardwert als Parameter.</span><span class="sxs-lookup"><span data-stu-id="856bf-1231">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="856bf-1232">Ändern Sie die Einstellungen für diesen Standardwert, und geben Sie ihn zurück, `HttpMessageHandler` oder geben Sie eine neue Instanz zurück.</span><span class="sxs-lookup"><span data-stu-id="856bf-1232">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="856bf-1233">**Wenn Sie den Handler ersetzen, stellen Sie sicher, dass die Einstellungen, die Sie beibehalten möchten, vom bereitgestellten Handler kopiert werden. Andernfalls gelten die konfigurierten Optionen (z. b. Cookies und Header) nicht für den neuen Handler.**</span><span class="sxs-lookup"><span data-stu-id="856bf-1233">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="856bf-1234">Ein HTTP-Proxy, der beim Senden von HTTP-Anforderungen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-1234">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="856bf-1235">Legen Sie diesen booleschen Wert fest, um die Standard Anmelde Informationen für http-und websockets-Anforderungen zu senden</span><span class="sxs-lookup"><span data-stu-id="856bf-1235">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="856bf-1236">Dies ermöglicht die Verwendung der Windows-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="856bf-1236">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="856bf-1237">Ein Delegat, der zum Konfigurieren zusätzlicher WebSocket-Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="856bf-1237">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="856bf-1238">Empfängt eine Instanz von [clientwebsocketoptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , die zum Konfigurieren der Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="856bf-1238">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="856bf-1239">JavaScript</span><span class="sxs-lookup"><span data-stu-id="856bf-1239">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="856bf-1240">JavaScript-Option</span><span class="sxs-lookup"><span data-stu-id="856bf-1240">JavaScript Option</span></span> | <span data-ttu-id="856bf-1241">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-1241">Default Value</span></span> | <span data-ttu-id="856bf-1242">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="856bf-1242">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="856bf-1243">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="856bf-1243">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="856bf-1244">Legen Sie dies `true` auf fest, um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="856bf-1244">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="856bf-1245">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="856bf-1245">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="856bf-1246">Diese Einstellung kann nicht aktiviert werden, wenn der Azure signalr-Dienst verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-1246">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="856bf-1247">Java</span><span class="sxs-lookup"><span data-stu-id="856bf-1247">Java</span></span>](#tab/java)

| <span data-ttu-id="856bf-1248">Java-Option</span><span class="sxs-lookup"><span data-stu-id="856bf-1248">Java Option</span></span> | <span data-ttu-id="856bf-1249">Standardwert</span><span class="sxs-lookup"><span data-stu-id="856bf-1249">Default Value</span></span> | <span data-ttu-id="856bf-1250">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="856bf-1250">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="856bf-1251">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="856bf-1251">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="856bf-1252">Legen Sie dies `true` auf fest, um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="856bf-1252">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="856bf-1253">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="856bf-1253">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="856bf-1254">Diese Einstellung kann nicht aktiviert werden, wenn der Azure signalr-Dienst verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="856bf-1254">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="856bf-1255">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="856bf-1255">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="856bf-1256">Leer</span><span class="sxs-lookup"><span data-stu-id="856bf-1256">Empty</span></span> | <span data-ttu-id="856bf-1257">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="856bf-1257">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="856bf-1258">Im .NET-Client können diese Optionen durch den für `WithUrl`bereitgestellten Options Delegaten geändert werden:</span><span class="sxs-lookup"><span data-stu-id="856bf-1258">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="856bf-1259">Im JavaScript-Client können diese Optionen in einem JavaScript-Objekt bereitgestellt werden, `withUrl`das für Folgendes bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="856bf-1259">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="856bf-1260">Im Java-Client können diese Optionen mit den Methoden auf der `HttpHubConnectionBuilder` konfiguriert werden, die von der`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="856bf-1260">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="856bf-1261">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="856bf-1261">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end

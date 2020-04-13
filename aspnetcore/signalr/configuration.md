---
title: ASP.NET SignalR Kernkonfiguration
author: bradygaster
description: Erfahren Sie, wie SignalR Sie ASP.NET Core-Apps konfigurieren.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 2e9fda6d57986171fc375a2e0fdebf9e111218e0
ms.sourcegitcommit: 6f1b516e0c899a49afe9a29044a2383ce2ada3c7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81223984"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="34952-103">ASP.NET Core SignalR-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="34952-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="34952-104">JSON/MessagePack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="34952-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="34952-105">ASP.NET Core SignalR unterstützt zwei Protokolle für die Codierung von Nachrichten: [JSON](https://www.json.org/) und [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="34952-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="34952-106">Jedes Protokoll verfügt über Serialisierungskonfigurationsoptionen.</span><span class="sxs-lookup"><span data-stu-id="34952-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="34952-107">Die JSON-Serialisierung kann auf dem Server mithilfe der [AddJsonProtocol-Erweiterungsmethode](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="34952-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="34952-108">`AddJsonProtocol`kann nach [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) `Startup.ConfigureServices`in hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="34952-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="34952-109">Die `AddJsonProtocol` Methode nimmt einen `options` Delegaten an, der ein Objekt empfängt.</span><span class="sxs-lookup"><span data-stu-id="34952-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="34952-110">Die [PayloadSerializerOptions-Eigenschaft](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) für `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> dieses Objekt ist ein Objekt, das zum Konfigurieren der Serialisierung von Argumenten und Rückgabewerten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="34952-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="34952-111">Weitere Informationen finden Sie in der [Dokumentation System.Text.Json](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="34952-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="34952-112">Verwenden Sie beispielsweise den folgenden Code, `Startup.ConfigureServices`um den Serialisierungsmodul so zu konfigurieren, dass das Groß-/Kleinschreibung von Eigenschaftsnamen anstelle der standardmäßigen "camelCase"-Namen nicht geändert wird:</span><span class="sxs-lookup"><span data-stu-id="34952-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="34952-113">Im .NET-Client ist `AddJsonProtocol` dieselbe Erweiterungsmethode auf [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)vorhanden.</span><span class="sxs-lookup"><span data-stu-id="34952-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="34952-114">Der `Microsoft.Extensions.DependencyInjection` Namespace muss importiert werden, um die Erweiterungsmethode aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="34952-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="34952-115">Es ist derzeit nicht möglich, die JSON-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="34952-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="34952-116">Wechseln zu Newtonsoft.Json</span><span class="sxs-lookup"><span data-stu-id="34952-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="34952-117">Wenn Sie Funktionen `Newtonsoft.Json` benötigen, die in `System.Text.Json`nicht unterstützt werden, siehe [Wechseln zu Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="34952-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="34952-118">MessagePack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="34952-118">MessagePack serialization options</span></span>

<span data-ttu-id="34952-119">Die MessagePack-Serialisierung kann konfiguriert werden, indem ein Delegat für den [AddMessagePackProtocol-Aufruf](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="34952-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="34952-120">Weitere Informationen finden Sie [unter MessagePack in SignalR.](xref:signalr/messagepackhubprotocol)</span><span class="sxs-lookup"><span data-stu-id="34952-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="34952-121">Es ist derzeit nicht möglich, die MessagePack-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="34952-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="34952-122">Konfigurieren von Serveroptionen</span><span class="sxs-lookup"><span data-stu-id="34952-122">Configure server options</span></span>

<span data-ttu-id="34952-123">In der folgenden Tabelle werden Optionen zum Konfigurieren von SignalR-Hubs beschrieben:</span><span class="sxs-lookup"><span data-stu-id="34952-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="34952-124">Option</span><span class="sxs-lookup"><span data-stu-id="34952-124">Option</span></span> | <span data-ttu-id="34952-125">Standardwert</span><span class="sxs-lookup"><span data-stu-id="34952-125">Default Value</span></span> | <span data-ttu-id="34952-126">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="34952-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="34952-127">30 Sekunden</span><span class="sxs-lookup"><span data-stu-id="34952-127">30 seconds</span></span> | <span data-ttu-id="34952-128">Der Server betrachtet die Verbindung des Clients, wenn er in diesem Intervall keine Nachricht (einschließlich Keep-Alive) erhalten hat.</span><span class="sxs-lookup"><span data-stu-id="34952-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="34952-129">Es kann länger dauern als dieses Timeoutintervall, bis der Client aufgrund der Implementierung tatsächlich als getrennt markiert wird.</span><span class="sxs-lookup"><span data-stu-id="34952-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="34952-130">Der empfohlene Wert `KeepAliveInterval` ist das Doppelte des Wertes.</span><span class="sxs-lookup"><span data-stu-id="34952-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="34952-131">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="34952-131">15 seconds</span></span> | <span data-ttu-id="34952-132">Wenn der Client innerhalb dieses Zeitintervalls keine erste Handshake-Nachricht sendet, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="34952-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="34952-133">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn Handshake-Timeoutfehler aufgrund einer schwerwiegenden Netzwerklatenz auftreten.</span><span class="sxs-lookup"><span data-stu-id="34952-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="34952-134">Weitere Informationen zum Handshake-Prozess finden Sie in der [SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="34952-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="34952-135">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="34952-135">15 seconds</span></span> | <span data-ttu-id="34952-136">Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten.</span><span class="sxs-lookup"><span data-stu-id="34952-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="34952-137">Ändern `KeepAliveInterval`Sie beim `ServerTimeout` / `serverTimeoutInMilliseconds` Ändern die Einstellung auf dem Client.</span><span class="sxs-lookup"><span data-stu-id="34952-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="34952-138">Der `ServerTimeout` / `serverTimeoutInMilliseconds` empfohlene Wert `KeepAliveInterval` ist das Doppelte des Wertes.</span><span class="sxs-lookup"><span data-stu-id="34952-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="34952-139">Alle installierten Protokolle</span><span class="sxs-lookup"><span data-stu-id="34952-139">All installed protocols</span></span> | <span data-ttu-id="34952-140">Protokolle, die von diesem Hub unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="34952-140">Protocols supported by this hub.</span></span> <span data-ttu-id="34952-141">Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, Protokolle können jedoch aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="34952-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="34952-142">Wenn `true`, werden detaillierte Ausnahmemeldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="34952-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="34952-143">Der Standardwert ist `false`, da diese Ausnahmemeldungen vertrauliche Informationen enthalten können.</span><span class="sxs-lookup"><span data-stu-id="34952-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="34952-144">Die maximale Anzahl von Elementen, die für Client-Upload-Streams gepuffert werden können.</span><span class="sxs-lookup"><span data-stu-id="34952-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="34952-145">Wenn dieser Grenzwert erreicht ist, wird die Verarbeitung von Aufrufen blockiert, bis der Server Streamelemente verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="34952-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="34952-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="34952-146">32 KB</span></span> | <span data-ttu-id="34952-147">Maximale Größe einer einzelnen eingehenden Hubnachricht.</span><span class="sxs-lookup"><span data-stu-id="34952-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="34952-148">Optionen können für alle Hubs konfiguriert werden, `AddSignalR` indem `Startup.ConfigureServices`ein Optionsdelegat für den Aufruf in zur Verfügung gestellt wird.</span><span class="sxs-lookup"><span data-stu-id="34952-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="34952-149">Optionen für einen einzelnen Hub überschreiben `AddSignalR` die globalen Optionen, die in bereitgestellt werden und können mit <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>folgenden Optionen konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="34952-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="34952-150">Erweiterte HTTP-Konfigurationsoptionen</span><span class="sxs-lookup"><span data-stu-id="34952-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="34952-151">Verwenden `HttpConnectionDispatcherOptions` Sie diese Datei zum Konfigurieren erweiterter Einstellungen für Transporte und Speicherpufferverwaltung.</span><span class="sxs-lookup"><span data-stu-id="34952-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="34952-152">Diese Optionen werden konfiguriert, indem sie einen `Startup.Configure`Delegaten an [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in übergeben.</span><span class="sxs-lookup"><span data-stu-id="34952-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="34952-153">In der folgenden Tabelle werden Optionen zum Konfigurieren ASP.NET erweiterten HTTP-Optionen von Core SignalR beschrieben:</span><span class="sxs-lookup"><span data-stu-id="34952-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="34952-154">Option</span><span class="sxs-lookup"><span data-stu-id="34952-154">Option</span></span> | <span data-ttu-id="34952-155">Standardwert</span><span class="sxs-lookup"><span data-stu-id="34952-155">Default Value</span></span> | <span data-ttu-id="34952-156">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="34952-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="34952-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="34952-157">32 KB</span></span> | <span data-ttu-id="34952-158">Die maximale Anzahl von Bytes, die vom Client empfangen werden, die der Server puffert, bevor der Gegendruck angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="34952-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="34952-159">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten schneller empfangen, ohne Gegendruck anzuwenden, kann jedoch den Speicherverbrauch erhöhen.</span><span class="sxs-lookup"><span data-stu-id="34952-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="34952-160">Daten, die `Authorize` automatisch aus den Attributen gesammelt werden, die auf die Hub-Klasse angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="34952-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="34952-161">Eine Liste von [IAuthorizeData-Objekten,](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) die verwendet werden, um zu bestimmen, ob ein Client zum Herstellen einer Verbindung mit dem Hub autorisiert ist.</span><span class="sxs-lookup"><span data-stu-id="34952-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="34952-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="34952-162">32 KB</span></span> | <span data-ttu-id="34952-163">Die maximale Anzahl von Bytes, die von der App gesendet werden, die der Server puffert, bevor die Rückdruckmenge beobachtet wird.</span><span class="sxs-lookup"><span data-stu-id="34952-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="34952-164">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten schneller puffern, ohne auf Gegendruck zu warten, kann jedoch den Speicherverbrauch erhöhen.</span><span class="sxs-lookup"><span data-stu-id="34952-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="34952-165">Alle Transporte sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="34952-165">All Transports are enabled.</span></span> | <span data-ttu-id="34952-166">Eine Bit-Flags-Enumere von `HttpTransportType` Werten, die die Transporte einschränken können, die ein Client zum Herstellen einer Verbindung verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="34952-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="34952-167">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="34952-167">See below.</span></span> | <span data-ttu-id="34952-168">Zusätzliche Optionen speziell für den Long Polling-Transport.</span><span class="sxs-lookup"><span data-stu-id="34952-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="34952-169">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="34952-169">See below.</span></span> | <span data-ttu-id="34952-170">Zusätzliche Optionen, die für den WebSockets-Transport spezifisch sind.</span><span class="sxs-lookup"><span data-stu-id="34952-170">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="34952-171">Der Long Polling-Transport verfügt über zusätzliche `LongPolling` Optionen, die mithilfe der Eigenschaft konfiguriert werden können:</span><span class="sxs-lookup"><span data-stu-id="34952-171">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="34952-172">Option</span><span class="sxs-lookup"><span data-stu-id="34952-172">Option</span></span> | <span data-ttu-id="34952-173">Standardwert</span><span class="sxs-lookup"><span data-stu-id="34952-173">Default Value</span></span> | <span data-ttu-id="34952-174">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="34952-174">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="34952-175">90 Sekunden</span><span class="sxs-lookup"><span data-stu-id="34952-175">90 seconds</span></span> | <span data-ttu-id="34952-176">Die maximale Zeit, die der Server auf das Senden einer Nachricht an den Client wartet, bevor eine einzelne Abfrageanforderung beendet wird.</span><span class="sxs-lookup"><span data-stu-id="34952-176">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="34952-177">Wenn Sie diesen Wert verringern, gibt der Client häufiger neue Abfrageanforderungen aus.</span><span class="sxs-lookup"><span data-stu-id="34952-177">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="34952-178">Der WebSocket-Transport verfügt über zusätzliche Optionen, die mithilfe der `WebSockets` Eigenschaft konfiguriert werden können:</span><span class="sxs-lookup"><span data-stu-id="34952-178">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="34952-179">Option</span><span class="sxs-lookup"><span data-stu-id="34952-179">Option</span></span> | <span data-ttu-id="34952-180">Standardwert</span><span class="sxs-lookup"><span data-stu-id="34952-180">Default Value</span></span> | <span data-ttu-id="34952-181">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="34952-181">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="34952-182">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="34952-182">5 seconds</span></span> | <span data-ttu-id="34952-183">Wenn der Client nicht innerhalb dieses Zeitintervalls geschlossen werden kann, wird die Verbindung beendet, nachdem der Server geschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="34952-183">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="34952-184">Ein Delegat, der `Sec-WebSocket-Protocol` verwendet werden kann, um den Header auf einen benutzerdefinierten Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="34952-184">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="34952-185">Der Delegat empfängt die vom Client angeforderten Werte als Eingabe und wird voraussichtlich den gewünschten Wert zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="34952-185">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="34952-186">Konfigurieren von Clientoptionen</span><span class="sxs-lookup"><span data-stu-id="34952-186">Configure client options</span></span>

<span data-ttu-id="34952-187">Clientoptionen können für den `HubConnectionBuilder` Typ konfiguriert werden (verfügbar in den .NET- und JavaScript-Clients).</span><span class="sxs-lookup"><span data-stu-id="34952-187">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="34952-188">Es ist auch im Java-Client `HttpHubConnectionBuilder` verfügbar, aber die Unterklasse ist, was `HubConnection` die Builder-Konfigurationsoptionen enthält, sowie auf der selbst.</span><span class="sxs-lookup"><span data-stu-id="34952-188">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="34952-189">Konfigurieren der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="34952-189">Configure logging</span></span>

<span data-ttu-id="34952-190">Die Protokollierung wird im .NET-Client mit der `ConfigureLogging` Methode konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="34952-190">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="34952-191">Protokollierungsanbieter und Filter können auf die gleiche Weise registriert werden wie auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="34952-191">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="34952-192">Weitere Informationen finden Sie in der Dokumentation ["Anmelden in ASP.NET Kern".](xref:fundamentals/logging/index)</span><span class="sxs-lookup"><span data-stu-id="34952-192">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="34952-193">Um Logging-Anbieter zu registrieren, müssen Sie die erforderlichen Pakete installieren.</span><span class="sxs-lookup"><span data-stu-id="34952-193">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="34952-194">Eine vollständige Liste finden Sie im Abschnitt [Integrierte Protokollierungsanbieter](xref:fundamentals/logging/index#built-in-logging-providers) in den Dokumenten.</span><span class="sxs-lookup"><span data-stu-id="34952-194">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="34952-195">Um beispielsweise die Konsolenprotokollierung `Microsoft.Extensions.Logging.Console` zu aktivieren, installieren Sie das NuGet-Paket.</span><span class="sxs-lookup"><span data-stu-id="34952-195">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="34952-196">Rufen `AddConsole` Sie die Erweiterungsmethode auf:</span><span class="sxs-lookup"><span data-stu-id="34952-196">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="34952-197">Im JavaScript-Client ist `configureLogging` eine ähnliche Methode vorhanden.</span><span class="sxs-lookup"><span data-stu-id="34952-197">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="34952-198">Geben `LogLevel` Sie einen Wert an, der die Mindestebene der zu erzeugenden Protokollnachrichten angibt.</span><span class="sxs-lookup"><span data-stu-id="34952-198">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="34952-199">Protokolle werden in das Browserkonsolenfenster geschrieben.</span><span class="sxs-lookup"><span data-stu-id="34952-199">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="34952-200">Anstelle eines `LogLevel` Werts können Sie `string` auch einen Wert angeben, der einen Namen auf Protokollebene darstellt.</span><span class="sxs-lookup"><span data-stu-id="34952-200">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="34952-201">Dies ist nützlich, wenn Sie die SignalR-Protokollierung `LogLevel` in Umgebungen konfigurieren, in denen Sie keinen Zugriff auf die Konstanten haben.</span><span class="sxs-lookup"><span data-stu-id="34952-201">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="34952-202">In der folgenden Tabelle sind die verfügbaren Protokollebenen aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="34952-202">The following table lists the available log levels.</span></span> <span data-ttu-id="34952-203">Der Wert, `configureLogging` den Sie angeben, um die **minimale** Protokollebene festzulegen, die protokolliert wird.</span><span class="sxs-lookup"><span data-stu-id="34952-203">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="34952-204">Nachrichten, die auf dieser Ebene protokolliert werden, **oder die ebenen, die danach in der Tabelle aufgeführt**sind, werden protokolliert.</span><span class="sxs-lookup"><span data-stu-id="34952-204">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="34952-205">String</span><span class="sxs-lookup"><span data-stu-id="34952-205">String</span></span>                      | <span data-ttu-id="34952-206">LogLevel</span><span class="sxs-lookup"><span data-stu-id="34952-206">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="34952-207">`info`**oder**`information`</span><span class="sxs-lookup"><span data-stu-id="34952-207">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="34952-208">`warn`**oder**`warning`</span><span class="sxs-lookup"><span data-stu-id="34952-208">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="34952-209">Um die Protokollierung `signalR.LogLevel.None` vollständig `configureLogging` zu deaktivieren, geben Sie in der Methode an.</span><span class="sxs-lookup"><span data-stu-id="34952-209">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="34952-210">Weitere Informationen zur Protokollierung finden Sie in der [SignalR-Diagnosedokumentation](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="34952-210">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="34952-211">Der SignalR Java-Client verwendet die [SLF4J-Bibliothek](https://www.slf4j.org/) für die Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="34952-211">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="34952-212">Es handelt sich um eine api auf hoher Ebene, die es Benutzern der Bibliothek ermöglicht, ihre eigene spezifische Protokollierungsimplementierung zu wählen, indem sie eine bestimmte Protokollierungsabhängigkeit einführt.</span><span class="sxs-lookup"><span data-stu-id="34952-212">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="34952-213">Der folgende Codeausschnitt zeigt, wie `java.util.logging` Sie mit dem SignalR Java-Client verwenden.</span><span class="sxs-lookup"><span data-stu-id="34952-213">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="34952-214">Wenn Sie die Protokollierung in Ihren Abhängigkeiten nicht konfigurieren, lädt SLF4J einen standardmäßigen Nicht-Betriebsprotokollmitlogger mit der folgenden Warnmeldung:</span><span class="sxs-lookup"><span data-stu-id="34952-214">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="34952-215">Dies kann getrost ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="34952-215">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="34952-216">Konfigurieren zulässiger Transporte</span><span class="sxs-lookup"><span data-stu-id="34952-216">Configure allowed transports</span></span>

<span data-ttu-id="34952-217">Die von SignalR verwendeten Transporte können `WithUrl` im`withUrl` Aufruf (in JavaScript) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="34952-217">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="34952-218">Ein bitweises-ODER der `HttpTransportType` Werte von kann verwendet werden, um den Client darauf zu beschränken, nur die angegebenen Transporte zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="34952-218">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="34952-219">Alle Transporte sind standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="34952-219">All transports are enabled by default.</span></span>

<span data-ttu-id="34952-220">Um z. B. den Transport servergesendeter Ereignisse zu deaktivieren, aber WebSockets- und Long Polling-Verbindungen zuzulassen:</span><span class="sxs-lookup"><span data-stu-id="34952-220">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="34952-221">Im JavaScript-Client werden Transporte konfiguriert, `transport` indem das Feld `withUrl`für das Optionsobjekt festgelegt wird, das für:</span><span class="sxs-lookup"><span data-stu-id="34952-221">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="34952-222">In dieser Version des Java-Client-Websockets ist der einzige verfügbare Transport.</span><span class="sxs-lookup"><span data-stu-id="34952-222">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="34952-223">Im Java-Client wird der Transport `withTransport` mit `HttpHubConnectionBuilder`der Methode auf der ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="34952-223">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="34952-224">Der Java-Client verwendet standardmäßig den WebSockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="34952-224">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="34952-225">Der SignalR Java-Client unterstützt den Transport-Fallback noch nicht.</span><span class="sxs-lookup"><span data-stu-id="34952-225">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="34952-226">Konfigurieren der Bearer-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="34952-226">Configure bearer authentication</span></span>

<span data-ttu-id="34952-227">Um Authentifizierungsdaten zusammen mit SignalR-Anforderungen bereitzustellen, verwenden Sie die `AccessTokenProvider` Option (in`accessTokenFactory` JavaScript), um eine Funktion anzugeben, die das gewünschte Zugriffstoken zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="34952-227">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="34952-228">Im .NET Client wird dieses Zugriffstoken als HTTP-Token "Bearer `Authorization` Authentication" übergeben `Bearer`(Verwenden des Headers mit einem Typ von ).</span><span class="sxs-lookup"><span data-stu-id="34952-228">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="34952-229">Im JavaScript-Client wird das Zugriffstoken als Bearer-Token verwendet, **außer** in einigen Fällen, in denen Browser-APIs die Möglichkeit einschränken, Header anzuwenden (insbesondere in Server-Sent Events und WebSockets-Anforderungen).</span><span class="sxs-lookup"><span data-stu-id="34952-229">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="34952-230">In diesen Fällen wird das Zugriffstoken als `access_token`Abfragezeichenfolgenwert bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="34952-230">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="34952-231">Im .NET-Client `AccessTokenProvider` kann die Option mithilfe der `WithUrl`Optionen delegate in angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="34952-231">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="34952-232">Im JavaScript-Client wird das Zugriffstoken konfiguriert, indem das `accessTokenFactory` `withUrl`Feld für das Optionsobjekt in :</span><span class="sxs-lookup"><span data-stu-id="34952-232">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="34952-233">Im SignalR Java-Client können Sie ein Trägertoken für die Authentifizierung konfigurieren, indem Sie dem [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)eine Zugriffstokenfactory bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="34952-233">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="34952-234">Verwenden Sie [withAccessTokenFactory,](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) um eine [RxJava](https://github.com/ReactiveX/RxJava) [\<Single String>](https://reactivex.io/documentation/single.html)bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="34952-234">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="34952-235">Mit einem Aufruf von [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)können Sie Logik schreiben, um Zugriffstoken für Ihren Client zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="34952-235">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="34952-236">Konfigurieren von Timeout- und Keep-Alive-Optionen</span><span class="sxs-lookup"><span data-stu-id="34952-236">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="34952-237">Zusätzliche Optionen zum Konfigurieren von Timeout und `HubConnection` Keep-Alive-Verhalten sind für das Objekt selbst verfügbar:</span><span class="sxs-lookup"><span data-stu-id="34952-237">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="34952-238">.NET</span><span class="sxs-lookup"><span data-stu-id="34952-238">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="34952-239">Option</span><span class="sxs-lookup"><span data-stu-id="34952-239">Option</span></span> | <span data-ttu-id="34952-240">Standardwert</span><span class="sxs-lookup"><span data-stu-id="34952-240">Default value</span></span> | <span data-ttu-id="34952-241">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="34952-241">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="34952-242">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="34952-242">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="34952-243">Timeout für Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="34952-243">Timeout for server activity.</span></span> <span data-ttu-id="34952-244">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der `Closed` Client`onclose` die Verbindung des Servers und löst das Ereignis aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="34952-244">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="34952-245">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** vom Client innerhalb des Timeoutintervalls empfangen werden kann.</span><span class="sxs-lookup"><span data-stu-id="34952-245">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="34952-246">Der empfohlene Wert ist eine Zahl, `KeepAliveInterval` die mindestens doppelt so hoch ist wie der Wert des Servers, damit Zeit für Pings eintrifft.</span><span class="sxs-lookup"><span data-stu-id="34952-246">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="34952-247">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="34952-247">15 seconds</span></span> | <span data-ttu-id="34952-248">Timeout für den ersten Serverhandshake.</span><span class="sxs-lookup"><span data-stu-id="34952-248">Timeout for initial server handshake.</span></span> <span data-ttu-id="34952-249">Wenn der Server in diesem Intervall keine Handshake-Antwort sendet, bricht der `Closed` Client`onclose` den Handshake ab und löst das Ereignis aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="34952-249">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="34952-250">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn Handshake-Timeoutfehler aufgrund einer schwerwiegenden Netzwerklatenz auftreten.</span><span class="sxs-lookup"><span data-stu-id="34952-250">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="34952-251">Weitere Informationen zum Handshake-Prozess finden Sie in der [SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="34952-251">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="34952-252">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="34952-252">15 seconds</span></span> | <span data-ttu-id="34952-253">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="34952-253">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="34952-254">Wenn Sie eine Nachricht vom Client senden, wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="34952-254">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="34952-255">Wenn der Client keine Nachricht in `ClientTimeoutInterval` der Gruppe auf dem Server gesendet hat, betrachtet der Server die Verbindung zum Client.</span><span class="sxs-lookup"><span data-stu-id="34952-255">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="34952-256">Im .NET-Client werden Timeoutwerte `TimeSpan` als Werte angegeben.</span><span class="sxs-lookup"><span data-stu-id="34952-256">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="34952-257">JavaScript</span><span class="sxs-lookup"><span data-stu-id="34952-257">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="34952-258">Option</span><span class="sxs-lookup"><span data-stu-id="34952-258">Option</span></span> | <span data-ttu-id="34952-259">Standardwert</span><span class="sxs-lookup"><span data-stu-id="34952-259">Default value</span></span> | <span data-ttu-id="34952-260">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="34952-260">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="34952-261">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="34952-261">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="34952-262">Timeout für Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="34952-262">Timeout for server activity.</span></span> <span data-ttu-id="34952-263">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der `onclose` Client die Verbindung zum Server und löst das Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="34952-263">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="34952-264">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** vom Client innerhalb des Timeoutintervalls empfangen werden kann.</span><span class="sxs-lookup"><span data-stu-id="34952-264">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="34952-265">Der empfohlene Wert ist eine Zahl, `KeepAliveInterval` die mindestens doppelt so hoch ist wie der Wert des Servers, damit Zeit für Pings eintrifft.</span><span class="sxs-lookup"><span data-stu-id="34952-265">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="34952-266">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="34952-266">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="34952-267">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="34952-267">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="34952-268">Wenn Sie eine Nachricht vom Client senden, wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="34952-268">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="34952-269">Wenn der Client keine Nachricht in `ClientTimeoutInterval` der Gruppe auf dem Server gesendet hat, betrachtet der Server die Verbindung zum Client.</span><span class="sxs-lookup"><span data-stu-id="34952-269">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="34952-270">Java</span><span class="sxs-lookup"><span data-stu-id="34952-270">Java</span></span>](#tab/java)

| <span data-ttu-id="34952-271">Option</span><span class="sxs-lookup"><span data-stu-id="34952-271">Option</span></span> | <span data-ttu-id="34952-272">Standardwert</span><span class="sxs-lookup"><span data-stu-id="34952-272">Default value</span></span> | <span data-ttu-id="34952-273">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="34952-273">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="34952-274">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="34952-274">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="34952-275">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="34952-275">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="34952-276">Timeout für Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="34952-276">Timeout for server activity.</span></span> <span data-ttu-id="34952-277">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der `onClose` Client die Verbindung zum Server und löst das Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="34952-277">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="34952-278">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** vom Client innerhalb des Timeoutintervalls empfangen werden kann.</span><span class="sxs-lookup"><span data-stu-id="34952-278">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="34952-279">Der empfohlene Wert ist eine Zahl, `KeepAliveInterval` die mindestens doppelt so hoch ist wie der Wert des Servers, damit Zeit für Pings eintrifft.</span><span class="sxs-lookup"><span data-stu-id="34952-279">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="34952-280">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="34952-280">15 seconds</span></span> | <span data-ttu-id="34952-281">Timeout für den ersten Serverhandshake.</span><span class="sxs-lookup"><span data-stu-id="34952-281">Timeout for initial server handshake.</span></span> <span data-ttu-id="34952-282">Wenn der Server in diesem Intervall keine Handshake-Antwort sendet, bricht der `onClose` Client den Handshake ab und löst das Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="34952-282">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="34952-283">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn Handshake-Timeoutfehler aufgrund einer schwerwiegenden Netzwerklatenz auftreten.</span><span class="sxs-lookup"><span data-stu-id="34952-283">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="34952-284">Weitere Informationen zum Handshake-Prozess finden Sie in der [SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="34952-284">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="34952-285">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="34952-285">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="34952-286">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="34952-286">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="34952-287">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="34952-287">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="34952-288">Wenn Sie eine Nachricht vom Client senden, wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="34952-288">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="34952-289">Wenn der Client keine Nachricht in `ClientTimeoutInterval` der Gruppe auf dem Server gesendet hat, betrachtet der Server die Verbindung zum Client.</span><span class="sxs-lookup"><span data-stu-id="34952-289">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="34952-290">Konfigurieren zusätzlicher Optionen</span><span class="sxs-lookup"><span data-stu-id="34952-290">Configure additional options</span></span>

<span data-ttu-id="34952-291">Zusätzliche Optionen können in `WithUrl` der`withUrl` ( in JavaScript) Methode auf `HubConnectionBuilder` oder `HttpHubConnectionBuilder` auf den verschiedenen Konfigurations-APIs auf dem im Java-Client konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="34952-291">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="34952-292">.NET</span><span class="sxs-lookup"><span data-stu-id="34952-292">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="34952-293">.NET-Option</span><span class="sxs-lookup"><span data-stu-id="34952-293">.NET Option</span></span> |  <span data-ttu-id="34952-294">Standardwert</span><span class="sxs-lookup"><span data-stu-id="34952-294">Default value</span></span> | <span data-ttu-id="34952-295">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="34952-295">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="34952-296">Eine Funktion, die eine Zeichenfolge zurückgibt, die als Bearer-Authentifizierungstoken in HTTP-Anforderungen bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="34952-296">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="34952-297">Legen Sie `true` diesen Vorgang fest, um den Aushandlungsschritt zu überspringen.</span><span class="sxs-lookup"><span data-stu-id="34952-297">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="34952-298">**Wird nur unterstützt, wenn der WebSockets-Transport der einzige aktivierte Transport ist.**</span><span class="sxs-lookup"><span data-stu-id="34952-298">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="34952-299">Diese Einstellung kann nicht aktiviert werden, wenn Sie den Azure SignalR-Dienst verwenden.</span><span class="sxs-lookup"><span data-stu-id="34952-299">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="34952-300">Leer</span><span class="sxs-lookup"><span data-stu-id="34952-300">Empty</span></span> | <span data-ttu-id="34952-301">Eine Auflistung von TLS-Zertifikaten, die an Authentifizierenvon Anforderungen gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="34952-301">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="34952-302">Leer</span><span class="sxs-lookup"><span data-stu-id="34952-302">Empty</span></span> | <span data-ttu-id="34952-303">Eine Sammlung von HTTP-Cookies, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="34952-303">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="34952-304">Leer</span><span class="sxs-lookup"><span data-stu-id="34952-304">Empty</span></span> | <span data-ttu-id="34952-305">Anmeldeinformationen, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="34952-305">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="34952-306">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="34952-306">5 seconds</span></span> | <span data-ttu-id="34952-307">Nur WebSockets.</span><span class="sxs-lookup"><span data-stu-id="34952-307">WebSockets only.</span></span> <span data-ttu-id="34952-308">Die maximale Zeitdauer, die der Client nach dem Schließen wartet, damit der Server die Close-Anforderung bestätigt.</span><span class="sxs-lookup"><span data-stu-id="34952-308">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="34952-309">Wenn der Server den Abschluss innerhalb dieser Zeit nicht bestätigt, trennt der Client die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="34952-309">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="34952-310">Leer</span><span class="sxs-lookup"><span data-stu-id="34952-310">Empty</span></span> | <span data-ttu-id="34952-311">Eine Zuordnung zusätzlicher HTTP-Header, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="34952-311">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="34952-312">Ein Delegat, der zum `HttpMessageHandler` Konfigurieren oder Ersetzen der zum Senden von HTTP-Anforderungen verwendeten Delegaten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="34952-312">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="34952-313">Wird nicht für WebSocket-Verbindungen verwendet.</span><span class="sxs-lookup"><span data-stu-id="34952-313">Not used for WebSocket connections.</span></span> <span data-ttu-id="34952-314">Dieser Delegat muss einen Wert ungleich NULL zurückgeben, und er empfängt den Standardwert als Parameter.</span><span class="sxs-lookup"><span data-stu-id="34952-314">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="34952-315">Ändern Sie entweder die Einstellungen für diesen Standardwert, und geben Sie ihn zurück, oder geben Sie eine neue `HttpMessageHandler` Instanz zurück.</span><span class="sxs-lookup"><span data-stu-id="34952-315">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="34952-316">**Wenn Sie den Handler ersetzen, sollten Sie die Einstellungen, die Sie vom bereitgestellten Handler beibehalten möchten, kopieren, andernfalls gelten die konfigurierten Optionen (z. B. Cookies und Header) nicht für den neuen Handler.**</span><span class="sxs-lookup"><span data-stu-id="34952-316">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="34952-317">Ein HTTP-Proxy, der beim Senden von HTTP-Anforderungen verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="34952-317">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="34952-318">Legen Sie diese boolesche Einstellung fest, um die Standardanmeldeinformationen für HTTP- und WebSockets-Anforderungen zu senden.</span><span class="sxs-lookup"><span data-stu-id="34952-318">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="34952-319">Dies ermöglicht die Verwendung der Windows-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="34952-319">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="34952-320">Ein Delegat, der zum Konfigurieren zusätzlicher WebSocket-Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="34952-320">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="34952-321">Empfängt eine Instanz von [ClientWebSocketOptions,](/dotnet/api/system.net.websockets.clientwebsocketoptions) die zum Konfigurieren der Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="34952-321">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="34952-322">JavaScript</span><span class="sxs-lookup"><span data-stu-id="34952-322">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="34952-323">JavaScript-Option</span><span class="sxs-lookup"><span data-stu-id="34952-323">JavaScript Option</span></span> | <span data-ttu-id="34952-324">Standardwert</span><span class="sxs-lookup"><span data-stu-id="34952-324">Default Value</span></span> | <span data-ttu-id="34952-325">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="34952-325">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="34952-326">Eine Funktion, die eine Zeichenfolge zurückgibt, die als Bearer-Authentifizierungstoken in HTTP-Anforderungen bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="34952-326">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="34952-327">Legen Sie `true` diesen Vorgang fest, um den Aushandlungsschritt zu überspringen.</span><span class="sxs-lookup"><span data-stu-id="34952-327">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="34952-328">**Wird nur unterstützt, wenn der WebSockets-Transport der einzige aktivierte Transport ist.**</span><span class="sxs-lookup"><span data-stu-id="34952-328">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="34952-329">Diese Einstellung kann nicht aktiviert werden, wenn Sie den Azure SignalR-Dienst verwenden.</span><span class="sxs-lookup"><span data-stu-id="34952-329">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="34952-330">Java</span><span class="sxs-lookup"><span data-stu-id="34952-330">Java</span></span>](#tab/java)

| <span data-ttu-id="34952-331">Java-Option</span><span class="sxs-lookup"><span data-stu-id="34952-331">Java Option</span></span> | <span data-ttu-id="34952-332">Standardwert</span><span class="sxs-lookup"><span data-stu-id="34952-332">Default Value</span></span> | <span data-ttu-id="34952-333">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="34952-333">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="34952-334">Eine Funktion, die eine Zeichenfolge zurückgibt, die als Bearer-Authentifizierungstoken in HTTP-Anforderungen bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="34952-334">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="34952-335">Legen Sie `true` diesen Vorgang fest, um den Aushandlungsschritt zu überspringen.</span><span class="sxs-lookup"><span data-stu-id="34952-335">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="34952-336">**Wird nur unterstützt, wenn der WebSockets-Transport der einzige aktivierte Transport ist.**</span><span class="sxs-lookup"><span data-stu-id="34952-336">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="34952-337">Diese Einstellung kann nicht aktiviert werden, wenn Sie den Azure SignalR-Dienst verwenden.</span><span class="sxs-lookup"><span data-stu-id="34952-337">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="34952-338">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="34952-338">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="34952-339">Leer</span><span class="sxs-lookup"><span data-stu-id="34952-339">Empty</span></span> | <span data-ttu-id="34952-340">Eine Zuordnung zusätzlicher HTTP-Header, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="34952-340">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="34952-341">Im .NET Client können diese Optionen durch den `WithUrl`Optionen-Delegaten geändert werden, der für:</span><span class="sxs-lookup"><span data-stu-id="34952-341">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="34952-342">Im JavaScript-Client können diese Optionen in einem JavaScript-Objekt bereitgestellt werden, `withUrl`das für:</span><span class="sxs-lookup"><span data-stu-id="34952-342">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="34952-343">Im Java-Client können diese Optionen mit den `HttpHubConnectionBuilder` Methoden auf dem zurückgegebenen`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="34952-343">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="34952-344">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="34952-344">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="34952-345">JSON/MessagePack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="34952-345">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="34952-346">ASP.NET Core SignalR unterstützt zwei Protokolle für die Codierung von Nachrichten: [JSON](https://www.json.org/) und [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="34952-346">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="34952-347">Jedes Protokoll verfügt über Serialisierungskonfigurationsoptionen.</span><span class="sxs-lookup"><span data-stu-id="34952-347">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="34952-348">Die JSON-Serialisierung kann auf dem Server mithilfe der [AddJsonProtocol-Erweiterungsmethode](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) konfiguriert `Startup.ConfigureServices` werden, die nach [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in Ihrer Methode hinzugefügt werden kann.</span><span class="sxs-lookup"><span data-stu-id="34952-348">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="34952-349">Die `AddJsonProtocol` Methode nimmt einen `options` Delegaten an, der ein Objekt empfängt.</span><span class="sxs-lookup"><span data-stu-id="34952-349">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="34952-350">Die [PayloadSerializerSettings-Eigenschaft](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) für dieses `JsonSerializerSettings` Objekt ist ein JSON.NET-Objekt, das zum Konfigurieren der Serialisierung von Argumenten und Rückgabewerten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="34952-350">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="34952-351">Weitere Informationen finden Sie in der [JSON.NET-Dokumentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="34952-351">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="34952-352">Um den Serialisierungsmodul beispielsweise für die Verwendung von "PascalCase"-Eigenschaftsnamen anstelle der standardmäßigen "camelCase"-Namen zu konfigurieren, verwenden Sie den folgenden Code in: `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="34952-352">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="34952-353">Im .NET-Client ist `AddJsonProtocol` dieselbe Erweiterungsmethode auf [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)vorhanden.</span><span class="sxs-lookup"><span data-stu-id="34952-353">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="34952-354">Der `Microsoft.Extensions.DependencyInjection` Namespace muss importiert werden, um die Erweiterungsmethode aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="34952-354">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="34952-355">Es ist derzeit nicht möglich, die JSON-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="34952-355">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="34952-356">MessagePack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="34952-356">MessagePack serialization options</span></span>

<span data-ttu-id="34952-357">Die MessagePack-Serialisierung kann konfiguriert werden, indem ein Delegat für den [AddMessagePackProtocol-Aufruf](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="34952-357">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="34952-358">Weitere Informationen finden Sie [unter MessagePack in SignalR.](xref:signalr/messagepackhubprotocol)</span><span class="sxs-lookup"><span data-stu-id="34952-358">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="34952-359">Es ist derzeit nicht möglich, die MessagePack-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="34952-359">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="34952-360">Konfigurieren von Serveroptionen</span><span class="sxs-lookup"><span data-stu-id="34952-360">Configure server options</span></span>

<span data-ttu-id="34952-361">In der folgenden Tabelle werden Optionen zum Konfigurieren von SignalR-Hubs beschrieben:</span><span class="sxs-lookup"><span data-stu-id="34952-361">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="34952-362">Option</span><span class="sxs-lookup"><span data-stu-id="34952-362">Option</span></span> | <span data-ttu-id="34952-363">Standardwert</span><span class="sxs-lookup"><span data-stu-id="34952-363">Default Value</span></span> | <span data-ttu-id="34952-364">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="34952-364">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="34952-365">30 Sekunden</span><span class="sxs-lookup"><span data-stu-id="34952-365">30 seconds</span></span> | <span data-ttu-id="34952-366">Der Server betrachtet die Verbindung des Clients, wenn er in diesem Intervall keine Nachricht (einschließlich Keep-Alive) erhalten hat.</span><span class="sxs-lookup"><span data-stu-id="34952-366">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="34952-367">Es kann länger dauern als dieses Timeoutintervall, bis der Client aufgrund der Implementierung tatsächlich als getrennt markiert wird.</span><span class="sxs-lookup"><span data-stu-id="34952-367">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="34952-368">Der empfohlene Wert `KeepAliveInterval` ist das Doppelte des Wertes.</span><span class="sxs-lookup"><span data-stu-id="34952-368">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="34952-369">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="34952-369">15 seconds</span></span> | <span data-ttu-id="34952-370">Wenn der Client innerhalb dieses Zeitintervalls keine erste Handshake-Nachricht sendet, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="34952-370">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="34952-371">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn Handshake-Timeoutfehler aufgrund einer schwerwiegenden Netzwerklatenz auftreten.</span><span class="sxs-lookup"><span data-stu-id="34952-371">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="34952-372">Weitere Informationen zum Handshake-Prozess finden Sie in der [SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="34952-372">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="34952-373">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="34952-373">15 seconds</span></span> | <span data-ttu-id="34952-374">Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten.</span><span class="sxs-lookup"><span data-stu-id="34952-374">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="34952-375">Ändern `KeepAliveInterval`Sie beim `ServerTimeout` / `serverTimeoutInMilliseconds` Ändern die Einstellung auf dem Client.</span><span class="sxs-lookup"><span data-stu-id="34952-375">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="34952-376">Der `ServerTimeout` / `serverTimeoutInMilliseconds` empfohlene Wert `KeepAliveInterval` ist das Doppelte des Wertes.</span><span class="sxs-lookup"><span data-stu-id="34952-376">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="34952-377">Alle installierten Protokolle</span><span class="sxs-lookup"><span data-stu-id="34952-377">All installed protocols</span></span> | <span data-ttu-id="34952-378">Protokolle, die von diesem Hub unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="34952-378">Protocols supported by this hub.</span></span> <span data-ttu-id="34952-379">Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, Protokolle können jedoch aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="34952-379">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="34952-380">Wenn `true`, werden detaillierte Ausnahmemeldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="34952-380">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="34952-381">Der Standardwert ist `false`, da diese Ausnahmemeldungen vertrauliche Informationen enthalten können.</span><span class="sxs-lookup"><span data-stu-id="34952-381">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="34952-382">Optionen können für alle Hubs konfiguriert werden, `AddSignalR` indem `Startup.ConfigureServices`ein Optionsdelegat für den Aufruf in zur Verfügung gestellt wird.</span><span class="sxs-lookup"><span data-stu-id="34952-382">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="34952-383">Optionen für einen einzelnen Hub überschreiben `AddSignalR` die globalen Optionen, die in bereitgestellt werden und können mit <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>folgenden Optionen konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="34952-383">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="34952-384">Erweiterte HTTP-Konfigurationsoptionen</span><span class="sxs-lookup"><span data-stu-id="34952-384">Advanced HTTP configuration options</span></span>

<span data-ttu-id="34952-385">Verwenden `HttpConnectionDispatcherOptions` Sie diese Datei zum Konfigurieren erweiterter Einstellungen für Transporte und Speicherpufferverwaltung.</span><span class="sxs-lookup"><span data-stu-id="34952-385">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="34952-386">Diese Optionen werden konfiguriert, indem sie einen `Startup.Configure`Delegaten an [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in übergeben.</span><span class="sxs-lookup"><span data-stu-id="34952-386">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="34952-387">In der folgenden Tabelle werden Optionen zum Konfigurieren ASP.NET erweiterten HTTP-Optionen von Core SignalR beschrieben:</span><span class="sxs-lookup"><span data-stu-id="34952-387">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="34952-388">Option</span><span class="sxs-lookup"><span data-stu-id="34952-388">Option</span></span> | <span data-ttu-id="34952-389">Standardwert</span><span class="sxs-lookup"><span data-stu-id="34952-389">Default Value</span></span> | <span data-ttu-id="34952-390">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="34952-390">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="34952-391">32 KB</span><span class="sxs-lookup"><span data-stu-id="34952-391">32 KB</span></span> | <span data-ttu-id="34952-392">Die maximale Anzahl von Bytes, die vom Client empfangen werden, den der Server puffert.</span><span class="sxs-lookup"><span data-stu-id="34952-392">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="34952-393">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten empfangen, kann sich jedoch negativ auf den Speicherverbrauch auswirken.</span><span class="sxs-lookup"><span data-stu-id="34952-393">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="34952-394">Daten, die `Authorize` automatisch aus den Attributen gesammelt werden, die auf die Hub-Klasse angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="34952-394">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="34952-395">Eine Liste von [IAuthorizeData-Objekten,](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) die verwendet werden, um zu bestimmen, ob ein Client zum Herstellen einer Verbindung mit dem Hub autorisiert ist.</span><span class="sxs-lookup"><span data-stu-id="34952-395">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="34952-396">32 KB</span><span class="sxs-lookup"><span data-stu-id="34952-396">32 KB</span></span> | <span data-ttu-id="34952-397">Die maximale Anzahl von Bytes, die von der App gesendet werden, die der Server puffert.</span><span class="sxs-lookup"><span data-stu-id="34952-397">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="34952-398">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten senden, kann sich jedoch negativ auf den Speicherverbrauch auswirken.</span><span class="sxs-lookup"><span data-stu-id="34952-398">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="34952-399">Alle Transporte sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="34952-399">All Transports are enabled.</span></span> | <span data-ttu-id="34952-400">Eine Bit-Flags-Enumere von `HttpTransportType` Werten, die die Transporte einschränken können, die ein Client zum Herstellen einer Verbindung verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="34952-400">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="34952-401">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="34952-401">See below.</span></span> | <span data-ttu-id="34952-402">Zusätzliche Optionen speziell für den Long Polling-Transport.</span><span class="sxs-lookup"><span data-stu-id="34952-402">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="34952-403">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="34952-403">See below.</span></span> | <span data-ttu-id="34952-404">Zusätzliche Optionen, die für den WebSockets-Transport spezifisch sind.</span><span class="sxs-lookup"><span data-stu-id="34952-404">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="34952-405">Der Long Polling-Transport verfügt über zusätzliche `LongPolling` Optionen, die mithilfe der Eigenschaft konfiguriert werden können:</span><span class="sxs-lookup"><span data-stu-id="34952-405">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="34952-406">Option</span><span class="sxs-lookup"><span data-stu-id="34952-406">Option</span></span> | <span data-ttu-id="34952-407">Standardwert</span><span class="sxs-lookup"><span data-stu-id="34952-407">Default Value</span></span> | <span data-ttu-id="34952-408">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="34952-408">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="34952-409">90 Sekunden</span><span class="sxs-lookup"><span data-stu-id="34952-409">90 seconds</span></span> | <span data-ttu-id="34952-410">Die maximale Zeit, die der Server auf das Senden einer Nachricht an den Client wartet, bevor eine einzelne Abfrageanforderung beendet wird.</span><span class="sxs-lookup"><span data-stu-id="34952-410">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="34952-411">Wenn Sie diesen Wert verringern, gibt der Client häufiger neue Abfrageanforderungen aus.</span><span class="sxs-lookup"><span data-stu-id="34952-411">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="34952-412">Der WebSocket-Transport verfügt über zusätzliche Optionen, die mithilfe der `WebSockets` Eigenschaft konfiguriert werden können:</span><span class="sxs-lookup"><span data-stu-id="34952-412">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="34952-413">Option</span><span class="sxs-lookup"><span data-stu-id="34952-413">Option</span></span> | <span data-ttu-id="34952-414">Standardwert</span><span class="sxs-lookup"><span data-stu-id="34952-414">Default Value</span></span> | <span data-ttu-id="34952-415">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="34952-415">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="34952-416">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="34952-416">5 seconds</span></span> | <span data-ttu-id="34952-417">Wenn der Client nicht innerhalb dieses Zeitintervalls geschlossen werden kann, wird die Verbindung beendet, nachdem der Server geschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="34952-417">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="34952-418">Ein Delegat, der `Sec-WebSocket-Protocol` verwendet werden kann, um den Header auf einen benutzerdefinierten Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="34952-418">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="34952-419">Der Delegat empfängt die vom Client angeforderten Werte als Eingabe und wird voraussichtlich den gewünschten Wert zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="34952-419">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="34952-420">Konfigurieren von Clientoptionen</span><span class="sxs-lookup"><span data-stu-id="34952-420">Configure client options</span></span>

<span data-ttu-id="34952-421">Clientoptionen können für den `HubConnectionBuilder` Typ konfiguriert werden (verfügbar in den .NET- und JavaScript-Clients).</span><span class="sxs-lookup"><span data-stu-id="34952-421">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="34952-422">Es ist auch im Java-Client `HttpHubConnectionBuilder` verfügbar, aber die Unterklasse ist, was `HubConnection` die Builder-Konfigurationsoptionen enthält, sowie auf der selbst.</span><span class="sxs-lookup"><span data-stu-id="34952-422">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="34952-423">Konfigurieren der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="34952-423">Configure logging</span></span>

<span data-ttu-id="34952-424">Die Protokollierung wird im .NET-Client mit der `ConfigureLogging` Methode konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="34952-424">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="34952-425">Protokollierungsanbieter und Filter können auf die gleiche Weise registriert werden wie auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="34952-425">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="34952-426">Weitere Informationen finden Sie in der Dokumentation ["Anmelden in ASP.NET Kern".](xref:fundamentals/logging/index)</span><span class="sxs-lookup"><span data-stu-id="34952-426">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="34952-427">Um Logging-Anbieter zu registrieren, müssen Sie die erforderlichen Pakete installieren.</span><span class="sxs-lookup"><span data-stu-id="34952-427">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="34952-428">Eine vollständige Liste finden Sie im Abschnitt [Integrierte Protokollierungsanbieter](xref:fundamentals/logging/index#built-in-logging-providers) in den Dokumenten.</span><span class="sxs-lookup"><span data-stu-id="34952-428">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="34952-429">Um beispielsweise die Konsolenprotokollierung `Microsoft.Extensions.Logging.Console` zu aktivieren, installieren Sie das NuGet-Paket.</span><span class="sxs-lookup"><span data-stu-id="34952-429">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="34952-430">Rufen `AddConsole` Sie die Erweiterungsmethode auf:</span><span class="sxs-lookup"><span data-stu-id="34952-430">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="34952-431">Im JavaScript-Client ist `configureLogging` eine ähnliche Methode vorhanden.</span><span class="sxs-lookup"><span data-stu-id="34952-431">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="34952-432">Geben `LogLevel` Sie einen Wert an, der die Mindestebene der zu erzeugenden Protokollnachrichten angibt.</span><span class="sxs-lookup"><span data-stu-id="34952-432">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="34952-433">Protokolle werden in das Browserkonsolenfenster geschrieben.</span><span class="sxs-lookup"><span data-stu-id="34952-433">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="34952-434">Um die Protokollierung `signalR.LogLevel.None` vollständig `configureLogging` zu deaktivieren, geben Sie in der Methode an.</span><span class="sxs-lookup"><span data-stu-id="34952-434">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="34952-435">Weitere Informationen zur Protokollierung finden Sie in der [SignalR-Diagnosedokumentation](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="34952-435">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="34952-436">Der SignalR Java-Client verwendet die [SLF4J-Bibliothek](https://www.slf4j.org/) für die Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="34952-436">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="34952-437">Es handelt sich um eine api auf hoher Ebene, die es Benutzern der Bibliothek ermöglicht, ihre eigene spezifische Protokollierungsimplementierung zu wählen, indem sie eine bestimmte Protokollierungsabhängigkeit einführt.</span><span class="sxs-lookup"><span data-stu-id="34952-437">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="34952-438">Der folgende Codeausschnitt zeigt, wie `java.util.logging` Sie mit dem SignalR Java-Client verwenden.</span><span class="sxs-lookup"><span data-stu-id="34952-438">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="34952-439">Wenn Sie die Protokollierung in Ihren Abhängigkeiten nicht konfigurieren, lädt SLF4J einen standardmäßigen Nicht-Betriebsprotokollmitlogger mit der folgenden Warnmeldung:</span><span class="sxs-lookup"><span data-stu-id="34952-439">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="34952-440">Dies kann getrost ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="34952-440">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="34952-441">Konfigurieren zulässiger Transporte</span><span class="sxs-lookup"><span data-stu-id="34952-441">Configure allowed transports</span></span>

<span data-ttu-id="34952-442">Die von SignalR verwendeten Transporte können `WithUrl` im`withUrl` Aufruf (in JavaScript) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="34952-442">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="34952-443">Ein bitweises-ODER der `HttpTransportType` Werte von kann verwendet werden, um den Client darauf zu beschränken, nur die angegebenen Transporte zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="34952-443">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="34952-444">Alle Transporte sind standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="34952-444">All transports are enabled by default.</span></span>

<span data-ttu-id="34952-445">Um z. B. den Transport servergesendeter Ereignisse zu deaktivieren, aber WebSockets- und Long Polling-Verbindungen zuzulassen:</span><span class="sxs-lookup"><span data-stu-id="34952-445">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="34952-446">Im JavaScript-Client werden Transporte konfiguriert, `transport` indem das Feld `withUrl`für das Optionsobjekt festgelegt wird, das für:</span><span class="sxs-lookup"><span data-stu-id="34952-446">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="34952-447">In dieser Version des Java-Client-Websockets ist der einzige verfügbare Transport.</span><span class="sxs-lookup"><span data-stu-id="34952-447">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="34952-448">Konfigurieren der Bearer-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="34952-448">Configure bearer authentication</span></span>

<span data-ttu-id="34952-449">Um Authentifizierungsdaten zusammen mit SignalR-Anforderungen bereitzustellen, verwenden Sie die `AccessTokenProvider` Option (in`accessTokenFactory` JavaScript), um eine Funktion anzugeben, die das gewünschte Zugriffstoken zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="34952-449">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="34952-450">Im .NET Client wird dieses Zugriffstoken als HTTP-Token "Bearer `Authorization` Authentication" übergeben `Bearer`(Verwenden des Headers mit einem Typ von ).</span><span class="sxs-lookup"><span data-stu-id="34952-450">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="34952-451">Im JavaScript-Client wird das Zugriffstoken als Bearer-Token verwendet, **außer** in einigen Fällen, in denen Browser-APIs die Möglichkeit einschränken, Header anzuwenden (insbesondere in Server-Sent Events und WebSockets-Anforderungen).</span><span class="sxs-lookup"><span data-stu-id="34952-451">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="34952-452">In diesen Fällen wird das Zugriffstoken als `access_token`Abfragezeichenfolgenwert bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="34952-452">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="34952-453">Im .NET-Client `AccessTokenProvider` kann die Option mithilfe der `WithUrl`Optionen delegate in angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="34952-453">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="34952-454">Im JavaScript-Client wird das Zugriffstoken konfiguriert, indem das `accessTokenFactory` `withUrl`Feld für das Optionsobjekt in :</span><span class="sxs-lookup"><span data-stu-id="34952-454">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="34952-455">Im SignalR Java-Client können Sie ein Trägertoken für die Authentifizierung konfigurieren, indem Sie dem [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)eine Zugriffstokenfactory bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="34952-455">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="34952-456">Verwenden Sie [withAccessTokenFactory,](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) um eine [RxJava](https://github.com/ReactiveX/RxJava) [\<Single String>](https://reactivex.io/documentation/single.html)bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="34952-456">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="34952-457">Mit einem Aufruf von [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)können Sie Logik schreiben, um Zugriffstoken für Ihren Client zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="34952-457">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="34952-458">Konfigurieren von Timeout- und Keep-Alive-Optionen</span><span class="sxs-lookup"><span data-stu-id="34952-458">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="34952-459">Zusätzliche Optionen zum Konfigurieren von Timeout und `HubConnection` Keep-Alive-Verhalten sind für das Objekt selbst verfügbar:</span><span class="sxs-lookup"><span data-stu-id="34952-459">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="34952-460">.NET</span><span class="sxs-lookup"><span data-stu-id="34952-460">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="34952-461">Option</span><span class="sxs-lookup"><span data-stu-id="34952-461">Option</span></span> | <span data-ttu-id="34952-462">Standardwert</span><span class="sxs-lookup"><span data-stu-id="34952-462">Default value</span></span> | <span data-ttu-id="34952-463">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="34952-463">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="34952-464">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="34952-464">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="34952-465">Timeout für Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="34952-465">Timeout for server activity.</span></span> <span data-ttu-id="34952-466">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der `Closed` Client`onclose` die Verbindung des Servers und löst das Ereignis aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="34952-466">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="34952-467">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** vom Client innerhalb des Timeoutintervalls empfangen werden kann.</span><span class="sxs-lookup"><span data-stu-id="34952-467">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="34952-468">Der empfohlene Wert ist eine Zahl, `KeepAliveInterval` die mindestens doppelt so hoch ist wie der Wert des Servers, damit Zeit für Pings eintrifft.</span><span class="sxs-lookup"><span data-stu-id="34952-468">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="34952-469">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="34952-469">15 seconds</span></span> | <span data-ttu-id="34952-470">Timeout für den ersten Serverhandshake.</span><span class="sxs-lookup"><span data-stu-id="34952-470">Timeout for initial server handshake.</span></span> <span data-ttu-id="34952-471">Wenn der Server in diesem Intervall keine Handshake-Antwort sendet, bricht der `Closed` Client`onclose` den Handshake ab und löst das Ereignis aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="34952-471">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="34952-472">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn Handshake-Timeoutfehler aufgrund einer schwerwiegenden Netzwerklatenz auftreten.</span><span class="sxs-lookup"><span data-stu-id="34952-472">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="34952-473">Weitere Informationen zum Handshake-Prozess finden Sie in der [SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="34952-473">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="34952-474">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="34952-474">15 seconds</span></span> | <span data-ttu-id="34952-475">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="34952-475">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="34952-476">Wenn Sie eine Nachricht vom Client senden, wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="34952-476">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="34952-477">Wenn der Client keine Nachricht in `ClientTimeoutInterval` der Gruppe auf dem Server gesendet hat, betrachtet der Server die Verbindung zum Client.</span><span class="sxs-lookup"><span data-stu-id="34952-477">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="34952-478">Im .NET-Client werden Timeoutwerte `TimeSpan` als Werte angegeben.</span><span class="sxs-lookup"><span data-stu-id="34952-478">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="34952-479">JavaScript</span><span class="sxs-lookup"><span data-stu-id="34952-479">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="34952-480">Option</span><span class="sxs-lookup"><span data-stu-id="34952-480">Option</span></span> | <span data-ttu-id="34952-481">Standardwert</span><span class="sxs-lookup"><span data-stu-id="34952-481">Default value</span></span> | <span data-ttu-id="34952-482">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="34952-482">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="34952-483">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="34952-483">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="34952-484">Timeout für Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="34952-484">Timeout for server activity.</span></span> <span data-ttu-id="34952-485">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der `onclose` Client die Verbindung zum Server und löst das Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="34952-485">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="34952-486">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** vom Client innerhalb des Timeoutintervalls empfangen werden kann.</span><span class="sxs-lookup"><span data-stu-id="34952-486">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="34952-487">Der empfohlene Wert ist eine Zahl, `KeepAliveInterval` die mindestens doppelt so hoch ist wie der Wert des Servers, damit Zeit für Pings eintrifft.</span><span class="sxs-lookup"><span data-stu-id="34952-487">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="34952-488">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="34952-488">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="34952-489">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="34952-489">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="34952-490">Wenn Sie eine Nachricht vom Client senden, wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="34952-490">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="34952-491">Wenn der Client keine Nachricht in `ClientTimeoutInterval` der Gruppe auf dem Server gesendet hat, betrachtet der Server die Verbindung zum Client.</span><span class="sxs-lookup"><span data-stu-id="34952-491">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="34952-492">Java</span><span class="sxs-lookup"><span data-stu-id="34952-492">Java</span></span>](#tab/java)

| <span data-ttu-id="34952-493">Option</span><span class="sxs-lookup"><span data-stu-id="34952-493">Option</span></span> | <span data-ttu-id="34952-494">Standardwert</span><span class="sxs-lookup"><span data-stu-id="34952-494">Default value</span></span> | <span data-ttu-id="34952-495">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="34952-495">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="34952-496">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="34952-496">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="34952-497">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="34952-497">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="34952-498">Timeout für Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="34952-498">Timeout for server activity.</span></span> <span data-ttu-id="34952-499">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der `onClose` Client die Verbindung zum Server und löst das Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="34952-499">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="34952-500">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** vom Client innerhalb des Timeoutintervalls empfangen werden kann.</span><span class="sxs-lookup"><span data-stu-id="34952-500">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="34952-501">Der empfohlene Wert ist eine Zahl, `KeepAliveInterval` die mindestens doppelt so hoch ist wie der Wert des Servers, damit Zeit für Pings eintrifft.</span><span class="sxs-lookup"><span data-stu-id="34952-501">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="34952-502">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="34952-502">15 seconds</span></span> | <span data-ttu-id="34952-503">Timeout für den ersten Serverhandshake.</span><span class="sxs-lookup"><span data-stu-id="34952-503">Timeout for initial server handshake.</span></span> <span data-ttu-id="34952-504">Wenn der Server in diesem Intervall keine Handshake-Antwort sendet, bricht der `onClose` Client den Handshake ab und löst das Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="34952-504">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="34952-505">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn Handshake-Timeoutfehler aufgrund einer schwerwiegenden Netzwerklatenz auftreten.</span><span class="sxs-lookup"><span data-stu-id="34952-505">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="34952-506">Weitere Informationen zum Handshake-Prozess finden Sie in der [SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="34952-506">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="34952-507">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="34952-507">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="34952-508">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="34952-508">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="34952-509">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="34952-509">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="34952-510">Wenn Sie eine Nachricht vom Client senden, wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="34952-510">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="34952-511">Wenn der Client keine Nachricht in `ClientTimeoutInterval` der Gruppe auf dem Server gesendet hat, betrachtet der Server die Verbindung zum Client.</span><span class="sxs-lookup"><span data-stu-id="34952-511">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="34952-512">Konfigurieren zusätzlicher Optionen</span><span class="sxs-lookup"><span data-stu-id="34952-512">Configure additional options</span></span>

<span data-ttu-id="34952-513">Zusätzliche Optionen können in `WithUrl` der`withUrl` ( in JavaScript) Methode auf `HubConnectionBuilder` oder `HttpHubConnectionBuilder` auf den verschiedenen Konfigurations-APIs auf dem im Java-Client konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="34952-513">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="34952-514">.NET</span><span class="sxs-lookup"><span data-stu-id="34952-514">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="34952-515">.NET-Option</span><span class="sxs-lookup"><span data-stu-id="34952-515">.NET Option</span></span> |  <span data-ttu-id="34952-516">Standardwert</span><span class="sxs-lookup"><span data-stu-id="34952-516">Default value</span></span> | <span data-ttu-id="34952-517">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="34952-517">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="34952-518">Eine Funktion, die eine Zeichenfolge zurückgibt, die als Bearer-Authentifizierungstoken in HTTP-Anforderungen bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="34952-518">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="34952-519">Legen Sie `true` diesen Vorgang fest, um den Aushandlungsschritt zu überspringen.</span><span class="sxs-lookup"><span data-stu-id="34952-519">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="34952-520">**Wird nur unterstützt, wenn der WebSockets-Transport der einzige aktivierte Transport ist.**</span><span class="sxs-lookup"><span data-stu-id="34952-520">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="34952-521">Diese Einstellung kann nicht aktiviert werden, wenn Sie den Azure SignalR-Dienst verwenden.</span><span class="sxs-lookup"><span data-stu-id="34952-521">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="34952-522">Leer</span><span class="sxs-lookup"><span data-stu-id="34952-522">Empty</span></span> | <span data-ttu-id="34952-523">Eine Auflistung von TLS-Zertifikaten, die an Authentifizierenvon Anforderungen gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="34952-523">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="34952-524">Leer</span><span class="sxs-lookup"><span data-stu-id="34952-524">Empty</span></span> | <span data-ttu-id="34952-525">Eine Sammlung von HTTP-Cookies, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="34952-525">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="34952-526">Leer</span><span class="sxs-lookup"><span data-stu-id="34952-526">Empty</span></span> | <span data-ttu-id="34952-527">Anmeldeinformationen, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="34952-527">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="34952-528">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="34952-528">5 seconds</span></span> | <span data-ttu-id="34952-529">Nur WebSockets.</span><span class="sxs-lookup"><span data-stu-id="34952-529">WebSockets only.</span></span> <span data-ttu-id="34952-530">Die maximale Zeitdauer, die der Client nach dem Schließen wartet, damit der Server die Close-Anforderung bestätigt.</span><span class="sxs-lookup"><span data-stu-id="34952-530">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="34952-531">Wenn der Server den Abschluss innerhalb dieser Zeit nicht bestätigt, trennt der Client die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="34952-531">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="34952-532">Leer</span><span class="sxs-lookup"><span data-stu-id="34952-532">Empty</span></span> | <span data-ttu-id="34952-533">Eine Zuordnung zusätzlicher HTTP-Header, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="34952-533">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="34952-534">Ein Delegat, der zum `HttpMessageHandler` Konfigurieren oder Ersetzen der zum Senden von HTTP-Anforderungen verwendeten Delegaten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="34952-534">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="34952-535">Wird nicht für WebSocket-Verbindungen verwendet.</span><span class="sxs-lookup"><span data-stu-id="34952-535">Not used for WebSocket connections.</span></span> <span data-ttu-id="34952-536">Dieser Delegat muss einen Wert ungleich NULL zurückgeben, und er empfängt den Standardwert als Parameter.</span><span class="sxs-lookup"><span data-stu-id="34952-536">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="34952-537">Ändern Sie entweder die Einstellungen für diesen Standardwert, und geben Sie ihn zurück, oder geben Sie eine neue `HttpMessageHandler` Instanz zurück.</span><span class="sxs-lookup"><span data-stu-id="34952-537">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="34952-538">**Wenn Sie den Handler ersetzen, sollten Sie die Einstellungen, die Sie vom bereitgestellten Handler beibehalten möchten, kopieren, andernfalls gelten die konfigurierten Optionen (z. B. Cookies und Header) nicht für den neuen Handler.**</span><span class="sxs-lookup"><span data-stu-id="34952-538">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="34952-539">Ein HTTP-Proxy, der beim Senden von HTTP-Anforderungen verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="34952-539">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="34952-540">Legen Sie diese boolesche Einstellung fest, um die Standardanmeldeinformationen für HTTP- und WebSockets-Anforderungen zu senden.</span><span class="sxs-lookup"><span data-stu-id="34952-540">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="34952-541">Dies ermöglicht die Verwendung der Windows-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="34952-541">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="34952-542">Ein Delegat, der zum Konfigurieren zusätzlicher WebSocket-Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="34952-542">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="34952-543">Empfängt eine Instanz von [ClientWebSocketOptions,](/dotnet/api/system.net.websockets.clientwebsocketoptions) die zum Konfigurieren der Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="34952-543">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="34952-544">JavaScript</span><span class="sxs-lookup"><span data-stu-id="34952-544">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="34952-545">JavaScript-Option</span><span class="sxs-lookup"><span data-stu-id="34952-545">JavaScript Option</span></span> | <span data-ttu-id="34952-546">Standardwert</span><span class="sxs-lookup"><span data-stu-id="34952-546">Default Value</span></span> | <span data-ttu-id="34952-547">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="34952-547">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="34952-548">Eine Funktion, die eine Zeichenfolge zurückgibt, die als Bearer-Authentifizierungstoken in HTTP-Anforderungen bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="34952-548">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="34952-549">Legen Sie `true` diesen Vorgang fest, um den Aushandlungsschritt zu überspringen.</span><span class="sxs-lookup"><span data-stu-id="34952-549">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="34952-550">**Wird nur unterstützt, wenn der WebSockets-Transport der einzige aktivierte Transport ist.**</span><span class="sxs-lookup"><span data-stu-id="34952-550">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="34952-551">Diese Einstellung kann nicht aktiviert werden, wenn Sie den Azure SignalR-Dienst verwenden.</span><span class="sxs-lookup"><span data-stu-id="34952-551">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="34952-552">Java</span><span class="sxs-lookup"><span data-stu-id="34952-552">Java</span></span>](#tab/java)

| <span data-ttu-id="34952-553">Java-Option</span><span class="sxs-lookup"><span data-stu-id="34952-553">Java Option</span></span> | <span data-ttu-id="34952-554">Standardwert</span><span class="sxs-lookup"><span data-stu-id="34952-554">Default Value</span></span> | <span data-ttu-id="34952-555">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="34952-555">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="34952-556">Eine Funktion, die eine Zeichenfolge zurückgibt, die als Bearer-Authentifizierungstoken in HTTP-Anforderungen bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="34952-556">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="34952-557">Legen Sie `true` diesen Vorgang fest, um den Aushandlungsschritt zu überspringen.</span><span class="sxs-lookup"><span data-stu-id="34952-557">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="34952-558">**Wird nur unterstützt, wenn der WebSockets-Transport der einzige aktivierte Transport ist.**</span><span class="sxs-lookup"><span data-stu-id="34952-558">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="34952-559">Diese Einstellung kann nicht aktiviert werden, wenn Sie den Azure SignalR-Dienst verwenden.</span><span class="sxs-lookup"><span data-stu-id="34952-559">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="34952-560">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="34952-560">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="34952-561">Leer</span><span class="sxs-lookup"><span data-stu-id="34952-561">Empty</span></span> | <span data-ttu-id="34952-562">Eine Zuordnung zusätzlicher HTTP-Header, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="34952-562">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="34952-563">Im .NET Client können diese Optionen durch den `WithUrl`Optionen-Delegaten geändert werden, der für:</span><span class="sxs-lookup"><span data-stu-id="34952-563">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="34952-564">Im JavaScript-Client können diese Optionen in einem JavaScript-Objekt bereitgestellt werden, `withUrl`das für:</span><span class="sxs-lookup"><span data-stu-id="34952-564">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="34952-565">Im Java-Client können diese Optionen mit den `HttpHubConnectionBuilder` Methoden auf dem zurückgegebenen`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="34952-565">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="34952-566">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="34952-566">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="34952-567">JSON/MessagePack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="34952-567">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="34952-568">ASP.NET Core SignalR unterstützt zwei Protokolle für die Codierung von Nachrichten: [JSON](https://www.json.org/) und [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="34952-568">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="34952-569">Jedes Protokoll verfügt über Serialisierungskonfigurationsoptionen.</span><span class="sxs-lookup"><span data-stu-id="34952-569">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="34952-570">Die JSON-Serialisierung kann auf dem Server mithilfe der [AddJsonProtocol-Erweiterungsmethode](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) konfiguriert `Startup.ConfigureServices` werden, die nach [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in Ihrer Methode hinzugefügt werden kann.</span><span class="sxs-lookup"><span data-stu-id="34952-570">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="34952-571">Die `AddJsonProtocol` Methode nimmt einen `options` Delegaten an, der ein Objekt empfängt.</span><span class="sxs-lookup"><span data-stu-id="34952-571">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="34952-572">Die [PayloadSerializerSettings-Eigenschaft](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) für dieses `JsonSerializerSettings` Objekt ist ein JSON.NET-Objekt, das zum Konfigurieren der Serialisierung von Argumenten und Rückgabewerten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="34952-572">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="34952-573">Weitere Informationen finden Sie in der [JSON.NET-Dokumentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="34952-573">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="34952-574">Um den Serialisierungsmodul beispielsweise für die Verwendung von "PascalCase"-Eigenschaftsnamen anstelle der standardmäßigen "camelCase"-Namen zu konfigurieren, verwenden Sie den folgenden Code in: `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="34952-574">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="34952-575">Im .NET-Client ist `AddJsonProtocol` dieselbe Erweiterungsmethode auf [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)vorhanden.</span><span class="sxs-lookup"><span data-stu-id="34952-575">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="34952-576">Der `Microsoft.Extensions.DependencyInjection` Namespace muss importiert werden, um die Erweiterungsmethode aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="34952-576">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="34952-577">Es ist derzeit nicht möglich, die JSON-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="34952-577">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="34952-578">MessagePack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="34952-578">MessagePack serialization options</span></span>

<span data-ttu-id="34952-579">Die MessagePack-Serialisierung kann konfiguriert werden, indem ein Delegat für den [AddMessagePackProtocol-Aufruf](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="34952-579">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="34952-580">Weitere Informationen finden Sie [unter MessagePack in SignalR.](xref:signalr/messagepackhubprotocol)</span><span class="sxs-lookup"><span data-stu-id="34952-580">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="34952-581">Es ist derzeit nicht möglich, die MessagePack-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="34952-581">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="34952-582">Konfigurieren von Serveroptionen</span><span class="sxs-lookup"><span data-stu-id="34952-582">Configure server options</span></span>

<span data-ttu-id="34952-583">In der folgenden Tabelle werden Optionen zum Konfigurieren von SignalR-Hubs beschrieben:</span><span class="sxs-lookup"><span data-stu-id="34952-583">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="34952-584">Option</span><span class="sxs-lookup"><span data-stu-id="34952-584">Option</span></span> | <span data-ttu-id="34952-585">Standardwert</span><span class="sxs-lookup"><span data-stu-id="34952-585">Default Value</span></span> | <span data-ttu-id="34952-586">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="34952-586">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="34952-587">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="34952-587">15 seconds</span></span> | <span data-ttu-id="34952-588">Wenn der Client innerhalb dieses Zeitintervalls keine erste Handshake-Nachricht sendet, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="34952-588">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="34952-589">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn Handshake-Timeoutfehler aufgrund einer schwerwiegenden Netzwerklatenz auftreten.</span><span class="sxs-lookup"><span data-stu-id="34952-589">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="34952-590">Weitere Informationen zum Handshake-Prozess finden Sie in der [SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="34952-590">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="34952-591">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="34952-591">15 seconds</span></span> | <span data-ttu-id="34952-592">Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten.</span><span class="sxs-lookup"><span data-stu-id="34952-592">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="34952-593">Ändern `KeepAliveInterval`Sie beim `ServerTimeout` / `serverTimeoutInMilliseconds` Ändern die Einstellung auf dem Client.</span><span class="sxs-lookup"><span data-stu-id="34952-593">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="34952-594">Der `ServerTimeout` / `serverTimeoutInMilliseconds` empfohlene Wert `KeepAliveInterval` ist das Doppelte des Wertes.</span><span class="sxs-lookup"><span data-stu-id="34952-594">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="34952-595">Alle installierten Protokolle</span><span class="sxs-lookup"><span data-stu-id="34952-595">All installed protocols</span></span> | <span data-ttu-id="34952-596">Protokolle, die von diesem Hub unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="34952-596">Protocols supported by this hub.</span></span> <span data-ttu-id="34952-597">Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, Protokolle können jedoch aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="34952-597">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="34952-598">Wenn `true`, werden detaillierte Ausnahmemeldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="34952-598">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="34952-599">Der Standardwert ist `false`, da diese Ausnahmemeldungen vertrauliche Informationen enthalten können.</span><span class="sxs-lookup"><span data-stu-id="34952-599">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="34952-600">Optionen können für alle Hubs konfiguriert werden, `AddSignalR` indem `Startup.ConfigureServices`ein Optionsdelegat für den Aufruf in zur Verfügung gestellt wird.</span><span class="sxs-lookup"><span data-stu-id="34952-600">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="34952-601">Optionen für einen einzelnen Hub überschreiben `AddSignalR` die globalen Optionen, die in bereitgestellt werden und können mit <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>folgenden Optionen konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="34952-601">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="34952-602">Erweiterte HTTP-Konfigurationsoptionen</span><span class="sxs-lookup"><span data-stu-id="34952-602">Advanced HTTP configuration options</span></span>

<span data-ttu-id="34952-603">Verwenden `HttpConnectionDispatcherOptions` Sie diese Datei zum Konfigurieren erweiterter Einstellungen für Transporte und Speicherpufferverwaltung.</span><span class="sxs-lookup"><span data-stu-id="34952-603">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="34952-604">Diese Optionen werden konfiguriert, indem sie einen `Startup.Configure`Delegaten an [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in übergeben.</span><span class="sxs-lookup"><span data-stu-id="34952-604">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="34952-605">In der folgenden Tabelle werden Optionen zum Konfigurieren ASP.NET erweiterten HTTP-Optionen von Core SignalR beschrieben:</span><span class="sxs-lookup"><span data-stu-id="34952-605">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="34952-606">Option</span><span class="sxs-lookup"><span data-stu-id="34952-606">Option</span></span> | <span data-ttu-id="34952-607">Standardwert</span><span class="sxs-lookup"><span data-stu-id="34952-607">Default Value</span></span> | <span data-ttu-id="34952-608">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="34952-608">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="34952-609">32 KB</span><span class="sxs-lookup"><span data-stu-id="34952-609">32 KB</span></span> | <span data-ttu-id="34952-610">Die maximale Anzahl von Bytes, die vom Client empfangen werden, den der Server puffert.</span><span class="sxs-lookup"><span data-stu-id="34952-610">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="34952-611">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten empfangen, kann sich jedoch negativ auf den Speicherverbrauch auswirken.</span><span class="sxs-lookup"><span data-stu-id="34952-611">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="34952-612">Daten, die `Authorize` automatisch aus den Attributen gesammelt werden, die auf die Hub-Klasse angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="34952-612">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="34952-613">Eine Liste von [IAuthorizeData-Objekten,](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) die verwendet werden, um zu bestimmen, ob ein Client zum Herstellen einer Verbindung mit dem Hub autorisiert ist.</span><span class="sxs-lookup"><span data-stu-id="34952-613">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="34952-614">32 KB</span><span class="sxs-lookup"><span data-stu-id="34952-614">32 KB</span></span> | <span data-ttu-id="34952-615">Die maximale Anzahl von Bytes, die von der App gesendet werden, die der Server puffert.</span><span class="sxs-lookup"><span data-stu-id="34952-615">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="34952-616">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten senden, kann sich jedoch negativ auf den Speicherverbrauch auswirken.</span><span class="sxs-lookup"><span data-stu-id="34952-616">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="34952-617">Alle Transporte sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="34952-617">All Transports are enabled.</span></span> | <span data-ttu-id="34952-618">Eine Bit-Flags-Enumere von `HttpTransportType` Werten, die die Transporte einschränken können, die ein Client zum Herstellen einer Verbindung verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="34952-618">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="34952-619">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="34952-619">See below.</span></span> | <span data-ttu-id="34952-620">Zusätzliche Optionen speziell für den Long Polling-Transport.</span><span class="sxs-lookup"><span data-stu-id="34952-620">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="34952-621">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="34952-621">See below.</span></span> | <span data-ttu-id="34952-622">Zusätzliche Optionen, die für den WebSockets-Transport spezifisch sind.</span><span class="sxs-lookup"><span data-stu-id="34952-622">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="34952-623">Der Long Polling-Transport verfügt über zusätzliche `LongPolling` Optionen, die mithilfe der Eigenschaft konfiguriert werden können:</span><span class="sxs-lookup"><span data-stu-id="34952-623">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="34952-624">Option</span><span class="sxs-lookup"><span data-stu-id="34952-624">Option</span></span> | <span data-ttu-id="34952-625">Standardwert</span><span class="sxs-lookup"><span data-stu-id="34952-625">Default Value</span></span> | <span data-ttu-id="34952-626">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="34952-626">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="34952-627">90 Sekunden</span><span class="sxs-lookup"><span data-stu-id="34952-627">90 seconds</span></span> | <span data-ttu-id="34952-628">Die maximale Zeit, die der Server auf das Senden einer Nachricht an den Client wartet, bevor eine einzelne Abfrageanforderung beendet wird.</span><span class="sxs-lookup"><span data-stu-id="34952-628">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="34952-629">Wenn Sie diesen Wert verringern, gibt der Client häufiger neue Abfrageanforderungen aus.</span><span class="sxs-lookup"><span data-stu-id="34952-629">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="34952-630">Der WebSocket-Transport verfügt über zusätzliche Optionen, die mithilfe der `WebSockets` Eigenschaft konfiguriert werden können:</span><span class="sxs-lookup"><span data-stu-id="34952-630">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="34952-631">Option</span><span class="sxs-lookup"><span data-stu-id="34952-631">Option</span></span> | <span data-ttu-id="34952-632">Standardwert</span><span class="sxs-lookup"><span data-stu-id="34952-632">Default Value</span></span> | <span data-ttu-id="34952-633">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="34952-633">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="34952-634">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="34952-634">5 seconds</span></span> | <span data-ttu-id="34952-635">Wenn der Client nicht innerhalb dieses Zeitintervalls geschlossen werden kann, wird die Verbindung beendet, nachdem der Server geschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="34952-635">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="34952-636">Ein Delegat, der `Sec-WebSocket-Protocol` verwendet werden kann, um den Header auf einen benutzerdefinierten Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="34952-636">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="34952-637">Der Delegat empfängt die vom Client angeforderten Werte als Eingabe und wird voraussichtlich den gewünschten Wert zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="34952-637">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="34952-638">Konfigurieren von Clientoptionen</span><span class="sxs-lookup"><span data-stu-id="34952-638">Configure client options</span></span>

<span data-ttu-id="34952-639">Clientoptionen können für den `HubConnectionBuilder` Typ konfiguriert werden (verfügbar in den .NET- und JavaScript-Clients).</span><span class="sxs-lookup"><span data-stu-id="34952-639">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="34952-640">Es ist auch im Java-Client `HttpHubConnectionBuilder` verfügbar, aber die Unterklasse ist, was `HubConnection` die Builder-Konfigurationsoptionen enthält, sowie auf der selbst.</span><span class="sxs-lookup"><span data-stu-id="34952-640">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="34952-641">Konfigurieren der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="34952-641">Configure logging</span></span>

<span data-ttu-id="34952-642">Die Protokollierung wird im .NET-Client mit der `ConfigureLogging` Methode konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="34952-642">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="34952-643">Protokollierungsanbieter und Filter können auf die gleiche Weise registriert werden wie auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="34952-643">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="34952-644">Weitere Informationen finden Sie in der Dokumentation ["Anmelden in ASP.NET Kern".](xref:fundamentals/logging/index)</span><span class="sxs-lookup"><span data-stu-id="34952-644">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="34952-645">Um Logging-Anbieter zu registrieren, müssen Sie die erforderlichen Pakete installieren.</span><span class="sxs-lookup"><span data-stu-id="34952-645">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="34952-646">Eine vollständige Liste finden Sie im Abschnitt [Integrierte Protokollierungsanbieter](xref:fundamentals/logging/index#built-in-logging-providers) in den Dokumenten.</span><span class="sxs-lookup"><span data-stu-id="34952-646">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="34952-647">Um beispielsweise die Konsolenprotokollierung `Microsoft.Extensions.Logging.Console` zu aktivieren, installieren Sie das NuGet-Paket.</span><span class="sxs-lookup"><span data-stu-id="34952-647">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="34952-648">Rufen `AddConsole` Sie die Erweiterungsmethode auf:</span><span class="sxs-lookup"><span data-stu-id="34952-648">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="34952-649">Im JavaScript-Client ist `configureLogging` eine ähnliche Methode vorhanden.</span><span class="sxs-lookup"><span data-stu-id="34952-649">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="34952-650">Geben `LogLevel` Sie einen Wert an, der die Mindestebene der zu erzeugenden Protokollnachrichten angibt.</span><span class="sxs-lookup"><span data-stu-id="34952-650">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="34952-651">Protokolle werden in das Browserkonsolenfenster geschrieben.</span><span class="sxs-lookup"><span data-stu-id="34952-651">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="34952-652">Um die Protokollierung `signalR.LogLevel.None` vollständig `configureLogging` zu deaktivieren, geben Sie in der Methode an.</span><span class="sxs-lookup"><span data-stu-id="34952-652">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="34952-653">Weitere Informationen zur Protokollierung finden Sie in der [SignalR-Diagnosedokumentation](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="34952-653">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="34952-654">Der SignalR Java-Client verwendet die [SLF4J-Bibliothek](https://www.slf4j.org/) für die Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="34952-654">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="34952-655">Es handelt sich um eine api auf hoher Ebene, die es Benutzern der Bibliothek ermöglicht, ihre eigene spezifische Protokollierungsimplementierung zu wählen, indem sie eine bestimmte Protokollierungsabhängigkeit einführt.</span><span class="sxs-lookup"><span data-stu-id="34952-655">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="34952-656">Der folgende Codeausschnitt zeigt, wie `java.util.logging` Sie mit dem SignalR Java-Client verwenden.</span><span class="sxs-lookup"><span data-stu-id="34952-656">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="34952-657">Wenn Sie die Protokollierung in Ihren Abhängigkeiten nicht konfigurieren, lädt SLF4J einen standardmäßigen Nicht-Betriebsprotokollmitlogger mit der folgenden Warnmeldung:</span><span class="sxs-lookup"><span data-stu-id="34952-657">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="34952-658">Dies kann getrost ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="34952-658">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="34952-659">Konfigurieren zulässiger Transporte</span><span class="sxs-lookup"><span data-stu-id="34952-659">Configure allowed transports</span></span>

<span data-ttu-id="34952-660">Die von SignalR verwendeten Transporte können `WithUrl` im`withUrl` Aufruf (in JavaScript) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="34952-660">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="34952-661">Ein bitweises-ODER der `HttpTransportType` Werte von kann verwendet werden, um den Client darauf zu beschränken, nur die angegebenen Transporte zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="34952-661">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="34952-662">Alle Transporte sind standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="34952-662">All transports are enabled by default.</span></span>

<span data-ttu-id="34952-663">Um z. B. den Transport servergesendeter Ereignisse zu deaktivieren, aber WebSockets- und Long Polling-Verbindungen zuzulassen:</span><span class="sxs-lookup"><span data-stu-id="34952-663">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="34952-664">Im JavaScript-Client werden Transporte konfiguriert, `transport` indem das Feld `withUrl`für das Optionsobjekt festgelegt wird, das für:</span><span class="sxs-lookup"><span data-stu-id="34952-664">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="34952-665">Konfigurieren der Bearer-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="34952-665">Configure bearer authentication</span></span>

<span data-ttu-id="34952-666">Um Authentifizierungsdaten zusammen mit SignalR-Anforderungen bereitzustellen, verwenden Sie die `AccessTokenProvider` Option (in`accessTokenFactory` JavaScript), um eine Funktion anzugeben, die das gewünschte Zugriffstoken zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="34952-666">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="34952-667">Im .NET Client wird dieses Zugriffstoken als HTTP-Token "Bearer `Authorization` Authentication" übergeben `Bearer`(Verwenden des Headers mit einem Typ von ).</span><span class="sxs-lookup"><span data-stu-id="34952-667">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="34952-668">Im JavaScript-Client wird das Zugriffstoken als Bearer-Token verwendet, **außer** in einigen Fällen, in denen Browser-APIs die Möglichkeit einschränken, Header anzuwenden (insbesondere in Server-Sent Events und WebSockets-Anforderungen).</span><span class="sxs-lookup"><span data-stu-id="34952-668">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="34952-669">In diesen Fällen wird das Zugriffstoken als `access_token`Abfragezeichenfolgenwert bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="34952-669">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="34952-670">Im .NET-Client `AccessTokenProvider` kann die Option mithilfe der `WithUrl`Optionen delegate in angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="34952-670">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="34952-671">Im JavaScript-Client wird das Zugriffstoken konfiguriert, indem das `accessTokenFactory` `withUrl`Feld für das Optionsobjekt in :</span><span class="sxs-lookup"><span data-stu-id="34952-671">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="34952-672">Im SignalR Java-Client können Sie ein Trägertoken für die Authentifizierung konfigurieren, indem Sie dem [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)eine Zugriffstokenfactory bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="34952-672">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="34952-673">Verwenden Sie [withAccessTokenFactory,](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) um eine [RxJava](https://github.com/ReactiveX/RxJava) [\<Single String>](https://reactivex.io/documentation/single.html)bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="34952-673">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="34952-674">Mit einem Aufruf von [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)können Sie Logik schreiben, um Zugriffstoken für Ihren Client zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="34952-674">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="34952-675">Konfigurieren von Timeout- und Keep-Alive-Optionen</span><span class="sxs-lookup"><span data-stu-id="34952-675">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="34952-676">Zusätzliche Optionen zum Konfigurieren von Timeout und `HubConnection` Keep-Alive-Verhalten sind für das Objekt selbst verfügbar:</span><span class="sxs-lookup"><span data-stu-id="34952-676">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="34952-677">.NET</span><span class="sxs-lookup"><span data-stu-id="34952-677">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="34952-678">Option</span><span class="sxs-lookup"><span data-stu-id="34952-678">Option</span></span> | <span data-ttu-id="34952-679">Standardwert</span><span class="sxs-lookup"><span data-stu-id="34952-679">Default value</span></span> | <span data-ttu-id="34952-680">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="34952-680">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="34952-681">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="34952-681">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="34952-682">Timeout für Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="34952-682">Timeout for server activity.</span></span> <span data-ttu-id="34952-683">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der `Closed` Client`onclose` die Verbindung des Servers und löst das Ereignis aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="34952-683">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="34952-684">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** vom Client innerhalb des Timeoutintervalls empfangen werden kann.</span><span class="sxs-lookup"><span data-stu-id="34952-684">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="34952-685">Der empfohlene Wert ist eine Zahl, `KeepAliveInterval` die mindestens doppelt so hoch ist wie der Wert des Servers, damit Zeit für Pings eintrifft.</span><span class="sxs-lookup"><span data-stu-id="34952-685">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="34952-686">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="34952-686">15 seconds</span></span> | <span data-ttu-id="34952-687">Timeout für den ersten Serverhandshake.</span><span class="sxs-lookup"><span data-stu-id="34952-687">Timeout for initial server handshake.</span></span> <span data-ttu-id="34952-688">Wenn der Server in diesem Intervall keine Handshake-Antwort sendet, bricht der `Closed` Client`onclose` den Handshake ab und löst das Ereignis aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="34952-688">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="34952-689">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn Handshake-Timeoutfehler aufgrund einer schwerwiegenden Netzwerklatenz auftreten.</span><span class="sxs-lookup"><span data-stu-id="34952-689">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="34952-690">Weitere Informationen zum Handshake-Prozess finden Sie in der [SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="34952-690">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="34952-691">Im .NET-Client werden Timeoutwerte `TimeSpan` als Werte angegeben.</span><span class="sxs-lookup"><span data-stu-id="34952-691">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="34952-692">JavaScript</span><span class="sxs-lookup"><span data-stu-id="34952-692">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="34952-693">Option</span><span class="sxs-lookup"><span data-stu-id="34952-693">Option</span></span> | <span data-ttu-id="34952-694">Standardwert</span><span class="sxs-lookup"><span data-stu-id="34952-694">Default value</span></span> | <span data-ttu-id="34952-695">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="34952-695">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="34952-696">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="34952-696">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="34952-697">Timeout für Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="34952-697">Timeout for server activity.</span></span> <span data-ttu-id="34952-698">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der `onclose` Client die Verbindung zum Server und löst das Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="34952-698">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="34952-699">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** vom Client innerhalb des Timeoutintervalls empfangen werden kann.</span><span class="sxs-lookup"><span data-stu-id="34952-699">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="34952-700">Der empfohlene Wert ist eine Zahl, `KeepAliveInterval` die mindestens doppelt so hoch ist wie der Wert des Servers, damit Zeit für Pings eintrifft.</span><span class="sxs-lookup"><span data-stu-id="34952-700">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="34952-701">Java</span><span class="sxs-lookup"><span data-stu-id="34952-701">Java</span></span>](#tab/java)

| <span data-ttu-id="34952-702">Option</span><span class="sxs-lookup"><span data-stu-id="34952-702">Option</span></span> | <span data-ttu-id="34952-703">Standardwert</span><span class="sxs-lookup"><span data-stu-id="34952-703">Default value</span></span> | <span data-ttu-id="34952-704">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="34952-704">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="34952-705">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="34952-705">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="34952-706">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="34952-706">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="34952-707">Timeout für Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="34952-707">Timeout for server activity.</span></span> <span data-ttu-id="34952-708">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der `onClose` Client die Verbindung zum Server und löst das Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="34952-708">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="34952-709">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** vom Client innerhalb des Timeoutintervalls empfangen werden kann.</span><span class="sxs-lookup"><span data-stu-id="34952-709">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="34952-710">Der empfohlene Wert ist eine Zahl, `KeepAliveInterval` die mindestens das Doppelte des Serverwerts beträgt, um Zeit für Pings zu haben.</span><span class="sxs-lookup"><span data-stu-id="34952-710">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="34952-711">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="34952-711">15 seconds</span></span> | <span data-ttu-id="34952-712">Timeout für den ersten Serverhandshake.</span><span class="sxs-lookup"><span data-stu-id="34952-712">Timeout for initial server handshake.</span></span> <span data-ttu-id="34952-713">Wenn der Server in diesem Intervall keine Handshake-Antwort sendet, bricht der `onClose` Client den Handshake ab und löst das Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="34952-713">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="34952-714">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn Handshake-Timeoutfehler aufgrund einer schwerwiegenden Netzwerklatenz auftreten.</span><span class="sxs-lookup"><span data-stu-id="34952-714">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="34952-715">Weitere Informationen zum Handshake-Prozess finden Sie in der [SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="34952-715">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="34952-716">Konfigurieren zusätzlicher Optionen</span><span class="sxs-lookup"><span data-stu-id="34952-716">Configure additional options</span></span>

<span data-ttu-id="34952-717">Zusätzliche Optionen können in `WithUrl` der`withUrl` ( in JavaScript) Methode auf `HubConnectionBuilder` oder `HttpHubConnectionBuilder` auf den verschiedenen Konfigurations-APIs auf dem im Java-Client konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="34952-717">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="34952-718">.NET</span><span class="sxs-lookup"><span data-stu-id="34952-718">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="34952-719">.NET-Option</span><span class="sxs-lookup"><span data-stu-id="34952-719">.NET Option</span></span> |  <span data-ttu-id="34952-720">Standardwert</span><span class="sxs-lookup"><span data-stu-id="34952-720">Default value</span></span> | <span data-ttu-id="34952-721">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="34952-721">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="34952-722">Eine Funktion, die eine Zeichenfolge zurückgibt, die als Bearer-Authentifizierungstoken in HTTP-Anforderungen bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="34952-722">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="34952-723">Legen Sie `true` diesen Vorgang fest, um den Aushandlungsschritt zu überspringen.</span><span class="sxs-lookup"><span data-stu-id="34952-723">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="34952-724">**Wird nur unterstützt, wenn der WebSockets-Transport der einzige aktivierte Transport ist.**</span><span class="sxs-lookup"><span data-stu-id="34952-724">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="34952-725">Diese Einstellung kann nicht aktiviert werden, wenn Sie den Azure SignalR-Dienst verwenden.</span><span class="sxs-lookup"><span data-stu-id="34952-725">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="34952-726">Leer</span><span class="sxs-lookup"><span data-stu-id="34952-726">Empty</span></span> | <span data-ttu-id="34952-727">Eine Auflistung von TLS-Zertifikaten, die an Authentifizierenvon Anforderungen gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="34952-727">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="34952-728">Leer</span><span class="sxs-lookup"><span data-stu-id="34952-728">Empty</span></span> | <span data-ttu-id="34952-729">Eine Sammlung von HTTP-Cookies, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="34952-729">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="34952-730">Leer</span><span class="sxs-lookup"><span data-stu-id="34952-730">Empty</span></span> | <span data-ttu-id="34952-731">Anmeldeinformationen, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="34952-731">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="34952-732">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="34952-732">5 seconds</span></span> | <span data-ttu-id="34952-733">Nur WebSockets.</span><span class="sxs-lookup"><span data-stu-id="34952-733">WebSockets only.</span></span> <span data-ttu-id="34952-734">Die maximale Zeitdauer, die der Client nach dem Schließen wartet, damit der Server die Close-Anforderung bestätigt.</span><span class="sxs-lookup"><span data-stu-id="34952-734">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="34952-735">Wenn der Server den Abschluss innerhalb dieser Zeit nicht bestätigt, trennt der Client die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="34952-735">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="34952-736">Leer</span><span class="sxs-lookup"><span data-stu-id="34952-736">Empty</span></span> | <span data-ttu-id="34952-737">Eine Zuordnung zusätzlicher HTTP-Header, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="34952-737">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="34952-738">Ein Delegat, der zum `HttpMessageHandler` Konfigurieren oder Ersetzen der zum Senden von HTTP-Anforderungen verwendeten Delegaten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="34952-738">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="34952-739">Wird nicht für WebSocket-Verbindungen verwendet.</span><span class="sxs-lookup"><span data-stu-id="34952-739">Not used for WebSocket connections.</span></span> <span data-ttu-id="34952-740">Dieser Delegat muss einen Wert ungleich NULL zurückgeben, und er empfängt den Standardwert als Parameter.</span><span class="sxs-lookup"><span data-stu-id="34952-740">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="34952-741">Ändern Sie entweder die Einstellungen für diesen Standardwert, und geben Sie ihn zurück, oder geben Sie eine neue `HttpMessageHandler` Instanz zurück.</span><span class="sxs-lookup"><span data-stu-id="34952-741">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="34952-742">**Wenn Sie den Handler ersetzen, sollten Sie die Einstellungen, die Sie vom bereitgestellten Handler beibehalten möchten, kopieren, andernfalls gelten die konfigurierten Optionen (z. B. Cookies und Header) nicht für den neuen Handler.**</span><span class="sxs-lookup"><span data-stu-id="34952-742">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="34952-743">Ein HTTP-Proxy, der beim Senden von HTTP-Anforderungen verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="34952-743">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="34952-744">Legen Sie diese boolesche Einstellung fest, um die Standardanmeldeinformationen für HTTP- und WebSockets-Anforderungen zu senden.</span><span class="sxs-lookup"><span data-stu-id="34952-744">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="34952-745">Dies ermöglicht die Verwendung der Windows-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="34952-745">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="34952-746">Ein Delegat, der zum Konfigurieren zusätzlicher WebSocket-Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="34952-746">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="34952-747">Empfängt eine Instanz von [ClientWebSocketOptions,](/dotnet/api/system.net.websockets.clientwebsocketoptions) die zum Konfigurieren der Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="34952-747">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="34952-748">JavaScript</span><span class="sxs-lookup"><span data-stu-id="34952-748">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="34952-749">JavaScript-Option</span><span class="sxs-lookup"><span data-stu-id="34952-749">JavaScript Option</span></span> | <span data-ttu-id="34952-750">Standardwert</span><span class="sxs-lookup"><span data-stu-id="34952-750">Default Value</span></span> | <span data-ttu-id="34952-751">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="34952-751">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="34952-752">Eine Funktion, die eine Zeichenfolge zurückgibt, die als Bearer-Authentifizierungstoken in HTTP-Anforderungen bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="34952-752">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="34952-753">Legen Sie `true` diesen Vorgang fest, um den Aushandlungsschritt zu überspringen.</span><span class="sxs-lookup"><span data-stu-id="34952-753">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="34952-754">**Wird nur unterstützt, wenn der WebSockets-Transport der einzige aktivierte Transport ist.**</span><span class="sxs-lookup"><span data-stu-id="34952-754">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="34952-755">Diese Einstellung kann nicht aktiviert werden, wenn Sie den Azure SignalR-Dienst verwenden.</span><span class="sxs-lookup"><span data-stu-id="34952-755">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="34952-756">Java</span><span class="sxs-lookup"><span data-stu-id="34952-756">Java</span></span>](#tab/java)

| <span data-ttu-id="34952-757">Java-Option</span><span class="sxs-lookup"><span data-stu-id="34952-757">Java Option</span></span> | <span data-ttu-id="34952-758">Standardwert</span><span class="sxs-lookup"><span data-stu-id="34952-758">Default Value</span></span> | <span data-ttu-id="34952-759">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="34952-759">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="34952-760">Eine Funktion, die eine Zeichenfolge zurückgibt, die als Bearer-Authentifizierungstoken in HTTP-Anforderungen bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="34952-760">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="34952-761">Legen Sie `true` diesen Vorgang fest, um den Aushandlungsschritt zu überspringen.</span><span class="sxs-lookup"><span data-stu-id="34952-761">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="34952-762">**Wird nur unterstützt, wenn der WebSockets-Transport der einzige aktivierte Transport ist.**</span><span class="sxs-lookup"><span data-stu-id="34952-762">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="34952-763">Diese Einstellung kann nicht aktiviert werden, wenn Sie den Azure SignalR-Dienst verwenden.</span><span class="sxs-lookup"><span data-stu-id="34952-763">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="34952-764">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="34952-764">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="34952-765">Leer</span><span class="sxs-lookup"><span data-stu-id="34952-765">Empty</span></span> | <span data-ttu-id="34952-766">Eine Zuordnung zusätzlicher HTTP-Header, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="34952-766">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="34952-767">Im .NET Client können diese Optionen durch den `WithUrl`Optionen-Delegaten geändert werden, der für:</span><span class="sxs-lookup"><span data-stu-id="34952-767">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="34952-768">Im JavaScript-Client können diese Optionen in einem JavaScript-Objekt bereitgestellt werden, `withUrl`das für:</span><span class="sxs-lookup"><span data-stu-id="34952-768">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="34952-769">Im Java-Client können diese Optionen mit den `HttpHubConnectionBuilder` Methoden auf dem zurückgegebenen`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="34952-769">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="34952-770">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="34952-770">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end

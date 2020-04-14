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
ms.openlocfilehash: 9f98a9c6371ef7e38586b0d728c670b0eecb8f6e
ms.sourcegitcommit: fbdb8b9ab5a52656384b117ff6e7c92ae070813c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81228139"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="c4d05-103">ASP.NET Core SignalR-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="c4d05-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="c4d05-104">JSON/MessagePack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="c4d05-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="c4d05-105">ASP.NET Core SignalR unterstützt zwei Protokolle für die Codierung von Nachrichten: [JSON](https://www.json.org/) und [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="c4d05-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="c4d05-106">Jedes Protokoll verfügt über Serialisierungskonfigurationsoptionen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="c4d05-107">Die JSON-Serialisierung kann auf dem Server mithilfe der [AddJsonProtocol-Erweiterungsmethode](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="c4d05-108">`AddJsonProtocol`kann nach [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) `Startup.ConfigureServices`in hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c4d05-109">Die `AddJsonProtocol` Methode nimmt einen `options` Delegaten an, der ein Objekt empfängt.</span><span class="sxs-lookup"><span data-stu-id="c4d05-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="c4d05-110">Die [PayloadSerializerOptions-Eigenschaft](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) für `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> dieses Objekt ist ein Objekt, das zum Konfigurieren der Serialisierung von Argumenten und Rückgabewerten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="c4d05-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="c4d05-111">Weitere Informationen finden Sie in der [Dokumentation System.Text.Json](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="c4d05-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="c4d05-112">Verwenden Sie beispielsweise den folgenden Code, `Startup.ConfigureServices`um den Serialisierungsmodul so zu konfigurieren, dass das Groß-/Kleinschreibung von Eigenschaftsnamen anstelle der standardmäßigen "camelCase"-Namen nicht geändert wird:</span><span class="sxs-lookup"><span data-stu-id="c4d05-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="c4d05-113">Im .NET-Client ist `AddJsonProtocol` dieselbe Erweiterungsmethode auf [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)vorhanden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="c4d05-114">Der `Microsoft.Extensions.DependencyInjection` Namespace muss importiert werden, um die Erweiterungsmethode aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="c4d05-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="c4d05-115">Es ist derzeit nicht möglich, die JSON-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="c4d05-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="c4d05-116">Wechseln zu Newtonsoft.Json</span><span class="sxs-lookup"><span data-stu-id="c4d05-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="c4d05-117">Wenn Sie Funktionen `Newtonsoft.Json` benötigen, die in `System.Text.Json`nicht unterstützt werden, siehe [Wechseln zu Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="c4d05-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="c4d05-118">MessagePack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="c4d05-118">MessagePack serialization options</span></span>

<span data-ttu-id="c4d05-119">Die MessagePack-Serialisierung kann konfiguriert werden, indem ein Delegat für den [AddMessagePackProtocol-Aufruf](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="c4d05-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="c4d05-120">Weitere Informationen finden Sie [unter MessagePack in SignalR.](xref:signalr/messagepackhubprotocol)</span><span class="sxs-lookup"><span data-stu-id="c4d05-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="c4d05-121">Es ist derzeit nicht möglich, die MessagePack-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="c4d05-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="c4d05-122">Konfigurieren von Serveroptionen</span><span class="sxs-lookup"><span data-stu-id="c4d05-122">Configure server options</span></span>

<span data-ttu-id="c4d05-123">In der folgenden Tabelle werden Optionen zum Konfigurieren von SignalR-Hubs beschrieben:</span><span class="sxs-lookup"><span data-stu-id="c4d05-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="c4d05-124">Option</span><span class="sxs-lookup"><span data-stu-id="c4d05-124">Option</span></span> | <span data-ttu-id="c4d05-125">Standardwert</span><span class="sxs-lookup"><span data-stu-id="c4d05-125">Default Value</span></span> | <span data-ttu-id="c4d05-126">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="c4d05-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="c4d05-127">30 Sekunden</span><span class="sxs-lookup"><span data-stu-id="c4d05-127">30 seconds</span></span> | <span data-ttu-id="c4d05-128">Der Server betrachtet die Verbindung des Clients, wenn er in diesem Intervall keine Nachricht (einschließlich Keep-Alive) erhalten hat.</span><span class="sxs-lookup"><span data-stu-id="c4d05-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="c4d05-129">Es kann länger dauern als dieses Timeoutintervall, bis der Client aufgrund der Implementierung tatsächlich als getrennt markiert wird.</span><span class="sxs-lookup"><span data-stu-id="c4d05-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="c4d05-130">Der empfohlene Wert `KeepAliveInterval` ist das Doppelte des Wertes.</span><span class="sxs-lookup"><span data-stu-id="c4d05-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="c4d05-131">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="c4d05-131">15 seconds</span></span> | <span data-ttu-id="c4d05-132">Wenn der Client innerhalb dieses Zeitintervalls keine erste Handshake-Nachricht sendet, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="c4d05-133">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn Handshake-Timeoutfehler aufgrund einer schwerwiegenden Netzwerklatenz auftreten.</span><span class="sxs-lookup"><span data-stu-id="c4d05-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c4d05-134">Weitere Informationen zum Handshake-Prozess finden Sie in der [SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c4d05-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c4d05-135">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="c4d05-135">15 seconds</span></span> | <span data-ttu-id="c4d05-136">Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten.</span><span class="sxs-lookup"><span data-stu-id="c4d05-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="c4d05-137">Ändern `KeepAliveInterval`Sie beim `ServerTimeout` / `serverTimeoutInMilliseconds` Ändern die Einstellung auf dem Client.</span><span class="sxs-lookup"><span data-stu-id="c4d05-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="c4d05-138">Der `ServerTimeout` / `serverTimeoutInMilliseconds` empfohlene Wert `KeepAliveInterval` ist das Doppelte des Wertes.</span><span class="sxs-lookup"><span data-stu-id="c4d05-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="c4d05-139">Alle installierten Protokolle</span><span class="sxs-lookup"><span data-stu-id="c4d05-139">All installed protocols</span></span> | <span data-ttu-id="c4d05-140">Protokolle, die von diesem Hub unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-140">Protocols supported by this hub.</span></span> <span data-ttu-id="c4d05-141">Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, Protokolle können jedoch aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="c4d05-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="c4d05-142">Wenn `true`, werden detaillierte Ausnahmemeldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="c4d05-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="c4d05-143">Der Standardwert ist `false`, da diese Ausnahmemeldungen vertrauliche Informationen enthalten können.</span><span class="sxs-lookup"><span data-stu-id="c4d05-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="c4d05-144">Die maximale Anzahl von Elementen, die für Client-Upload-Streams gepuffert werden können.</span><span class="sxs-lookup"><span data-stu-id="c4d05-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="c4d05-145">Wenn dieser Grenzwert erreicht ist, wird die Verarbeitung von Aufrufen blockiert, bis der Server Streamelemente verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="c4d05-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="c4d05-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="c4d05-146">32 KB</span></span> | <span data-ttu-id="c4d05-147">Maximale Größe einer einzelnen eingehenden Hubnachricht.</span><span class="sxs-lookup"><span data-stu-id="c4d05-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="c4d05-148">Optionen können für alle Hubs konfiguriert werden, `AddSignalR` indem `Startup.ConfigureServices`ein Optionsdelegat für den Aufruf in zur Verfügung gestellt wird.</span><span class="sxs-lookup"><span data-stu-id="c4d05-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="c4d05-149">Optionen für einen einzelnen Hub überschreiben `AddSignalR` die globalen Optionen, die in bereitgestellt werden und können mit <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>folgenden Optionen konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="c4d05-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="c4d05-150">Erweiterte HTTP-Konfigurationsoptionen</span><span class="sxs-lookup"><span data-stu-id="c4d05-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="c4d05-151">Verwenden `HttpConnectionDispatcherOptions` Sie diese Datei zum Konfigurieren erweiterter Einstellungen für Transporte und Speicherpufferverwaltung.</span><span class="sxs-lookup"><span data-stu-id="c4d05-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="c4d05-152">Diese Optionen werden konfiguriert, indem sie einen `Startup.Configure`Delegaten an [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in übergeben.</span><span class="sxs-lookup"><span data-stu-id="c4d05-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="c4d05-153">In der folgenden Tabelle werden Optionen zum Konfigurieren ASP.NET erweiterten HTTP-Optionen von Core SignalR beschrieben:</span><span class="sxs-lookup"><span data-stu-id="c4d05-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="c4d05-154">Option</span><span class="sxs-lookup"><span data-stu-id="c4d05-154">Option</span></span> | <span data-ttu-id="c4d05-155">Standardwert</span><span class="sxs-lookup"><span data-stu-id="c4d05-155">Default Value</span></span> | <span data-ttu-id="c4d05-156">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="c4d05-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="c4d05-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="c4d05-157">32 KB</span></span> | <span data-ttu-id="c4d05-158">Die maximale Anzahl von Bytes, die vom Client empfangen werden, die der Server puffert, bevor der Gegendruck angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="c4d05-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="c4d05-159">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten schneller empfangen, ohne Gegendruck anzuwenden, kann jedoch den Speicherverbrauch erhöhen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="c4d05-160">Daten, die `Authorize` automatisch aus den Attributen gesammelt werden, die auf die Hub-Klasse angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="c4d05-161">Eine Liste von [IAuthorizeData-Objekten,](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) die verwendet werden, um zu bestimmen, ob ein Client zum Herstellen einer Verbindung mit dem Hub autorisiert ist.</span><span class="sxs-lookup"><span data-stu-id="c4d05-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="c4d05-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="c4d05-162">32 KB</span></span> | <span data-ttu-id="c4d05-163">Die maximale Anzahl von Bytes, die von der App gesendet werden, die der Server puffert, bevor die Rückdruckmenge beobachtet wird.</span><span class="sxs-lookup"><span data-stu-id="c4d05-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="c4d05-164">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten schneller puffern, ohne auf Gegendruck zu warten, kann jedoch den Speicherverbrauch erhöhen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="c4d05-165">Alle Transporte sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="c4d05-165">All Transports are enabled.</span></span> | <span data-ttu-id="c4d05-166">Eine Bit-Flags-Enumere von `HttpTransportType` Werten, die die Transporte einschränken können, die ein Client zum Herstellen einer Verbindung verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="c4d05-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="c4d05-167">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="c4d05-167">See below.</span></span> | <span data-ttu-id="c4d05-168">Zusätzliche Optionen speziell für den Long Polling-Transport.</span><span class="sxs-lookup"><span data-stu-id="c4d05-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="c4d05-169">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="c4d05-169">See below.</span></span> | <span data-ttu-id="c4d05-170">Zusätzliche Optionen, die für den WebSockets-Transport spezifisch sind.</span><span class="sxs-lookup"><span data-stu-id="c4d05-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="c4d05-171">0</span><span class="sxs-lookup"><span data-stu-id="c4d05-171">0</span></span> | <span data-ttu-id="c4d05-172">Geben Sie die Mindestversion des Aushandelnsprotokolls an.</span><span class="sxs-lookup"><span data-stu-id="c4d05-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="c4d05-173">Dies wird verwendet, um Clients auf neuere Versionen zu beschränken.</span><span class="sxs-lookup"><span data-stu-id="c4d05-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="c4d05-174">Der Long Polling-Transport verfügt über zusätzliche `LongPolling` Optionen, die mithilfe der Eigenschaft konfiguriert werden können:</span><span class="sxs-lookup"><span data-stu-id="c4d05-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="c4d05-175">Option</span><span class="sxs-lookup"><span data-stu-id="c4d05-175">Option</span></span> | <span data-ttu-id="c4d05-176">Standardwert</span><span class="sxs-lookup"><span data-stu-id="c4d05-176">Default Value</span></span> | <span data-ttu-id="c4d05-177">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="c4d05-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="c4d05-178">90 Sekunden</span><span class="sxs-lookup"><span data-stu-id="c4d05-178">90 seconds</span></span> | <span data-ttu-id="c4d05-179">Die maximale Zeit, die der Server auf das Senden einer Nachricht an den Client wartet, bevor eine einzelne Abfrageanforderung beendet wird.</span><span class="sxs-lookup"><span data-stu-id="c4d05-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="c4d05-180">Wenn Sie diesen Wert verringern, gibt der Client häufiger neue Abfrageanforderungen aus.</span><span class="sxs-lookup"><span data-stu-id="c4d05-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="c4d05-181">Der WebSocket-Transport verfügt über zusätzliche Optionen, die mithilfe der `WebSockets` Eigenschaft konfiguriert werden können:</span><span class="sxs-lookup"><span data-stu-id="c4d05-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="c4d05-182">Option</span><span class="sxs-lookup"><span data-stu-id="c4d05-182">Option</span></span> | <span data-ttu-id="c4d05-183">Standardwert</span><span class="sxs-lookup"><span data-stu-id="c4d05-183">Default Value</span></span> | <span data-ttu-id="c4d05-184">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="c4d05-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="c4d05-185">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="c4d05-185">5 seconds</span></span> | <span data-ttu-id="c4d05-186">Wenn der Client nicht innerhalb dieses Zeitintervalls geschlossen werden kann, wird die Verbindung beendet, nachdem der Server geschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="c4d05-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="c4d05-187">Ein Delegat, der `Sec-WebSocket-Protocol` verwendet werden kann, um den Header auf einen benutzerdefinierten Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="c4d05-188">Der Delegat empfängt die vom Client angeforderten Werte als Eingabe und wird voraussichtlich den gewünschten Wert zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="c4d05-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="c4d05-189">Konfigurieren von Clientoptionen</span><span class="sxs-lookup"><span data-stu-id="c4d05-189">Configure client options</span></span>

<span data-ttu-id="c4d05-190">Clientoptionen können für den `HubConnectionBuilder` Typ konfiguriert werden (verfügbar in den .NET- und JavaScript-Clients).</span><span class="sxs-lookup"><span data-stu-id="c4d05-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="c4d05-191">Es ist auch im Java-Client `HttpHubConnectionBuilder` verfügbar, aber die Unterklasse ist, was `HubConnection` die Builder-Konfigurationsoptionen enthält, sowie auf der selbst.</span><span class="sxs-lookup"><span data-stu-id="c4d05-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="c4d05-192">Konfigurieren der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="c4d05-192">Configure logging</span></span>

<span data-ttu-id="c4d05-193">Die Protokollierung wird im .NET-Client mit der `ConfigureLogging` Methode konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="c4d05-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="c4d05-194">Protokollierungsanbieter und Filter können auf die gleiche Weise registriert werden wie auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="c4d05-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="c4d05-195">Weitere Informationen finden Sie in der Dokumentation ["Anmelden in ASP.NET Kern".](xref:fundamentals/logging/index)</span><span class="sxs-lookup"><span data-stu-id="c4d05-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="c4d05-196">Um Logging-Anbieter zu registrieren, müssen Sie die erforderlichen Pakete installieren.</span><span class="sxs-lookup"><span data-stu-id="c4d05-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="c4d05-197">Eine vollständige Liste finden Sie im Abschnitt [Integrierte Protokollierungsanbieter](xref:fundamentals/logging/index#built-in-logging-providers) in den Dokumenten.</span><span class="sxs-lookup"><span data-stu-id="c4d05-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="c4d05-198">Um beispielsweise die Konsolenprotokollierung `Microsoft.Extensions.Logging.Console` zu aktivieren, installieren Sie das NuGet-Paket.</span><span class="sxs-lookup"><span data-stu-id="c4d05-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="c4d05-199">Rufen `AddConsole` Sie die Erweiterungsmethode auf:</span><span class="sxs-lookup"><span data-stu-id="c4d05-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="c4d05-200">Im JavaScript-Client ist `configureLogging` eine ähnliche Methode vorhanden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="c4d05-201">Geben `LogLevel` Sie einen Wert an, der die Mindestebene der zu erzeugenden Protokollnachrichten angibt.</span><span class="sxs-lookup"><span data-stu-id="c4d05-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="c4d05-202">Protokolle werden in das Browserkonsolenfenster geschrieben.</span><span class="sxs-lookup"><span data-stu-id="c4d05-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="c4d05-203">Anstelle eines `LogLevel` Werts können Sie `string` auch einen Wert angeben, der einen Namen auf Protokollebene darstellt.</span><span class="sxs-lookup"><span data-stu-id="c4d05-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="c4d05-204">Dies ist nützlich, wenn Sie die SignalR-Protokollierung `LogLevel` in Umgebungen konfigurieren, in denen Sie keinen Zugriff auf die Konstanten haben.</span><span class="sxs-lookup"><span data-stu-id="c4d05-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="c4d05-205">In der folgenden Tabelle sind die verfügbaren Protokollebenen aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="c4d05-205">The following table lists the available log levels.</span></span> <span data-ttu-id="c4d05-206">Der Wert, `configureLogging` den Sie angeben, um die **minimale** Protokollebene festzulegen, die protokolliert wird.</span><span class="sxs-lookup"><span data-stu-id="c4d05-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="c4d05-207">Nachrichten, die auf dieser Ebene protokolliert werden, **oder die ebenen, die danach in der Tabelle aufgeführt**sind, werden protokolliert.</span><span class="sxs-lookup"><span data-stu-id="c4d05-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="c4d05-208">String</span><span class="sxs-lookup"><span data-stu-id="c4d05-208">String</span></span>                      | <span data-ttu-id="c4d05-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="c4d05-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="c4d05-210">`info`**oder**`information`</span><span class="sxs-lookup"><span data-stu-id="c4d05-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="c4d05-211">`warn`**oder**`warning`</span><span class="sxs-lookup"><span data-stu-id="c4d05-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="c4d05-212">Um die Protokollierung `signalR.LogLevel.None` vollständig `configureLogging` zu deaktivieren, geben Sie in der Methode an.</span><span class="sxs-lookup"><span data-stu-id="c4d05-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="c4d05-213">Weitere Informationen zur Protokollierung finden Sie in der [SignalR-Diagnosedokumentation](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="c4d05-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="c4d05-214">Der SignalR Java-Client verwendet die [SLF4J-Bibliothek](https://www.slf4j.org/) für die Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="c4d05-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="c4d05-215">Es handelt sich um eine api auf hoher Ebene, die es Benutzern der Bibliothek ermöglicht, ihre eigene spezifische Protokollierungsimplementierung zu wählen, indem sie eine bestimmte Protokollierungsabhängigkeit einführt.</span><span class="sxs-lookup"><span data-stu-id="c4d05-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="c4d05-216">Der folgende Codeausschnitt zeigt, wie `java.util.logging` Sie mit dem SignalR Java-Client verwenden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="c4d05-217">Wenn Sie die Protokollierung in Ihren Abhängigkeiten nicht konfigurieren, lädt SLF4J einen standardmäßigen Nicht-Betriebsprotokollmitlogger mit der folgenden Warnmeldung:</span><span class="sxs-lookup"><span data-stu-id="c4d05-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="c4d05-218">Dies kann getrost ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="c4d05-219">Konfigurieren zulässiger Transporte</span><span class="sxs-lookup"><span data-stu-id="c4d05-219">Configure allowed transports</span></span>

<span data-ttu-id="c4d05-220">Die von SignalR verwendeten Transporte können `WithUrl` im`withUrl` Aufruf (in JavaScript) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="c4d05-221">Ein bitweises-ODER der `HttpTransportType` Werte von kann verwendet werden, um den Client darauf zu beschränken, nur die angegebenen Transporte zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="c4d05-222">Alle Transporte sind standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="c4d05-222">All transports are enabled by default.</span></span>

<span data-ttu-id="c4d05-223">Um z. B. den Transport servergesendeter Ereignisse zu deaktivieren, aber WebSockets- und Long Polling-Verbindungen zuzulassen:</span><span class="sxs-lookup"><span data-stu-id="c4d05-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="c4d05-224">Im JavaScript-Client werden Transporte konfiguriert, `transport` indem das Feld `withUrl`für das Optionsobjekt festgelegt wird, das für:</span><span class="sxs-lookup"><span data-stu-id="c4d05-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="c4d05-225">In dieser Version des Java-Client-Websockets ist der einzige verfügbare Transport.</span><span class="sxs-lookup"><span data-stu-id="c4d05-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="c4d05-226">Im Java-Client wird der Transport `withTransport` mit `HttpHubConnectionBuilder`der Methode auf der ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="c4d05-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="c4d05-227">Der Java-Client verwendet standardmäßig den WebSockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="c4d05-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="c4d05-228">Der SignalR Java-Client unterstützt den Transport-Fallback noch nicht.</span><span class="sxs-lookup"><span data-stu-id="c4d05-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="c4d05-229">Konfigurieren der Bearer-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="c4d05-229">Configure bearer authentication</span></span>

<span data-ttu-id="c4d05-230">Um Authentifizierungsdaten zusammen mit SignalR-Anforderungen bereitzustellen, verwenden Sie die `AccessTokenProvider` Option (in`accessTokenFactory` JavaScript), um eine Funktion anzugeben, die das gewünschte Zugriffstoken zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="c4d05-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="c4d05-231">Im .NET Client wird dieses Zugriffstoken als HTTP-Token "Bearer `Authorization` Authentication" übergeben `Bearer`(Verwenden des Headers mit einem Typ von ).</span><span class="sxs-lookup"><span data-stu-id="c4d05-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="c4d05-232">Im JavaScript-Client wird das Zugriffstoken als Bearer-Token verwendet, **außer** in einigen Fällen, in denen Browser-APIs die Möglichkeit einschränken, Header anzuwenden (insbesondere in Server-Sent Events und WebSockets-Anforderungen).</span><span class="sxs-lookup"><span data-stu-id="c4d05-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="c4d05-233">In diesen Fällen wird das Zugriffstoken als `access_token`Abfragezeichenfolgenwert bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="c4d05-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="c4d05-234">Im .NET-Client `AccessTokenProvider` kann die Option mithilfe der `WithUrl`Optionen delegate in angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="c4d05-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="c4d05-235">Im JavaScript-Client wird das Zugriffstoken konfiguriert, indem das `accessTokenFactory` `withUrl`Feld für das Optionsobjekt in :</span><span class="sxs-lookup"><span data-stu-id="c4d05-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="c4d05-236">Im SignalR Java-Client können Sie ein Trägertoken für die Authentifizierung konfigurieren, indem Sie dem [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)eine Zugriffstokenfactory bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="c4d05-237">Verwenden Sie [withAccessTokenFactory,](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) um eine [RxJava](https://github.com/ReactiveX/RxJava) [\<Single String>](https://reactivex.io/documentation/single.html)bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="c4d05-238">Mit einem Aufruf von [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)können Sie Logik schreiben, um Zugriffstoken für Ihren Client zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="c4d05-239">Konfigurieren von Timeout- und Keep-Alive-Optionen</span><span class="sxs-lookup"><span data-stu-id="c4d05-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="c4d05-240">Zusätzliche Optionen zum Konfigurieren von Timeout und `HubConnection` Keep-Alive-Verhalten sind für das Objekt selbst verfügbar:</span><span class="sxs-lookup"><span data-stu-id="c4d05-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="c4d05-241">.NET</span><span class="sxs-lookup"><span data-stu-id="c4d05-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c4d05-242">Option</span><span class="sxs-lookup"><span data-stu-id="c4d05-242">Option</span></span> | <span data-ttu-id="c4d05-243">Standardwert</span><span class="sxs-lookup"><span data-stu-id="c4d05-243">Default value</span></span> | <span data-ttu-id="c4d05-244">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="c4d05-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="c4d05-245">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="c4d05-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c4d05-246">Timeout für Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="c4d05-246">Timeout for server activity.</span></span> <span data-ttu-id="c4d05-247">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der `Closed` Client`onclose` die Verbindung des Servers und löst das Ereignis aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c4d05-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c4d05-248">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** vom Client innerhalb des Timeoutintervalls empfangen werden kann.</span><span class="sxs-lookup"><span data-stu-id="c4d05-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c4d05-249">Der empfohlene Wert ist eine Zahl, `KeepAliveInterval` die mindestens doppelt so hoch ist wie der Wert des Servers, damit Zeit für Pings eintrifft.</span><span class="sxs-lookup"><span data-stu-id="c4d05-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="c4d05-250">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="c4d05-250">15 seconds</span></span> | <span data-ttu-id="c4d05-251">Timeout für den ersten Serverhandshake.</span><span class="sxs-lookup"><span data-stu-id="c4d05-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="c4d05-252">Wenn der Server in diesem Intervall keine Handshake-Antwort sendet, bricht der `Closed` Client`onclose` den Handshake ab und löst das Ereignis aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c4d05-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c4d05-253">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn Handshake-Timeoutfehler aufgrund einer schwerwiegenden Netzwerklatenz auftreten.</span><span class="sxs-lookup"><span data-stu-id="c4d05-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c4d05-254">Weitere Informationen zum Handshake-Prozess finden Sie in der [SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c4d05-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c4d05-255">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="c4d05-255">15 seconds</span></span> | <span data-ttu-id="c4d05-256">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="c4d05-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c4d05-257">Wenn Sie eine Nachricht vom Client senden, wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="c4d05-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c4d05-258">Wenn der Client keine Nachricht in `ClientTimeoutInterval` der Gruppe auf dem Server gesendet hat, betrachtet der Server die Verbindung zum Client.</span><span class="sxs-lookup"><span data-stu-id="c4d05-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="c4d05-259">Im .NET-Client werden Timeoutwerte `TimeSpan` als Werte angegeben.</span><span class="sxs-lookup"><span data-stu-id="c4d05-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="c4d05-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c4d05-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c4d05-261">Option</span><span class="sxs-lookup"><span data-stu-id="c4d05-261">Option</span></span> | <span data-ttu-id="c4d05-262">Standardwert</span><span class="sxs-lookup"><span data-stu-id="c4d05-262">Default value</span></span> | <span data-ttu-id="c4d05-263">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="c4d05-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="c4d05-264">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="c4d05-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c4d05-265">Timeout für Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="c4d05-265">Timeout for server activity.</span></span> <span data-ttu-id="c4d05-266">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der `onclose` Client die Verbindung zum Server und löst das Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="c4d05-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="c4d05-267">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** vom Client innerhalb des Timeoutintervalls empfangen werden kann.</span><span class="sxs-lookup"><span data-stu-id="c4d05-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c4d05-268">Der empfohlene Wert ist eine Zahl, `KeepAliveInterval` die mindestens doppelt so hoch ist wie der Wert des Servers, damit Zeit für Pings eintrifft.</span><span class="sxs-lookup"><span data-stu-id="c4d05-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="c4d05-269">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="c4d05-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="c4d05-270">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="c4d05-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c4d05-271">Wenn Sie eine Nachricht vom Client senden, wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="c4d05-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c4d05-272">Wenn der Client keine Nachricht in `ClientTimeoutInterval` der Gruppe auf dem Server gesendet hat, betrachtet der Server die Verbindung zum Client.</span><span class="sxs-lookup"><span data-stu-id="c4d05-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c4d05-273">Java</span><span class="sxs-lookup"><span data-stu-id="c4d05-273">Java</span></span>](#tab/java)

| <span data-ttu-id="c4d05-274">Option</span><span class="sxs-lookup"><span data-stu-id="c4d05-274">Option</span></span> | <span data-ttu-id="c4d05-275">Standardwert</span><span class="sxs-lookup"><span data-stu-id="c4d05-275">Default value</span></span> | <span data-ttu-id="c4d05-276">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="c4d05-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="c4d05-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="c4d05-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="c4d05-278">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="c4d05-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c4d05-279">Timeout für Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="c4d05-279">Timeout for server activity.</span></span> <span data-ttu-id="c4d05-280">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der `onClose` Client die Verbindung zum Server und löst das Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="c4d05-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="c4d05-281">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** vom Client innerhalb des Timeoutintervalls empfangen werden kann.</span><span class="sxs-lookup"><span data-stu-id="c4d05-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c4d05-282">Der empfohlene Wert ist eine Zahl, `KeepAliveInterval` die mindestens doppelt so hoch ist wie der Wert des Servers, damit Zeit für Pings eintrifft.</span><span class="sxs-lookup"><span data-stu-id="c4d05-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="c4d05-283">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="c4d05-283">15 seconds</span></span> | <span data-ttu-id="c4d05-284">Timeout für den ersten Serverhandshake.</span><span class="sxs-lookup"><span data-stu-id="c4d05-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="c4d05-285">Wenn der Server in diesem Intervall keine Handshake-Antwort sendet, bricht der `onClose` Client den Handshake ab und löst das Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="c4d05-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="c4d05-286">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn Handshake-Timeoutfehler aufgrund einer schwerwiegenden Netzwerklatenz auftreten.</span><span class="sxs-lookup"><span data-stu-id="c4d05-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c4d05-287">Weitere Informationen zum Handshake-Prozess finden Sie in der [SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c4d05-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="c4d05-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="c4d05-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="c4d05-289">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="c4d05-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="c4d05-290">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="c4d05-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c4d05-291">Wenn Sie eine Nachricht vom Client senden, wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="c4d05-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c4d05-292">Wenn der Client keine Nachricht in `ClientTimeoutInterval` der Gruppe auf dem Server gesendet hat, betrachtet der Server die Verbindung zum Client.</span><span class="sxs-lookup"><span data-stu-id="c4d05-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="c4d05-293">Konfigurieren zusätzlicher Optionen</span><span class="sxs-lookup"><span data-stu-id="c4d05-293">Configure additional options</span></span>

<span data-ttu-id="c4d05-294">Zusätzliche Optionen können in `WithUrl` der`withUrl` ( in JavaScript) Methode auf `HubConnectionBuilder` oder `HttpHubConnectionBuilder` auf den verschiedenen Konfigurations-APIs auf dem im Java-Client konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="c4d05-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="c4d05-295">.NET</span><span class="sxs-lookup"><span data-stu-id="c4d05-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c4d05-296">.NET-Option</span><span class="sxs-lookup"><span data-stu-id="c4d05-296">.NET Option</span></span> |  <span data-ttu-id="c4d05-297">Standardwert</span><span class="sxs-lookup"><span data-stu-id="c4d05-297">Default value</span></span> | <span data-ttu-id="c4d05-298">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="c4d05-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="c4d05-299">Eine Funktion, die eine Zeichenfolge zurückgibt, die als Bearer-Authentifizierungstoken in HTTP-Anforderungen bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="c4d05-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="c4d05-300">Legen Sie `true` diesen Vorgang fest, um den Aushandlungsschritt zu überspringen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c4d05-301">**Wird nur unterstützt, wenn der WebSockets-Transport der einzige aktivierte Transport ist.**</span><span class="sxs-lookup"><span data-stu-id="c4d05-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c4d05-302">Diese Einstellung kann nicht aktiviert werden, wenn Sie den Azure SignalR-Dienst verwenden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="c4d05-303">Leer</span><span class="sxs-lookup"><span data-stu-id="c4d05-303">Empty</span></span> | <span data-ttu-id="c4d05-304">Eine Auflistung von TLS-Zertifikaten, die an Authentifizierenvon Anforderungen gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="c4d05-305">Leer</span><span class="sxs-lookup"><span data-stu-id="c4d05-305">Empty</span></span> | <span data-ttu-id="c4d05-306">Eine Sammlung von HTTP-Cookies, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="c4d05-307">Leer</span><span class="sxs-lookup"><span data-stu-id="c4d05-307">Empty</span></span> | <span data-ttu-id="c4d05-308">Anmeldeinformationen, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="c4d05-309">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="c4d05-309">5 seconds</span></span> | <span data-ttu-id="c4d05-310">Nur WebSockets.</span><span class="sxs-lookup"><span data-stu-id="c4d05-310">WebSockets only.</span></span> <span data-ttu-id="c4d05-311">Die maximale Zeitdauer, die der Client nach dem Schließen wartet, damit der Server die Close-Anforderung bestätigt.</span><span class="sxs-lookup"><span data-stu-id="c4d05-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="c4d05-312">Wenn der Server den Abschluss innerhalb dieser Zeit nicht bestätigt, trennt der Client die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="c4d05-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="c4d05-313">Leer</span><span class="sxs-lookup"><span data-stu-id="c4d05-313">Empty</span></span> | <span data-ttu-id="c4d05-314">Eine Zuordnung zusätzlicher HTTP-Header, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="c4d05-315">Ein Delegat, der zum `HttpMessageHandler` Konfigurieren oder Ersetzen der zum Senden von HTTP-Anforderungen verwendeten Delegaten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="c4d05-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="c4d05-316">Wird nicht für WebSocket-Verbindungen verwendet.</span><span class="sxs-lookup"><span data-stu-id="c4d05-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="c4d05-317">Dieser Delegat muss einen Wert ungleich NULL zurückgeben, und er empfängt den Standardwert als Parameter.</span><span class="sxs-lookup"><span data-stu-id="c4d05-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="c4d05-318">Ändern Sie entweder die Einstellungen für diesen Standardwert, und geben Sie ihn zurück, oder geben Sie eine neue `HttpMessageHandler` Instanz zurück.</span><span class="sxs-lookup"><span data-stu-id="c4d05-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="c4d05-319">**Wenn Sie den Handler ersetzen, sollten Sie die Einstellungen, die Sie vom bereitgestellten Handler beibehalten möchten, kopieren, andernfalls gelten die konfigurierten Optionen (z. B. Cookies und Header) nicht für den neuen Handler.**</span><span class="sxs-lookup"><span data-stu-id="c4d05-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="c4d05-320">Ein HTTP-Proxy, der beim Senden von HTTP-Anforderungen verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="c4d05-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="c4d05-321">Legen Sie diese boolesche Einstellung fest, um die Standardanmeldeinformationen für HTTP- und WebSockets-Anforderungen zu senden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="c4d05-322">Dies ermöglicht die Verwendung der Windows-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="c4d05-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="c4d05-323">Ein Delegat, der zum Konfigurieren zusätzlicher WebSocket-Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="c4d05-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="c4d05-324">Empfängt eine Instanz von [ClientWebSocketOptions,](/dotnet/api/system.net.websockets.clientwebsocketoptions) die zum Konfigurieren der Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="c4d05-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="c4d05-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c4d05-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c4d05-326">JavaScript-Option</span><span class="sxs-lookup"><span data-stu-id="c4d05-326">JavaScript Option</span></span> | <span data-ttu-id="c4d05-327">Standardwert</span><span class="sxs-lookup"><span data-stu-id="c4d05-327">Default Value</span></span> | <span data-ttu-id="c4d05-328">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="c4d05-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="c4d05-329">Eine Funktion, die eine Zeichenfolge zurückgibt, die als Bearer-Authentifizierungstoken in HTTP-Anforderungen bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="c4d05-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="c4d05-330">Legen Sie `true` diesen Vorgang fest, um den Aushandlungsschritt zu überspringen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-330">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c4d05-331">**Wird nur unterstützt, wenn der WebSockets-Transport der einzige aktivierte Transport ist.**</span><span class="sxs-lookup"><span data-stu-id="c4d05-331">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c4d05-332">Diese Einstellung kann nicht aktiviert werden, wenn Sie den Azure SignalR-Dienst verwenden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-332">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c4d05-333">Java</span><span class="sxs-lookup"><span data-stu-id="c4d05-333">Java</span></span>](#tab/java)

| <span data-ttu-id="c4d05-334">Java-Option</span><span class="sxs-lookup"><span data-stu-id="c4d05-334">Java Option</span></span> | <span data-ttu-id="c4d05-335">Standardwert</span><span class="sxs-lookup"><span data-stu-id="c4d05-335">Default Value</span></span> | <span data-ttu-id="c4d05-336">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="c4d05-336">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="c4d05-337">Eine Funktion, die eine Zeichenfolge zurückgibt, die als Bearer-Authentifizierungstoken in HTTP-Anforderungen bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="c4d05-337">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="c4d05-338">Legen Sie `true` diesen Vorgang fest, um den Aushandlungsschritt zu überspringen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-338">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c4d05-339">**Wird nur unterstützt, wenn der WebSockets-Transport der einzige aktivierte Transport ist.**</span><span class="sxs-lookup"><span data-stu-id="c4d05-339">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c4d05-340">Diese Einstellung kann nicht aktiviert werden, wenn Sie den Azure SignalR-Dienst verwenden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-340">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="c4d05-341">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="c4d05-341">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="c4d05-342">Leer</span><span class="sxs-lookup"><span data-stu-id="c4d05-342">Empty</span></span> | <span data-ttu-id="c4d05-343">Eine Zuordnung zusätzlicher HTTP-Header, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-343">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="c4d05-344">Im .NET Client können diese Optionen durch den `WithUrl`Optionen-Delegaten geändert werden, der für:</span><span class="sxs-lookup"><span data-stu-id="c4d05-344">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="c4d05-345">Im JavaScript-Client können diese Optionen in einem JavaScript-Objekt bereitgestellt werden, `withUrl`das für:</span><span class="sxs-lookup"><span data-stu-id="c4d05-345">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="c4d05-346">Im Java-Client können diese Optionen mit den `HttpHubConnectionBuilder` Methoden auf dem zurückgegebenen`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="c4d05-346">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="c4d05-347">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="c4d05-347">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="c4d05-348">JSON/MessagePack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="c4d05-348">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="c4d05-349">ASP.NET Core SignalR unterstützt zwei Protokolle für die Codierung von Nachrichten: [JSON](https://www.json.org/) und [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="c4d05-349">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="c4d05-350">Jedes Protokoll verfügt über Serialisierungskonfigurationsoptionen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-350">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="c4d05-351">Die JSON-Serialisierung kann auf dem Server mithilfe der [AddJsonProtocol-Erweiterungsmethode](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-351">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="c4d05-352">`AddJsonProtocol`kann nach [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) `Startup.ConfigureServices`in hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-352">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c4d05-353">Die `AddJsonProtocol` Methode nimmt einen `options` Delegaten an, der ein Objekt empfängt.</span><span class="sxs-lookup"><span data-stu-id="c4d05-353">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="c4d05-354">Die [PayloadSerializerOptions-Eigenschaft](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) für `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> dieses Objekt ist ein Objekt, das zum Konfigurieren der Serialisierung von Argumenten und Rückgabewerten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="c4d05-354">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="c4d05-355">Weitere Informationen finden Sie in der [Dokumentation System.Text.Json](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="c4d05-355">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="c4d05-356">Verwenden Sie beispielsweise den folgenden Code, `Startup.ConfigureServices`um den Serialisierungsmodul so zu konfigurieren, dass das Groß-/Kleinschreibung von Eigenschaftsnamen anstelle der standardmäßigen "camelCase"-Namen nicht geändert wird:</span><span class="sxs-lookup"><span data-stu-id="c4d05-356">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="c4d05-357">Im .NET-Client ist `AddJsonProtocol` dieselbe Erweiterungsmethode auf [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)vorhanden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-357">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="c4d05-358">Der `Microsoft.Extensions.DependencyInjection` Namespace muss importiert werden, um die Erweiterungsmethode aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="c4d05-358">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="c4d05-359">Es ist derzeit nicht möglich, die JSON-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="c4d05-359">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="c4d05-360">Wechseln zu Newtonsoft.Json</span><span class="sxs-lookup"><span data-stu-id="c4d05-360">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="c4d05-361">Wenn Sie Funktionen `Newtonsoft.Json` benötigen, die in `System.Text.Json`nicht unterstützt werden, siehe [Wechseln zu Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="c4d05-361">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="c4d05-362">MessagePack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="c4d05-362">MessagePack serialization options</span></span>

<span data-ttu-id="c4d05-363">Die MessagePack-Serialisierung kann konfiguriert werden, indem ein Delegat für den [AddMessagePackProtocol-Aufruf](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="c4d05-363">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="c4d05-364">Weitere Informationen finden Sie [unter MessagePack in SignalR.](xref:signalr/messagepackhubprotocol)</span><span class="sxs-lookup"><span data-stu-id="c4d05-364">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="c4d05-365">Es ist derzeit nicht möglich, die MessagePack-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="c4d05-365">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="c4d05-366">Konfigurieren von Serveroptionen</span><span class="sxs-lookup"><span data-stu-id="c4d05-366">Configure server options</span></span>

<span data-ttu-id="c4d05-367">In der folgenden Tabelle werden Optionen zum Konfigurieren von SignalR-Hubs beschrieben:</span><span class="sxs-lookup"><span data-stu-id="c4d05-367">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="c4d05-368">Option</span><span class="sxs-lookup"><span data-stu-id="c4d05-368">Option</span></span> | <span data-ttu-id="c4d05-369">Standardwert</span><span class="sxs-lookup"><span data-stu-id="c4d05-369">Default Value</span></span> | <span data-ttu-id="c4d05-370">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="c4d05-370">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="c4d05-371">30 Sekunden</span><span class="sxs-lookup"><span data-stu-id="c4d05-371">30 seconds</span></span> | <span data-ttu-id="c4d05-372">Der Server betrachtet die Verbindung des Clients, wenn er in diesem Intervall keine Nachricht (einschließlich Keep-Alive) erhalten hat.</span><span class="sxs-lookup"><span data-stu-id="c4d05-372">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="c4d05-373">Es kann länger dauern als dieses Timeoutintervall, bis der Client aufgrund der Implementierung tatsächlich als getrennt markiert wird.</span><span class="sxs-lookup"><span data-stu-id="c4d05-373">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="c4d05-374">Der empfohlene Wert `KeepAliveInterval` ist das Doppelte des Wertes.</span><span class="sxs-lookup"><span data-stu-id="c4d05-374">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="c4d05-375">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="c4d05-375">15 seconds</span></span> | <span data-ttu-id="c4d05-376">Wenn der Client innerhalb dieses Zeitintervalls keine erste Handshake-Nachricht sendet, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-376">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="c4d05-377">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn Handshake-Timeoutfehler aufgrund einer schwerwiegenden Netzwerklatenz auftreten.</span><span class="sxs-lookup"><span data-stu-id="c4d05-377">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c4d05-378">Weitere Informationen zum Handshake-Prozess finden Sie in der [SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c4d05-378">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c4d05-379">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="c4d05-379">15 seconds</span></span> | <span data-ttu-id="c4d05-380">Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten.</span><span class="sxs-lookup"><span data-stu-id="c4d05-380">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="c4d05-381">Ändern `KeepAliveInterval`Sie beim `ServerTimeout` / `serverTimeoutInMilliseconds` Ändern die Einstellung auf dem Client.</span><span class="sxs-lookup"><span data-stu-id="c4d05-381">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="c4d05-382">Der `ServerTimeout` / `serverTimeoutInMilliseconds` empfohlene Wert `KeepAliveInterval` ist das Doppelte des Wertes.</span><span class="sxs-lookup"><span data-stu-id="c4d05-382">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="c4d05-383">Alle installierten Protokolle</span><span class="sxs-lookup"><span data-stu-id="c4d05-383">All installed protocols</span></span> | <span data-ttu-id="c4d05-384">Protokolle, die von diesem Hub unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-384">Protocols supported by this hub.</span></span> <span data-ttu-id="c4d05-385">Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, Protokolle können jedoch aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="c4d05-385">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="c4d05-386">Wenn `true`, werden detaillierte Ausnahmemeldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="c4d05-386">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="c4d05-387">Der Standardwert ist `false`, da diese Ausnahmemeldungen vertrauliche Informationen enthalten können.</span><span class="sxs-lookup"><span data-stu-id="c4d05-387">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="c4d05-388">Die maximale Anzahl von Elementen, die für Client-Upload-Streams gepuffert werden können.</span><span class="sxs-lookup"><span data-stu-id="c4d05-388">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="c4d05-389">Wenn dieser Grenzwert erreicht ist, wird die Verarbeitung von Aufrufen blockiert, bis der Server Streamelemente verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="c4d05-389">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="c4d05-390">32 KB</span><span class="sxs-lookup"><span data-stu-id="c4d05-390">32 KB</span></span> | <span data-ttu-id="c4d05-391">Maximale Größe einer einzelnen eingehenden Hubnachricht.</span><span class="sxs-lookup"><span data-stu-id="c4d05-391">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="c4d05-392">Optionen können für alle Hubs konfiguriert werden, `AddSignalR` indem `Startup.ConfigureServices`ein Optionsdelegat für den Aufruf in zur Verfügung gestellt wird.</span><span class="sxs-lookup"><span data-stu-id="c4d05-392">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="c4d05-393">Optionen für einen einzelnen Hub überschreiben `AddSignalR` die globalen Optionen, die in bereitgestellt werden und können mit <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>folgenden Optionen konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="c4d05-393">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="c4d05-394">Erweiterte HTTP-Konfigurationsoptionen</span><span class="sxs-lookup"><span data-stu-id="c4d05-394">Advanced HTTP configuration options</span></span>

<span data-ttu-id="c4d05-395">Verwenden `HttpConnectionDispatcherOptions` Sie diese Datei zum Konfigurieren erweiterter Einstellungen für Transporte und Speicherpufferverwaltung.</span><span class="sxs-lookup"><span data-stu-id="c4d05-395">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="c4d05-396">Diese Optionen werden konfiguriert, indem sie einen `Startup.Configure`Delegaten an [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in übergeben.</span><span class="sxs-lookup"><span data-stu-id="c4d05-396">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="c4d05-397">In der folgenden Tabelle werden Optionen zum Konfigurieren ASP.NET erweiterten HTTP-Optionen von Core SignalR beschrieben:</span><span class="sxs-lookup"><span data-stu-id="c4d05-397">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="c4d05-398">Option</span><span class="sxs-lookup"><span data-stu-id="c4d05-398">Option</span></span> | <span data-ttu-id="c4d05-399">Standardwert</span><span class="sxs-lookup"><span data-stu-id="c4d05-399">Default Value</span></span> | <span data-ttu-id="c4d05-400">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="c4d05-400">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="c4d05-401">32 KB</span><span class="sxs-lookup"><span data-stu-id="c4d05-401">32 KB</span></span> | <span data-ttu-id="c4d05-402">Die maximale Anzahl von Bytes, die vom Client empfangen werden, die der Server puffert, bevor der Gegendruck angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="c4d05-402">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="c4d05-403">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten schneller empfangen, ohne Gegendruck anzuwenden, kann jedoch den Speicherverbrauch erhöhen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-403">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="c4d05-404">Daten, die `Authorize` automatisch aus den Attributen gesammelt werden, die auf die Hub-Klasse angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-404">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="c4d05-405">Eine Liste von [IAuthorizeData-Objekten,](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) die verwendet werden, um zu bestimmen, ob ein Client zum Herstellen einer Verbindung mit dem Hub autorisiert ist.</span><span class="sxs-lookup"><span data-stu-id="c4d05-405">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="c4d05-406">32 KB</span><span class="sxs-lookup"><span data-stu-id="c4d05-406">32 KB</span></span> | <span data-ttu-id="c4d05-407">Die maximale Anzahl von Bytes, die von der App gesendet werden, die der Server puffert, bevor die Rückdruckmenge beobachtet wird.</span><span class="sxs-lookup"><span data-stu-id="c4d05-407">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="c4d05-408">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten schneller puffern, ohne auf Gegendruck zu warten, kann jedoch den Speicherverbrauch erhöhen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-408">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="c4d05-409">Alle Transporte sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="c4d05-409">All Transports are enabled.</span></span> | <span data-ttu-id="c4d05-410">Eine Bit-Flags-Enumere von `HttpTransportType` Werten, die die Transporte einschränken können, die ein Client zum Herstellen einer Verbindung verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="c4d05-410">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="c4d05-411">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="c4d05-411">See below.</span></span> | <span data-ttu-id="c4d05-412">Zusätzliche Optionen speziell für den Long Polling-Transport.</span><span class="sxs-lookup"><span data-stu-id="c4d05-412">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="c4d05-413">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="c4d05-413">See below.</span></span> | <span data-ttu-id="c4d05-414">Zusätzliche Optionen, die für den WebSockets-Transport spezifisch sind.</span><span class="sxs-lookup"><span data-stu-id="c4d05-414">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="c4d05-415">Der Long Polling-Transport verfügt über zusätzliche `LongPolling` Optionen, die mithilfe der Eigenschaft konfiguriert werden können:</span><span class="sxs-lookup"><span data-stu-id="c4d05-415">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="c4d05-416">Option</span><span class="sxs-lookup"><span data-stu-id="c4d05-416">Option</span></span> | <span data-ttu-id="c4d05-417">Standardwert</span><span class="sxs-lookup"><span data-stu-id="c4d05-417">Default Value</span></span> | <span data-ttu-id="c4d05-418">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="c4d05-418">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="c4d05-419">90 Sekunden</span><span class="sxs-lookup"><span data-stu-id="c4d05-419">90 seconds</span></span> | <span data-ttu-id="c4d05-420">Die maximale Zeit, die der Server auf das Senden einer Nachricht an den Client wartet, bevor eine einzelne Abfrageanforderung beendet wird.</span><span class="sxs-lookup"><span data-stu-id="c4d05-420">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="c4d05-421">Wenn Sie diesen Wert verringern, gibt der Client häufiger neue Abfrageanforderungen aus.</span><span class="sxs-lookup"><span data-stu-id="c4d05-421">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="c4d05-422">Der WebSocket-Transport verfügt über zusätzliche Optionen, die mithilfe der `WebSockets` Eigenschaft konfiguriert werden können:</span><span class="sxs-lookup"><span data-stu-id="c4d05-422">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="c4d05-423">Option</span><span class="sxs-lookup"><span data-stu-id="c4d05-423">Option</span></span> | <span data-ttu-id="c4d05-424">Standardwert</span><span class="sxs-lookup"><span data-stu-id="c4d05-424">Default Value</span></span> | <span data-ttu-id="c4d05-425">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="c4d05-425">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="c4d05-426">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="c4d05-426">5 seconds</span></span> | <span data-ttu-id="c4d05-427">Wenn der Client nicht innerhalb dieses Zeitintervalls geschlossen werden kann, wird die Verbindung beendet, nachdem der Server geschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="c4d05-427">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="c4d05-428">Ein Delegat, der `Sec-WebSocket-Protocol` verwendet werden kann, um den Header auf einen benutzerdefinierten Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-428">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="c4d05-429">Der Delegat empfängt die vom Client angeforderten Werte als Eingabe und wird voraussichtlich den gewünschten Wert zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="c4d05-429">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="c4d05-430">Konfigurieren von Clientoptionen</span><span class="sxs-lookup"><span data-stu-id="c4d05-430">Configure client options</span></span>

<span data-ttu-id="c4d05-431">Clientoptionen können für den `HubConnectionBuilder` Typ konfiguriert werden (verfügbar in den .NET- und JavaScript-Clients).</span><span class="sxs-lookup"><span data-stu-id="c4d05-431">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="c4d05-432">Es ist auch im Java-Client `HttpHubConnectionBuilder` verfügbar, aber die Unterklasse ist, was `HubConnection` die Builder-Konfigurationsoptionen enthält, sowie auf der selbst.</span><span class="sxs-lookup"><span data-stu-id="c4d05-432">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="c4d05-433">Konfigurieren der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="c4d05-433">Configure logging</span></span>

<span data-ttu-id="c4d05-434">Die Protokollierung wird im .NET-Client mit der `ConfigureLogging` Methode konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="c4d05-434">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="c4d05-435">Protokollierungsanbieter und Filter können auf die gleiche Weise registriert werden wie auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="c4d05-435">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="c4d05-436">Weitere Informationen finden Sie in der Dokumentation ["Anmelden in ASP.NET Kern".](xref:fundamentals/logging/index)</span><span class="sxs-lookup"><span data-stu-id="c4d05-436">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="c4d05-437">Um Logging-Anbieter zu registrieren, müssen Sie die erforderlichen Pakete installieren.</span><span class="sxs-lookup"><span data-stu-id="c4d05-437">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="c4d05-438">Eine vollständige Liste finden Sie im Abschnitt [Integrierte Protokollierungsanbieter](xref:fundamentals/logging/index#built-in-logging-providers) in den Dokumenten.</span><span class="sxs-lookup"><span data-stu-id="c4d05-438">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="c4d05-439">Um beispielsweise die Konsolenprotokollierung `Microsoft.Extensions.Logging.Console` zu aktivieren, installieren Sie das NuGet-Paket.</span><span class="sxs-lookup"><span data-stu-id="c4d05-439">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="c4d05-440">Rufen `AddConsole` Sie die Erweiterungsmethode auf:</span><span class="sxs-lookup"><span data-stu-id="c4d05-440">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="c4d05-441">Im JavaScript-Client ist `configureLogging` eine ähnliche Methode vorhanden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-441">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="c4d05-442">Geben `LogLevel` Sie einen Wert an, der die Mindestebene der zu erzeugenden Protokollnachrichten angibt.</span><span class="sxs-lookup"><span data-stu-id="c4d05-442">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="c4d05-443">Protokolle werden in das Browserkonsolenfenster geschrieben.</span><span class="sxs-lookup"><span data-stu-id="c4d05-443">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="c4d05-444">Anstelle eines `LogLevel` Werts können Sie `string` auch einen Wert angeben, der einen Namen auf Protokollebene darstellt.</span><span class="sxs-lookup"><span data-stu-id="c4d05-444">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="c4d05-445">Dies ist nützlich, wenn Sie die SignalR-Protokollierung `LogLevel` in Umgebungen konfigurieren, in denen Sie keinen Zugriff auf die Konstanten haben.</span><span class="sxs-lookup"><span data-stu-id="c4d05-445">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="c4d05-446">In der folgenden Tabelle sind die verfügbaren Protokollebenen aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="c4d05-446">The following table lists the available log levels.</span></span> <span data-ttu-id="c4d05-447">Der Wert, `configureLogging` den Sie angeben, um die **minimale** Protokollebene festzulegen, die protokolliert wird.</span><span class="sxs-lookup"><span data-stu-id="c4d05-447">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="c4d05-448">Nachrichten, die auf dieser Ebene protokolliert werden, **oder die ebenen, die danach in der Tabelle aufgeführt**sind, werden protokolliert.</span><span class="sxs-lookup"><span data-stu-id="c4d05-448">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="c4d05-449">String</span><span class="sxs-lookup"><span data-stu-id="c4d05-449">String</span></span>                      | <span data-ttu-id="c4d05-450">LogLevel</span><span class="sxs-lookup"><span data-stu-id="c4d05-450">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="c4d05-451">`info`**oder**`information`</span><span class="sxs-lookup"><span data-stu-id="c4d05-451">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="c4d05-452">`warn`**oder**`warning`</span><span class="sxs-lookup"><span data-stu-id="c4d05-452">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="c4d05-453">Um die Protokollierung `signalR.LogLevel.None` vollständig `configureLogging` zu deaktivieren, geben Sie in der Methode an.</span><span class="sxs-lookup"><span data-stu-id="c4d05-453">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="c4d05-454">Weitere Informationen zur Protokollierung finden Sie in der [SignalR-Diagnosedokumentation](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="c4d05-454">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="c4d05-455">Der SignalR Java-Client verwendet die [SLF4J-Bibliothek](https://www.slf4j.org/) für die Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="c4d05-455">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="c4d05-456">Es handelt sich um eine api auf hoher Ebene, die es Benutzern der Bibliothek ermöglicht, ihre eigene spezifische Protokollierungsimplementierung zu wählen, indem sie eine bestimmte Protokollierungsabhängigkeit einführt.</span><span class="sxs-lookup"><span data-stu-id="c4d05-456">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="c4d05-457">Der folgende Codeausschnitt zeigt, wie `java.util.logging` Sie mit dem SignalR Java-Client verwenden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-457">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="c4d05-458">Wenn Sie die Protokollierung in Ihren Abhängigkeiten nicht konfigurieren, lädt SLF4J einen standardmäßigen Nicht-Betriebsprotokollmitlogger mit der folgenden Warnmeldung:</span><span class="sxs-lookup"><span data-stu-id="c4d05-458">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="c4d05-459">Dies kann getrost ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-459">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="c4d05-460">Konfigurieren zulässiger Transporte</span><span class="sxs-lookup"><span data-stu-id="c4d05-460">Configure allowed transports</span></span>

<span data-ttu-id="c4d05-461">Die von SignalR verwendeten Transporte können `WithUrl` im`withUrl` Aufruf (in JavaScript) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-461">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="c4d05-462">Ein bitweises-ODER der `HttpTransportType` Werte von kann verwendet werden, um den Client darauf zu beschränken, nur die angegebenen Transporte zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-462">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="c4d05-463">Alle Transporte sind standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="c4d05-463">All transports are enabled by default.</span></span>

<span data-ttu-id="c4d05-464">Um z. B. den Transport servergesendeter Ereignisse zu deaktivieren, aber WebSockets- und Long Polling-Verbindungen zuzulassen:</span><span class="sxs-lookup"><span data-stu-id="c4d05-464">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="c4d05-465">Im JavaScript-Client werden Transporte konfiguriert, `transport` indem das Feld `withUrl`für das Optionsobjekt festgelegt wird, das für:</span><span class="sxs-lookup"><span data-stu-id="c4d05-465">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="c4d05-466">In dieser Version des Java-Client-Websockets ist der einzige verfügbare Transport.</span><span class="sxs-lookup"><span data-stu-id="c4d05-466">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="c4d05-467">Im Java-Client wird der Transport `withTransport` mit `HttpHubConnectionBuilder`der Methode auf der ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="c4d05-467">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="c4d05-468">Der Java-Client verwendet standardmäßig den WebSockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="c4d05-468">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="c4d05-469">Der SignalR Java-Client unterstützt den Transport-Fallback noch nicht.</span><span class="sxs-lookup"><span data-stu-id="c4d05-469">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="c4d05-470">Konfigurieren der Bearer-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="c4d05-470">Configure bearer authentication</span></span>

<span data-ttu-id="c4d05-471">Um Authentifizierungsdaten zusammen mit SignalR-Anforderungen bereitzustellen, verwenden Sie die `AccessTokenProvider` Option (in`accessTokenFactory` JavaScript), um eine Funktion anzugeben, die das gewünschte Zugriffstoken zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="c4d05-471">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="c4d05-472">Im .NET Client wird dieses Zugriffstoken als HTTP-Token "Bearer `Authorization` Authentication" übergeben `Bearer`(Verwenden des Headers mit einem Typ von ).</span><span class="sxs-lookup"><span data-stu-id="c4d05-472">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="c4d05-473">Im JavaScript-Client wird das Zugriffstoken als Bearer-Token verwendet, **außer** in einigen Fällen, in denen Browser-APIs die Möglichkeit einschränken, Header anzuwenden (insbesondere in Server-Sent Events und WebSockets-Anforderungen).</span><span class="sxs-lookup"><span data-stu-id="c4d05-473">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="c4d05-474">In diesen Fällen wird das Zugriffstoken als `access_token`Abfragezeichenfolgenwert bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="c4d05-474">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="c4d05-475">Im .NET-Client `AccessTokenProvider` kann die Option mithilfe der `WithUrl`Optionen delegate in angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="c4d05-475">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="c4d05-476">Im JavaScript-Client wird das Zugriffstoken konfiguriert, indem das `accessTokenFactory` `withUrl`Feld für das Optionsobjekt in :</span><span class="sxs-lookup"><span data-stu-id="c4d05-476">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="c4d05-477">Im SignalR Java-Client können Sie ein Trägertoken für die Authentifizierung konfigurieren, indem Sie dem [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)eine Zugriffstokenfactory bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-477">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="c4d05-478">Verwenden Sie [withAccessTokenFactory,](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) um eine [RxJava](https://github.com/ReactiveX/RxJava) [\<Single String>](https://reactivex.io/documentation/single.html)bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-478">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="c4d05-479">Mit einem Aufruf von [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)können Sie Logik schreiben, um Zugriffstoken für Ihren Client zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-479">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="c4d05-480">Konfigurieren von Timeout- und Keep-Alive-Optionen</span><span class="sxs-lookup"><span data-stu-id="c4d05-480">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="c4d05-481">Zusätzliche Optionen zum Konfigurieren von Timeout und `HubConnection` Keep-Alive-Verhalten sind für das Objekt selbst verfügbar:</span><span class="sxs-lookup"><span data-stu-id="c4d05-481">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="c4d05-482">.NET</span><span class="sxs-lookup"><span data-stu-id="c4d05-482">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c4d05-483">Option</span><span class="sxs-lookup"><span data-stu-id="c4d05-483">Option</span></span> | <span data-ttu-id="c4d05-484">Standardwert</span><span class="sxs-lookup"><span data-stu-id="c4d05-484">Default value</span></span> | <span data-ttu-id="c4d05-485">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="c4d05-485">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="c4d05-486">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="c4d05-486">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c4d05-487">Timeout für Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="c4d05-487">Timeout for server activity.</span></span> <span data-ttu-id="c4d05-488">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der `Closed` Client`onclose` die Verbindung des Servers und löst das Ereignis aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c4d05-488">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c4d05-489">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** vom Client innerhalb des Timeoutintervalls empfangen werden kann.</span><span class="sxs-lookup"><span data-stu-id="c4d05-489">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c4d05-490">Der empfohlene Wert ist eine Zahl, `KeepAliveInterval` die mindestens doppelt so hoch ist wie der Wert des Servers, damit Zeit für Pings eintrifft.</span><span class="sxs-lookup"><span data-stu-id="c4d05-490">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="c4d05-491">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="c4d05-491">15 seconds</span></span> | <span data-ttu-id="c4d05-492">Timeout für den ersten Serverhandshake.</span><span class="sxs-lookup"><span data-stu-id="c4d05-492">Timeout for initial server handshake.</span></span> <span data-ttu-id="c4d05-493">Wenn der Server in diesem Intervall keine Handshake-Antwort sendet, bricht der `Closed` Client`onclose` den Handshake ab und löst das Ereignis aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c4d05-493">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c4d05-494">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn Handshake-Timeoutfehler aufgrund einer schwerwiegenden Netzwerklatenz auftreten.</span><span class="sxs-lookup"><span data-stu-id="c4d05-494">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c4d05-495">Weitere Informationen zum Handshake-Prozess finden Sie in der [SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c4d05-495">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c4d05-496">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="c4d05-496">15 seconds</span></span> | <span data-ttu-id="c4d05-497">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="c4d05-497">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c4d05-498">Wenn Sie eine Nachricht vom Client senden, wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="c4d05-498">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c4d05-499">Wenn der Client keine Nachricht in `ClientTimeoutInterval` der Gruppe auf dem Server gesendet hat, betrachtet der Server die Verbindung zum Client.</span><span class="sxs-lookup"><span data-stu-id="c4d05-499">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="c4d05-500">Im .NET-Client werden Timeoutwerte `TimeSpan` als Werte angegeben.</span><span class="sxs-lookup"><span data-stu-id="c4d05-500">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="c4d05-501">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c4d05-501">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c4d05-502">Option</span><span class="sxs-lookup"><span data-stu-id="c4d05-502">Option</span></span> | <span data-ttu-id="c4d05-503">Standardwert</span><span class="sxs-lookup"><span data-stu-id="c4d05-503">Default value</span></span> | <span data-ttu-id="c4d05-504">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="c4d05-504">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="c4d05-505">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="c4d05-505">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c4d05-506">Timeout für Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="c4d05-506">Timeout for server activity.</span></span> <span data-ttu-id="c4d05-507">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der `onclose` Client die Verbindung zum Server und löst das Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="c4d05-507">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="c4d05-508">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** vom Client innerhalb des Timeoutintervalls empfangen werden kann.</span><span class="sxs-lookup"><span data-stu-id="c4d05-508">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c4d05-509">Der empfohlene Wert ist eine Zahl, `KeepAliveInterval` die mindestens doppelt so hoch ist wie der Wert des Servers, damit Zeit für Pings eintrifft.</span><span class="sxs-lookup"><span data-stu-id="c4d05-509">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="c4d05-510">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="c4d05-510">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="c4d05-511">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="c4d05-511">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c4d05-512">Wenn Sie eine Nachricht vom Client senden, wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="c4d05-512">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c4d05-513">Wenn der Client keine Nachricht in `ClientTimeoutInterval` der Gruppe auf dem Server gesendet hat, betrachtet der Server die Verbindung zum Client.</span><span class="sxs-lookup"><span data-stu-id="c4d05-513">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c4d05-514">Java</span><span class="sxs-lookup"><span data-stu-id="c4d05-514">Java</span></span>](#tab/java)

| <span data-ttu-id="c4d05-515">Option</span><span class="sxs-lookup"><span data-stu-id="c4d05-515">Option</span></span> | <span data-ttu-id="c4d05-516">Standardwert</span><span class="sxs-lookup"><span data-stu-id="c4d05-516">Default value</span></span> | <span data-ttu-id="c4d05-517">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="c4d05-517">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="c4d05-518">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="c4d05-518">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="c4d05-519">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="c4d05-519">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c4d05-520">Timeout für Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="c4d05-520">Timeout for server activity.</span></span> <span data-ttu-id="c4d05-521">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der `onClose` Client die Verbindung zum Server und löst das Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="c4d05-521">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="c4d05-522">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** vom Client innerhalb des Timeoutintervalls empfangen werden kann.</span><span class="sxs-lookup"><span data-stu-id="c4d05-522">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c4d05-523">Der empfohlene Wert ist eine Zahl, `KeepAliveInterval` die mindestens doppelt so hoch ist wie der Wert des Servers, damit Zeit für Pings eintrifft.</span><span class="sxs-lookup"><span data-stu-id="c4d05-523">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="c4d05-524">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="c4d05-524">15 seconds</span></span> | <span data-ttu-id="c4d05-525">Timeout für den ersten Serverhandshake.</span><span class="sxs-lookup"><span data-stu-id="c4d05-525">Timeout for initial server handshake.</span></span> <span data-ttu-id="c4d05-526">Wenn der Server in diesem Intervall keine Handshake-Antwort sendet, bricht der `onClose` Client den Handshake ab und löst das Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="c4d05-526">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="c4d05-527">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn Handshake-Timeoutfehler aufgrund einer schwerwiegenden Netzwerklatenz auftreten.</span><span class="sxs-lookup"><span data-stu-id="c4d05-527">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c4d05-528">Weitere Informationen zum Handshake-Prozess finden Sie in der [SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c4d05-528">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="c4d05-529">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="c4d05-529">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="c4d05-530">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="c4d05-530">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="c4d05-531">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="c4d05-531">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c4d05-532">Wenn Sie eine Nachricht vom Client senden, wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="c4d05-532">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c4d05-533">Wenn der Client keine Nachricht in `ClientTimeoutInterval` der Gruppe auf dem Server gesendet hat, betrachtet der Server die Verbindung zum Client.</span><span class="sxs-lookup"><span data-stu-id="c4d05-533">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="c4d05-534">Konfigurieren zusätzlicher Optionen</span><span class="sxs-lookup"><span data-stu-id="c4d05-534">Configure additional options</span></span>

<span data-ttu-id="c4d05-535">Zusätzliche Optionen können in `WithUrl` der`withUrl` ( in JavaScript) Methode auf `HubConnectionBuilder` oder `HttpHubConnectionBuilder` auf den verschiedenen Konfigurations-APIs auf dem im Java-Client konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="c4d05-535">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="c4d05-536">.NET</span><span class="sxs-lookup"><span data-stu-id="c4d05-536">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c4d05-537">.NET-Option</span><span class="sxs-lookup"><span data-stu-id="c4d05-537">.NET Option</span></span> |  <span data-ttu-id="c4d05-538">Standardwert</span><span class="sxs-lookup"><span data-stu-id="c4d05-538">Default value</span></span> | <span data-ttu-id="c4d05-539">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="c4d05-539">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="c4d05-540">Eine Funktion, die eine Zeichenfolge zurückgibt, die als Bearer-Authentifizierungstoken in HTTP-Anforderungen bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="c4d05-540">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="c4d05-541">Legen Sie `true` diesen Vorgang fest, um den Aushandlungsschritt zu überspringen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-541">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c4d05-542">**Wird nur unterstützt, wenn der WebSockets-Transport der einzige aktivierte Transport ist.**</span><span class="sxs-lookup"><span data-stu-id="c4d05-542">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c4d05-543">Diese Einstellung kann nicht aktiviert werden, wenn Sie den Azure SignalR-Dienst verwenden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-543">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="c4d05-544">Leer</span><span class="sxs-lookup"><span data-stu-id="c4d05-544">Empty</span></span> | <span data-ttu-id="c4d05-545">Eine Auflistung von TLS-Zertifikaten, die an Authentifizierenvon Anforderungen gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-545">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="c4d05-546">Leer</span><span class="sxs-lookup"><span data-stu-id="c4d05-546">Empty</span></span> | <span data-ttu-id="c4d05-547">Eine Sammlung von HTTP-Cookies, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-547">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="c4d05-548">Leer</span><span class="sxs-lookup"><span data-stu-id="c4d05-548">Empty</span></span> | <span data-ttu-id="c4d05-549">Anmeldeinformationen, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-549">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="c4d05-550">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="c4d05-550">5 seconds</span></span> | <span data-ttu-id="c4d05-551">Nur WebSockets.</span><span class="sxs-lookup"><span data-stu-id="c4d05-551">WebSockets only.</span></span> <span data-ttu-id="c4d05-552">Die maximale Zeitdauer, die der Client nach dem Schließen wartet, damit der Server die Close-Anforderung bestätigt.</span><span class="sxs-lookup"><span data-stu-id="c4d05-552">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="c4d05-553">Wenn der Server den Abschluss innerhalb dieser Zeit nicht bestätigt, trennt der Client die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="c4d05-553">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="c4d05-554">Leer</span><span class="sxs-lookup"><span data-stu-id="c4d05-554">Empty</span></span> | <span data-ttu-id="c4d05-555">Eine Zuordnung zusätzlicher HTTP-Header, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-555">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="c4d05-556">Ein Delegat, der zum `HttpMessageHandler` Konfigurieren oder Ersetzen der zum Senden von HTTP-Anforderungen verwendeten Delegaten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="c4d05-556">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="c4d05-557">Wird nicht für WebSocket-Verbindungen verwendet.</span><span class="sxs-lookup"><span data-stu-id="c4d05-557">Not used for WebSocket connections.</span></span> <span data-ttu-id="c4d05-558">Dieser Delegat muss einen Wert ungleich NULL zurückgeben, und er empfängt den Standardwert als Parameter.</span><span class="sxs-lookup"><span data-stu-id="c4d05-558">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="c4d05-559">Ändern Sie entweder die Einstellungen für diesen Standardwert, und geben Sie ihn zurück, oder geben Sie eine neue `HttpMessageHandler` Instanz zurück.</span><span class="sxs-lookup"><span data-stu-id="c4d05-559">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="c4d05-560">**Wenn Sie den Handler ersetzen, sollten Sie die Einstellungen, die Sie vom bereitgestellten Handler beibehalten möchten, kopieren, andernfalls gelten die konfigurierten Optionen (z. B. Cookies und Header) nicht für den neuen Handler.**</span><span class="sxs-lookup"><span data-stu-id="c4d05-560">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="c4d05-561">Ein HTTP-Proxy, der beim Senden von HTTP-Anforderungen verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="c4d05-561">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="c4d05-562">Legen Sie diese boolesche Einstellung fest, um die Standardanmeldeinformationen für HTTP- und WebSockets-Anforderungen zu senden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-562">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="c4d05-563">Dies ermöglicht die Verwendung der Windows-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="c4d05-563">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="c4d05-564">Ein Delegat, der zum Konfigurieren zusätzlicher WebSocket-Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="c4d05-564">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="c4d05-565">Empfängt eine Instanz von [ClientWebSocketOptions,](/dotnet/api/system.net.websockets.clientwebsocketoptions) die zum Konfigurieren der Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="c4d05-565">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="c4d05-566">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c4d05-566">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c4d05-567">JavaScript-Option</span><span class="sxs-lookup"><span data-stu-id="c4d05-567">JavaScript Option</span></span> | <span data-ttu-id="c4d05-568">Standardwert</span><span class="sxs-lookup"><span data-stu-id="c4d05-568">Default Value</span></span> | <span data-ttu-id="c4d05-569">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="c4d05-569">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="c4d05-570">Eine Funktion, die eine Zeichenfolge zurückgibt, die als Bearer-Authentifizierungstoken in HTTP-Anforderungen bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="c4d05-570">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="c4d05-571">Legen Sie `true` diesen Vorgang fest, um den Aushandlungsschritt zu überspringen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-571">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c4d05-572">**Wird nur unterstützt, wenn der WebSockets-Transport der einzige aktivierte Transport ist.**</span><span class="sxs-lookup"><span data-stu-id="c4d05-572">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c4d05-573">Diese Einstellung kann nicht aktiviert werden, wenn Sie den Azure SignalR-Dienst verwenden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-573">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c4d05-574">Java</span><span class="sxs-lookup"><span data-stu-id="c4d05-574">Java</span></span>](#tab/java)

| <span data-ttu-id="c4d05-575">Java-Option</span><span class="sxs-lookup"><span data-stu-id="c4d05-575">Java Option</span></span> | <span data-ttu-id="c4d05-576">Standardwert</span><span class="sxs-lookup"><span data-stu-id="c4d05-576">Default Value</span></span> | <span data-ttu-id="c4d05-577">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="c4d05-577">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="c4d05-578">Eine Funktion, die eine Zeichenfolge zurückgibt, die als Bearer-Authentifizierungstoken in HTTP-Anforderungen bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="c4d05-578">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="c4d05-579">Legen Sie `true` diesen Vorgang fest, um den Aushandlungsschritt zu überspringen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-579">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c4d05-580">**Wird nur unterstützt, wenn der WebSockets-Transport der einzige aktivierte Transport ist.**</span><span class="sxs-lookup"><span data-stu-id="c4d05-580">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c4d05-581">Diese Einstellung kann nicht aktiviert werden, wenn Sie den Azure SignalR-Dienst verwenden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-581">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="c4d05-582">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="c4d05-582">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="c4d05-583">Leer</span><span class="sxs-lookup"><span data-stu-id="c4d05-583">Empty</span></span> | <span data-ttu-id="c4d05-584">Eine Zuordnung zusätzlicher HTTP-Header, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-584">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="c4d05-585">Im .NET Client können diese Optionen durch den `WithUrl`Optionen-Delegaten geändert werden, der für:</span><span class="sxs-lookup"><span data-stu-id="c4d05-585">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="c4d05-586">Im JavaScript-Client können diese Optionen in einem JavaScript-Objekt bereitgestellt werden, `withUrl`das für:</span><span class="sxs-lookup"><span data-stu-id="c4d05-586">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="c4d05-587">Im Java-Client können diese Optionen mit den `HttpHubConnectionBuilder` Methoden auf dem zurückgegebenen`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="c4d05-587">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="c4d05-588">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="c4d05-588">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="c4d05-589">JSON/MessagePack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="c4d05-589">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="c4d05-590">ASP.NET Core SignalR unterstützt zwei Protokolle für die Codierung von Nachrichten: [JSON](https://www.json.org/) und [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="c4d05-590">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="c4d05-591">Jedes Protokoll verfügt über Serialisierungskonfigurationsoptionen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-591">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="c4d05-592">Die JSON-Serialisierung kann auf dem Server mithilfe der [AddJsonProtocol-Erweiterungsmethode](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) konfiguriert `Startup.ConfigureServices` werden, die nach [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in Ihrer Methode hinzugefügt werden kann.</span><span class="sxs-lookup"><span data-stu-id="c4d05-592">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="c4d05-593">Die `AddJsonProtocol` Methode nimmt einen `options` Delegaten an, der ein Objekt empfängt.</span><span class="sxs-lookup"><span data-stu-id="c4d05-593">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="c4d05-594">Die [PayloadSerializerSettings-Eigenschaft](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) für dieses `JsonSerializerSettings` Objekt ist ein JSON.NET-Objekt, das zum Konfigurieren der Serialisierung von Argumenten und Rückgabewerten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="c4d05-594">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="c4d05-595">Weitere Informationen finden Sie in der [JSON.NET-Dokumentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="c4d05-595">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="c4d05-596">Um den Serialisierungsmodul beispielsweise für die Verwendung von "PascalCase"-Eigenschaftsnamen anstelle der standardmäßigen "camelCase"-Namen zu konfigurieren, verwenden Sie den folgenden Code in: `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="c4d05-596">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="c4d05-597">Im .NET-Client ist `AddJsonProtocol` dieselbe Erweiterungsmethode auf [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)vorhanden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-597">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="c4d05-598">Der `Microsoft.Extensions.DependencyInjection` Namespace muss importiert werden, um die Erweiterungsmethode aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="c4d05-598">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="c4d05-599">Es ist derzeit nicht möglich, die JSON-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="c4d05-599">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="c4d05-600">MessagePack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="c4d05-600">MessagePack serialization options</span></span>

<span data-ttu-id="c4d05-601">Die MessagePack-Serialisierung kann konfiguriert werden, indem ein Delegat für den [AddMessagePackProtocol-Aufruf](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="c4d05-601">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="c4d05-602">Weitere Informationen finden Sie [unter MessagePack in SignalR.](xref:signalr/messagepackhubprotocol)</span><span class="sxs-lookup"><span data-stu-id="c4d05-602">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="c4d05-603">Es ist derzeit nicht möglich, die MessagePack-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="c4d05-603">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="c4d05-604">Konfigurieren von Serveroptionen</span><span class="sxs-lookup"><span data-stu-id="c4d05-604">Configure server options</span></span>

<span data-ttu-id="c4d05-605">In der folgenden Tabelle werden Optionen zum Konfigurieren von SignalR-Hubs beschrieben:</span><span class="sxs-lookup"><span data-stu-id="c4d05-605">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="c4d05-606">Option</span><span class="sxs-lookup"><span data-stu-id="c4d05-606">Option</span></span> | <span data-ttu-id="c4d05-607">Standardwert</span><span class="sxs-lookup"><span data-stu-id="c4d05-607">Default Value</span></span> | <span data-ttu-id="c4d05-608">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="c4d05-608">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="c4d05-609">30 Sekunden</span><span class="sxs-lookup"><span data-stu-id="c4d05-609">30 seconds</span></span> | <span data-ttu-id="c4d05-610">Der Server betrachtet die Verbindung des Clients, wenn er in diesem Intervall keine Nachricht (einschließlich Keep-Alive) erhalten hat.</span><span class="sxs-lookup"><span data-stu-id="c4d05-610">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="c4d05-611">Es kann länger dauern als dieses Timeoutintervall, bis der Client aufgrund der Implementierung tatsächlich als getrennt markiert wird.</span><span class="sxs-lookup"><span data-stu-id="c4d05-611">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="c4d05-612">Der empfohlene Wert `KeepAliveInterval` ist das Doppelte des Wertes.</span><span class="sxs-lookup"><span data-stu-id="c4d05-612">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="c4d05-613">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="c4d05-613">15 seconds</span></span> | <span data-ttu-id="c4d05-614">Wenn der Client innerhalb dieses Zeitintervalls keine erste Handshake-Nachricht sendet, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-614">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="c4d05-615">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn Handshake-Timeoutfehler aufgrund einer schwerwiegenden Netzwerklatenz auftreten.</span><span class="sxs-lookup"><span data-stu-id="c4d05-615">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c4d05-616">Weitere Informationen zum Handshake-Prozess finden Sie in der [SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c4d05-616">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c4d05-617">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="c4d05-617">15 seconds</span></span> | <span data-ttu-id="c4d05-618">Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten.</span><span class="sxs-lookup"><span data-stu-id="c4d05-618">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="c4d05-619">Ändern `KeepAliveInterval`Sie beim `ServerTimeout` / `serverTimeoutInMilliseconds` Ändern die Einstellung auf dem Client.</span><span class="sxs-lookup"><span data-stu-id="c4d05-619">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="c4d05-620">Der `ServerTimeout` / `serverTimeoutInMilliseconds` empfohlene Wert `KeepAliveInterval` ist das Doppelte des Wertes.</span><span class="sxs-lookup"><span data-stu-id="c4d05-620">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="c4d05-621">Alle installierten Protokolle</span><span class="sxs-lookup"><span data-stu-id="c4d05-621">All installed protocols</span></span> | <span data-ttu-id="c4d05-622">Protokolle, die von diesem Hub unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-622">Protocols supported by this hub.</span></span> <span data-ttu-id="c4d05-623">Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, Protokolle können jedoch aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="c4d05-623">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="c4d05-624">Wenn `true`, werden detaillierte Ausnahmemeldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="c4d05-624">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="c4d05-625">Der Standardwert ist `false`, da diese Ausnahmemeldungen vertrauliche Informationen enthalten können.</span><span class="sxs-lookup"><span data-stu-id="c4d05-625">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="c4d05-626">Optionen können für alle Hubs konfiguriert werden, `AddSignalR` indem `Startup.ConfigureServices`ein Optionsdelegat für den Aufruf in zur Verfügung gestellt wird.</span><span class="sxs-lookup"><span data-stu-id="c4d05-626">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="c4d05-627">Optionen für einen einzelnen Hub überschreiben `AddSignalR` die globalen Optionen, die in bereitgestellt werden und können mit <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>folgenden Optionen konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="c4d05-627">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="c4d05-628">Erweiterte HTTP-Konfigurationsoptionen</span><span class="sxs-lookup"><span data-stu-id="c4d05-628">Advanced HTTP configuration options</span></span>

<span data-ttu-id="c4d05-629">Verwenden `HttpConnectionDispatcherOptions` Sie diese Datei zum Konfigurieren erweiterter Einstellungen für Transporte und Speicherpufferverwaltung.</span><span class="sxs-lookup"><span data-stu-id="c4d05-629">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="c4d05-630">Diese Optionen werden konfiguriert, indem sie einen `Startup.Configure`Delegaten an [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in übergeben.</span><span class="sxs-lookup"><span data-stu-id="c4d05-630">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="c4d05-631">In der folgenden Tabelle werden Optionen zum Konfigurieren ASP.NET erweiterten HTTP-Optionen von Core SignalR beschrieben:</span><span class="sxs-lookup"><span data-stu-id="c4d05-631">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="c4d05-632">Option</span><span class="sxs-lookup"><span data-stu-id="c4d05-632">Option</span></span> | <span data-ttu-id="c4d05-633">Standardwert</span><span class="sxs-lookup"><span data-stu-id="c4d05-633">Default Value</span></span> | <span data-ttu-id="c4d05-634">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="c4d05-634">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="c4d05-635">32 KB</span><span class="sxs-lookup"><span data-stu-id="c4d05-635">32 KB</span></span> | <span data-ttu-id="c4d05-636">Die maximale Anzahl von Bytes, die vom Client empfangen werden, den der Server puffert.</span><span class="sxs-lookup"><span data-stu-id="c4d05-636">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="c4d05-637">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten empfangen, kann sich jedoch negativ auf den Speicherverbrauch auswirken.</span><span class="sxs-lookup"><span data-stu-id="c4d05-637">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="c4d05-638">Daten, die `Authorize` automatisch aus den Attributen gesammelt werden, die auf die Hub-Klasse angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-638">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="c4d05-639">Eine Liste von [IAuthorizeData-Objekten,](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) die verwendet werden, um zu bestimmen, ob ein Client zum Herstellen einer Verbindung mit dem Hub autorisiert ist.</span><span class="sxs-lookup"><span data-stu-id="c4d05-639">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="c4d05-640">32 KB</span><span class="sxs-lookup"><span data-stu-id="c4d05-640">32 KB</span></span> | <span data-ttu-id="c4d05-641">Die maximale Anzahl von Bytes, die von der App gesendet werden, die der Server puffert.</span><span class="sxs-lookup"><span data-stu-id="c4d05-641">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="c4d05-642">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten senden, kann sich jedoch negativ auf den Speicherverbrauch auswirken.</span><span class="sxs-lookup"><span data-stu-id="c4d05-642">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="c4d05-643">Alle Transporte sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="c4d05-643">All Transports are enabled.</span></span> | <span data-ttu-id="c4d05-644">Eine Bit-Flags-Enumere von `HttpTransportType` Werten, die die Transporte einschränken können, die ein Client zum Herstellen einer Verbindung verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="c4d05-644">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="c4d05-645">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="c4d05-645">See below.</span></span> | <span data-ttu-id="c4d05-646">Zusätzliche Optionen speziell für den Long Polling-Transport.</span><span class="sxs-lookup"><span data-stu-id="c4d05-646">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="c4d05-647">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="c4d05-647">See below.</span></span> | <span data-ttu-id="c4d05-648">Zusätzliche Optionen, die für den WebSockets-Transport spezifisch sind.</span><span class="sxs-lookup"><span data-stu-id="c4d05-648">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="c4d05-649">Der Long Polling-Transport verfügt über zusätzliche `LongPolling` Optionen, die mithilfe der Eigenschaft konfiguriert werden können:</span><span class="sxs-lookup"><span data-stu-id="c4d05-649">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="c4d05-650">Option</span><span class="sxs-lookup"><span data-stu-id="c4d05-650">Option</span></span> | <span data-ttu-id="c4d05-651">Standardwert</span><span class="sxs-lookup"><span data-stu-id="c4d05-651">Default Value</span></span> | <span data-ttu-id="c4d05-652">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="c4d05-652">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="c4d05-653">90 Sekunden</span><span class="sxs-lookup"><span data-stu-id="c4d05-653">90 seconds</span></span> | <span data-ttu-id="c4d05-654">Die maximale Zeit, die der Server auf das Senden einer Nachricht an den Client wartet, bevor eine einzelne Abfrageanforderung beendet wird.</span><span class="sxs-lookup"><span data-stu-id="c4d05-654">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="c4d05-655">Wenn Sie diesen Wert verringern, gibt der Client häufiger neue Abfrageanforderungen aus.</span><span class="sxs-lookup"><span data-stu-id="c4d05-655">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="c4d05-656">Der WebSocket-Transport verfügt über zusätzliche Optionen, die mithilfe der `WebSockets` Eigenschaft konfiguriert werden können:</span><span class="sxs-lookup"><span data-stu-id="c4d05-656">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="c4d05-657">Option</span><span class="sxs-lookup"><span data-stu-id="c4d05-657">Option</span></span> | <span data-ttu-id="c4d05-658">Standardwert</span><span class="sxs-lookup"><span data-stu-id="c4d05-658">Default Value</span></span> | <span data-ttu-id="c4d05-659">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="c4d05-659">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="c4d05-660">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="c4d05-660">5 seconds</span></span> | <span data-ttu-id="c4d05-661">Wenn der Client nicht innerhalb dieses Zeitintervalls geschlossen werden kann, wird die Verbindung beendet, nachdem der Server geschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="c4d05-661">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="c4d05-662">Ein Delegat, der `Sec-WebSocket-Protocol` verwendet werden kann, um den Header auf einen benutzerdefinierten Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-662">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="c4d05-663">Der Delegat empfängt die vom Client angeforderten Werte als Eingabe und wird voraussichtlich den gewünschten Wert zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="c4d05-663">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="c4d05-664">Konfigurieren von Clientoptionen</span><span class="sxs-lookup"><span data-stu-id="c4d05-664">Configure client options</span></span>

<span data-ttu-id="c4d05-665">Clientoptionen können für den `HubConnectionBuilder` Typ konfiguriert werden (verfügbar in den .NET- und JavaScript-Clients).</span><span class="sxs-lookup"><span data-stu-id="c4d05-665">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="c4d05-666">Es ist auch im Java-Client `HttpHubConnectionBuilder` verfügbar, aber die Unterklasse ist, was `HubConnection` die Builder-Konfigurationsoptionen enthält, sowie auf der selbst.</span><span class="sxs-lookup"><span data-stu-id="c4d05-666">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="c4d05-667">Konfigurieren der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="c4d05-667">Configure logging</span></span>

<span data-ttu-id="c4d05-668">Die Protokollierung wird im .NET-Client mit der `ConfigureLogging` Methode konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="c4d05-668">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="c4d05-669">Protokollierungsanbieter und Filter können auf die gleiche Weise registriert werden wie auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="c4d05-669">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="c4d05-670">Weitere Informationen finden Sie in der Dokumentation ["Anmelden in ASP.NET Kern".](xref:fundamentals/logging/index)</span><span class="sxs-lookup"><span data-stu-id="c4d05-670">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="c4d05-671">Um Logging-Anbieter zu registrieren, müssen Sie die erforderlichen Pakete installieren.</span><span class="sxs-lookup"><span data-stu-id="c4d05-671">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="c4d05-672">Eine vollständige Liste finden Sie im Abschnitt [Integrierte Protokollierungsanbieter](xref:fundamentals/logging/index#built-in-logging-providers) in den Dokumenten.</span><span class="sxs-lookup"><span data-stu-id="c4d05-672">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="c4d05-673">Um beispielsweise die Konsolenprotokollierung `Microsoft.Extensions.Logging.Console` zu aktivieren, installieren Sie das NuGet-Paket.</span><span class="sxs-lookup"><span data-stu-id="c4d05-673">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="c4d05-674">Rufen `AddConsole` Sie die Erweiterungsmethode auf:</span><span class="sxs-lookup"><span data-stu-id="c4d05-674">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="c4d05-675">Im JavaScript-Client ist `configureLogging` eine ähnliche Methode vorhanden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-675">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="c4d05-676">Geben `LogLevel` Sie einen Wert an, der die Mindestebene der zu erzeugenden Protokollnachrichten angibt.</span><span class="sxs-lookup"><span data-stu-id="c4d05-676">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="c4d05-677">Protokolle werden in das Browserkonsolenfenster geschrieben.</span><span class="sxs-lookup"><span data-stu-id="c4d05-677">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="c4d05-678">Um die Protokollierung `signalR.LogLevel.None` vollständig `configureLogging` zu deaktivieren, geben Sie in der Methode an.</span><span class="sxs-lookup"><span data-stu-id="c4d05-678">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="c4d05-679">Weitere Informationen zur Protokollierung finden Sie in der [SignalR-Diagnosedokumentation](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="c4d05-679">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="c4d05-680">Der SignalR Java-Client verwendet die [SLF4J-Bibliothek](https://www.slf4j.org/) für die Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="c4d05-680">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="c4d05-681">Es handelt sich um eine api auf hoher Ebene, die es Benutzern der Bibliothek ermöglicht, ihre eigene spezifische Protokollierungsimplementierung zu wählen, indem sie eine bestimmte Protokollierungsabhängigkeit einführt.</span><span class="sxs-lookup"><span data-stu-id="c4d05-681">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="c4d05-682">Der folgende Codeausschnitt zeigt, wie `java.util.logging` Sie mit dem SignalR Java-Client verwenden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-682">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="c4d05-683">Wenn Sie die Protokollierung in Ihren Abhängigkeiten nicht konfigurieren, lädt SLF4J einen standardmäßigen Nicht-Betriebsprotokollmitlogger mit der folgenden Warnmeldung:</span><span class="sxs-lookup"><span data-stu-id="c4d05-683">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="c4d05-684">Dies kann getrost ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-684">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="c4d05-685">Konfigurieren zulässiger Transporte</span><span class="sxs-lookup"><span data-stu-id="c4d05-685">Configure allowed transports</span></span>

<span data-ttu-id="c4d05-686">Die von SignalR verwendeten Transporte können `WithUrl` im`withUrl` Aufruf (in JavaScript) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-686">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="c4d05-687">Ein bitweises-ODER der `HttpTransportType` Werte von kann verwendet werden, um den Client darauf zu beschränken, nur die angegebenen Transporte zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-687">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="c4d05-688">Alle Transporte sind standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="c4d05-688">All transports are enabled by default.</span></span>

<span data-ttu-id="c4d05-689">Um z. B. den Transport servergesendeter Ereignisse zu deaktivieren, aber WebSockets- und Long Polling-Verbindungen zuzulassen:</span><span class="sxs-lookup"><span data-stu-id="c4d05-689">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="c4d05-690">Im JavaScript-Client werden Transporte konfiguriert, `transport` indem das Feld `withUrl`für das Optionsobjekt festgelegt wird, das für:</span><span class="sxs-lookup"><span data-stu-id="c4d05-690">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="c4d05-691">In dieser Version des Java-Client-Websockets ist der einzige verfügbare Transport.</span><span class="sxs-lookup"><span data-stu-id="c4d05-691">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="c4d05-692">Konfigurieren der Bearer-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="c4d05-692">Configure bearer authentication</span></span>

<span data-ttu-id="c4d05-693">Um Authentifizierungsdaten zusammen mit SignalR-Anforderungen bereitzustellen, verwenden Sie die `AccessTokenProvider` Option (in`accessTokenFactory` JavaScript), um eine Funktion anzugeben, die das gewünschte Zugriffstoken zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="c4d05-693">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="c4d05-694">Im .NET Client wird dieses Zugriffstoken als HTTP-Token "Bearer `Authorization` Authentication" übergeben `Bearer`(Verwenden des Headers mit einem Typ von ).</span><span class="sxs-lookup"><span data-stu-id="c4d05-694">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="c4d05-695">Im JavaScript-Client wird das Zugriffstoken als Bearer-Token verwendet, **außer** in einigen Fällen, in denen Browser-APIs die Möglichkeit einschränken, Header anzuwenden (insbesondere in Server-Sent Events und WebSockets-Anforderungen).</span><span class="sxs-lookup"><span data-stu-id="c4d05-695">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="c4d05-696">In diesen Fällen wird das Zugriffstoken als `access_token`Abfragezeichenfolgenwert bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="c4d05-696">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="c4d05-697">Im .NET-Client `AccessTokenProvider` kann die Option mithilfe der `WithUrl`Optionen delegate in angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="c4d05-697">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="c4d05-698">Im JavaScript-Client wird das Zugriffstoken konfiguriert, indem das `accessTokenFactory` `withUrl`Feld für das Optionsobjekt in :</span><span class="sxs-lookup"><span data-stu-id="c4d05-698">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="c4d05-699">Im SignalR Java-Client können Sie ein Trägertoken für die Authentifizierung konfigurieren, indem Sie dem [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)eine Zugriffstokenfactory bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-699">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="c4d05-700">Verwenden Sie [withAccessTokenFactory,](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) um eine [RxJava](https://github.com/ReactiveX/RxJava) [\<Single String>](https://reactivex.io/documentation/single.html)bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-700">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="c4d05-701">Mit einem Aufruf von [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)können Sie Logik schreiben, um Zugriffstoken für Ihren Client zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-701">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="c4d05-702">Konfigurieren von Timeout- und Keep-Alive-Optionen</span><span class="sxs-lookup"><span data-stu-id="c4d05-702">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="c4d05-703">Zusätzliche Optionen zum Konfigurieren von Timeout und `HubConnection` Keep-Alive-Verhalten sind für das Objekt selbst verfügbar:</span><span class="sxs-lookup"><span data-stu-id="c4d05-703">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="c4d05-704">.NET</span><span class="sxs-lookup"><span data-stu-id="c4d05-704">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c4d05-705">Option</span><span class="sxs-lookup"><span data-stu-id="c4d05-705">Option</span></span> | <span data-ttu-id="c4d05-706">Standardwert</span><span class="sxs-lookup"><span data-stu-id="c4d05-706">Default value</span></span> | <span data-ttu-id="c4d05-707">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="c4d05-707">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="c4d05-708">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="c4d05-708">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c4d05-709">Timeout für Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="c4d05-709">Timeout for server activity.</span></span> <span data-ttu-id="c4d05-710">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der `Closed` Client`onclose` die Verbindung des Servers und löst das Ereignis aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c4d05-710">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c4d05-711">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** vom Client innerhalb des Timeoutintervalls empfangen werden kann.</span><span class="sxs-lookup"><span data-stu-id="c4d05-711">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c4d05-712">Der empfohlene Wert ist eine Zahl, `KeepAliveInterval` die mindestens doppelt so hoch ist wie der Wert des Servers, damit Zeit für Pings eintrifft.</span><span class="sxs-lookup"><span data-stu-id="c4d05-712">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="c4d05-713">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="c4d05-713">15 seconds</span></span> | <span data-ttu-id="c4d05-714">Timeout für den ersten Serverhandshake.</span><span class="sxs-lookup"><span data-stu-id="c4d05-714">Timeout for initial server handshake.</span></span> <span data-ttu-id="c4d05-715">Wenn der Server in diesem Intervall keine Handshake-Antwort sendet, bricht der `Closed` Client`onclose` den Handshake ab und löst das Ereignis aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c4d05-715">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c4d05-716">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn Handshake-Timeoutfehler aufgrund einer schwerwiegenden Netzwerklatenz auftreten.</span><span class="sxs-lookup"><span data-stu-id="c4d05-716">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c4d05-717">Weitere Informationen zum Handshake-Prozess finden Sie in der [SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c4d05-717">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c4d05-718">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="c4d05-718">15 seconds</span></span> | <span data-ttu-id="c4d05-719">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="c4d05-719">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c4d05-720">Wenn Sie eine Nachricht vom Client senden, wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="c4d05-720">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c4d05-721">Wenn der Client keine Nachricht in `ClientTimeoutInterval` der Gruppe auf dem Server gesendet hat, betrachtet der Server die Verbindung zum Client.</span><span class="sxs-lookup"><span data-stu-id="c4d05-721">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="c4d05-722">Im .NET-Client werden Timeoutwerte `TimeSpan` als Werte angegeben.</span><span class="sxs-lookup"><span data-stu-id="c4d05-722">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="c4d05-723">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c4d05-723">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c4d05-724">Option</span><span class="sxs-lookup"><span data-stu-id="c4d05-724">Option</span></span> | <span data-ttu-id="c4d05-725">Standardwert</span><span class="sxs-lookup"><span data-stu-id="c4d05-725">Default value</span></span> | <span data-ttu-id="c4d05-726">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="c4d05-726">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="c4d05-727">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="c4d05-727">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c4d05-728">Timeout für Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="c4d05-728">Timeout for server activity.</span></span> <span data-ttu-id="c4d05-729">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der `onclose` Client die Verbindung zum Server und löst das Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="c4d05-729">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="c4d05-730">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** vom Client innerhalb des Timeoutintervalls empfangen werden kann.</span><span class="sxs-lookup"><span data-stu-id="c4d05-730">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c4d05-731">Der empfohlene Wert ist eine Zahl, `KeepAliveInterval` die mindestens doppelt so hoch ist wie der Wert des Servers, damit Zeit für Pings eintrifft.</span><span class="sxs-lookup"><span data-stu-id="c4d05-731">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="c4d05-732">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="c4d05-732">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="c4d05-733">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="c4d05-733">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c4d05-734">Wenn Sie eine Nachricht vom Client senden, wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="c4d05-734">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c4d05-735">Wenn der Client keine Nachricht in `ClientTimeoutInterval` der Gruppe auf dem Server gesendet hat, betrachtet der Server die Verbindung zum Client.</span><span class="sxs-lookup"><span data-stu-id="c4d05-735">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c4d05-736">Java</span><span class="sxs-lookup"><span data-stu-id="c4d05-736">Java</span></span>](#tab/java)

| <span data-ttu-id="c4d05-737">Option</span><span class="sxs-lookup"><span data-stu-id="c4d05-737">Option</span></span> | <span data-ttu-id="c4d05-738">Standardwert</span><span class="sxs-lookup"><span data-stu-id="c4d05-738">Default value</span></span> | <span data-ttu-id="c4d05-739">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="c4d05-739">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="c4d05-740">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="c4d05-740">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="c4d05-741">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="c4d05-741">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c4d05-742">Timeout für Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="c4d05-742">Timeout for server activity.</span></span> <span data-ttu-id="c4d05-743">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der `onClose` Client die Verbindung zum Server und löst das Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="c4d05-743">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="c4d05-744">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** vom Client innerhalb des Timeoutintervalls empfangen werden kann.</span><span class="sxs-lookup"><span data-stu-id="c4d05-744">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c4d05-745">Der empfohlene Wert ist eine Zahl, `KeepAliveInterval` die mindestens doppelt so hoch ist wie der Wert des Servers, damit Zeit für Pings eintrifft.</span><span class="sxs-lookup"><span data-stu-id="c4d05-745">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="c4d05-746">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="c4d05-746">15 seconds</span></span> | <span data-ttu-id="c4d05-747">Timeout für den ersten Serverhandshake.</span><span class="sxs-lookup"><span data-stu-id="c4d05-747">Timeout for initial server handshake.</span></span> <span data-ttu-id="c4d05-748">Wenn der Server in diesem Intervall keine Handshake-Antwort sendet, bricht der `onClose` Client den Handshake ab und löst das Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="c4d05-748">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="c4d05-749">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn Handshake-Timeoutfehler aufgrund einer schwerwiegenden Netzwerklatenz auftreten.</span><span class="sxs-lookup"><span data-stu-id="c4d05-749">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c4d05-750">Weitere Informationen zum Handshake-Prozess finden Sie in der [SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c4d05-750">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="c4d05-751">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="c4d05-751">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="c4d05-752">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="c4d05-752">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="c4d05-753">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="c4d05-753">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c4d05-754">Wenn Sie eine Nachricht vom Client senden, wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="c4d05-754">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c4d05-755">Wenn der Client keine Nachricht in `ClientTimeoutInterval` der Gruppe auf dem Server gesendet hat, betrachtet der Server die Verbindung zum Client.</span><span class="sxs-lookup"><span data-stu-id="c4d05-755">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="c4d05-756">Konfigurieren zusätzlicher Optionen</span><span class="sxs-lookup"><span data-stu-id="c4d05-756">Configure additional options</span></span>

<span data-ttu-id="c4d05-757">Zusätzliche Optionen können in `WithUrl` der`withUrl` ( in JavaScript) Methode auf `HubConnectionBuilder` oder `HttpHubConnectionBuilder` auf den verschiedenen Konfigurations-APIs auf dem im Java-Client konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="c4d05-757">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="c4d05-758">.NET</span><span class="sxs-lookup"><span data-stu-id="c4d05-758">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c4d05-759">.NET-Option</span><span class="sxs-lookup"><span data-stu-id="c4d05-759">.NET Option</span></span> |  <span data-ttu-id="c4d05-760">Standardwert</span><span class="sxs-lookup"><span data-stu-id="c4d05-760">Default value</span></span> | <span data-ttu-id="c4d05-761">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="c4d05-761">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="c4d05-762">Eine Funktion, die eine Zeichenfolge zurückgibt, die als Bearer-Authentifizierungstoken in HTTP-Anforderungen bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="c4d05-762">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="c4d05-763">Legen Sie `true` diesen Vorgang fest, um den Aushandlungsschritt zu überspringen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-763">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c4d05-764">**Wird nur unterstützt, wenn der WebSockets-Transport der einzige aktivierte Transport ist.**</span><span class="sxs-lookup"><span data-stu-id="c4d05-764">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c4d05-765">Diese Einstellung kann nicht aktiviert werden, wenn Sie den Azure SignalR-Dienst verwenden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-765">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="c4d05-766">Leer</span><span class="sxs-lookup"><span data-stu-id="c4d05-766">Empty</span></span> | <span data-ttu-id="c4d05-767">Eine Auflistung von TLS-Zertifikaten, die an Authentifizierenvon Anforderungen gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-767">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="c4d05-768">Leer</span><span class="sxs-lookup"><span data-stu-id="c4d05-768">Empty</span></span> | <span data-ttu-id="c4d05-769">Eine Sammlung von HTTP-Cookies, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-769">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="c4d05-770">Leer</span><span class="sxs-lookup"><span data-stu-id="c4d05-770">Empty</span></span> | <span data-ttu-id="c4d05-771">Anmeldeinformationen, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-771">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="c4d05-772">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="c4d05-772">5 seconds</span></span> | <span data-ttu-id="c4d05-773">Nur WebSockets.</span><span class="sxs-lookup"><span data-stu-id="c4d05-773">WebSockets only.</span></span> <span data-ttu-id="c4d05-774">Die maximale Zeitdauer, die der Client nach dem Schließen wartet, damit der Server die Close-Anforderung bestätigt.</span><span class="sxs-lookup"><span data-stu-id="c4d05-774">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="c4d05-775">Wenn der Server den Abschluss innerhalb dieser Zeit nicht bestätigt, trennt der Client die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="c4d05-775">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="c4d05-776">Leer</span><span class="sxs-lookup"><span data-stu-id="c4d05-776">Empty</span></span> | <span data-ttu-id="c4d05-777">Eine Zuordnung zusätzlicher HTTP-Header, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-777">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="c4d05-778">Ein Delegat, der zum `HttpMessageHandler` Konfigurieren oder Ersetzen der zum Senden von HTTP-Anforderungen verwendeten Delegaten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="c4d05-778">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="c4d05-779">Wird nicht für WebSocket-Verbindungen verwendet.</span><span class="sxs-lookup"><span data-stu-id="c4d05-779">Not used for WebSocket connections.</span></span> <span data-ttu-id="c4d05-780">Dieser Delegat muss einen Wert ungleich NULL zurückgeben, und er empfängt den Standardwert als Parameter.</span><span class="sxs-lookup"><span data-stu-id="c4d05-780">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="c4d05-781">Ändern Sie entweder die Einstellungen für diesen Standardwert, und geben Sie ihn zurück, oder geben Sie eine neue `HttpMessageHandler` Instanz zurück.</span><span class="sxs-lookup"><span data-stu-id="c4d05-781">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="c4d05-782">**Wenn Sie den Handler ersetzen, sollten Sie die Einstellungen, die Sie vom bereitgestellten Handler beibehalten möchten, kopieren, andernfalls gelten die konfigurierten Optionen (z. B. Cookies und Header) nicht für den neuen Handler.**</span><span class="sxs-lookup"><span data-stu-id="c4d05-782">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="c4d05-783">Ein HTTP-Proxy, der beim Senden von HTTP-Anforderungen verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="c4d05-783">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="c4d05-784">Legen Sie diese boolesche Einstellung fest, um die Standardanmeldeinformationen für HTTP- und WebSockets-Anforderungen zu senden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-784">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="c4d05-785">Dies ermöglicht die Verwendung der Windows-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="c4d05-785">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="c4d05-786">Ein Delegat, der zum Konfigurieren zusätzlicher WebSocket-Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="c4d05-786">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="c4d05-787">Empfängt eine Instanz von [ClientWebSocketOptions,](/dotnet/api/system.net.websockets.clientwebsocketoptions) die zum Konfigurieren der Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="c4d05-787">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="c4d05-788">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c4d05-788">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c4d05-789">JavaScript-Option</span><span class="sxs-lookup"><span data-stu-id="c4d05-789">JavaScript Option</span></span> | <span data-ttu-id="c4d05-790">Standardwert</span><span class="sxs-lookup"><span data-stu-id="c4d05-790">Default Value</span></span> | <span data-ttu-id="c4d05-791">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="c4d05-791">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="c4d05-792">Eine Funktion, die eine Zeichenfolge zurückgibt, die als Bearer-Authentifizierungstoken in HTTP-Anforderungen bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="c4d05-792">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="c4d05-793">Legen Sie `true` diesen Vorgang fest, um den Aushandlungsschritt zu überspringen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-793">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c4d05-794">**Wird nur unterstützt, wenn der WebSockets-Transport der einzige aktivierte Transport ist.**</span><span class="sxs-lookup"><span data-stu-id="c4d05-794">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c4d05-795">Diese Einstellung kann nicht aktiviert werden, wenn Sie den Azure SignalR-Dienst verwenden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-795">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c4d05-796">Java</span><span class="sxs-lookup"><span data-stu-id="c4d05-796">Java</span></span>](#tab/java)

| <span data-ttu-id="c4d05-797">Java-Option</span><span class="sxs-lookup"><span data-stu-id="c4d05-797">Java Option</span></span> | <span data-ttu-id="c4d05-798">Standardwert</span><span class="sxs-lookup"><span data-stu-id="c4d05-798">Default Value</span></span> | <span data-ttu-id="c4d05-799">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="c4d05-799">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="c4d05-800">Eine Funktion, die eine Zeichenfolge zurückgibt, die als Bearer-Authentifizierungstoken in HTTP-Anforderungen bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="c4d05-800">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="c4d05-801">Legen Sie `true` diesen Vorgang fest, um den Aushandlungsschritt zu überspringen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-801">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c4d05-802">**Wird nur unterstützt, wenn der WebSockets-Transport der einzige aktivierte Transport ist.**</span><span class="sxs-lookup"><span data-stu-id="c4d05-802">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c4d05-803">Diese Einstellung kann nicht aktiviert werden, wenn Sie den Azure SignalR-Dienst verwenden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-803">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="c4d05-804">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="c4d05-804">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="c4d05-805">Leer</span><span class="sxs-lookup"><span data-stu-id="c4d05-805">Empty</span></span> | <span data-ttu-id="c4d05-806">Eine Zuordnung zusätzlicher HTTP-Header, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-806">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="c4d05-807">Im .NET Client können diese Optionen durch den `WithUrl`Optionen-Delegaten geändert werden, der für:</span><span class="sxs-lookup"><span data-stu-id="c4d05-807">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="c4d05-808">Im JavaScript-Client können diese Optionen in einem JavaScript-Objekt bereitgestellt werden, `withUrl`das für:</span><span class="sxs-lookup"><span data-stu-id="c4d05-808">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="c4d05-809">Im Java-Client können diese Optionen mit den `HttpHubConnectionBuilder` Methoden auf dem zurückgegebenen`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="c4d05-809">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="c4d05-810">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="c4d05-810">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="c4d05-811">JSON/MessagePack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="c4d05-811">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="c4d05-812">ASP.NET Core SignalR unterstützt zwei Protokolle für die Codierung von Nachrichten: [JSON](https://www.json.org/) und [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="c4d05-812">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="c4d05-813">Jedes Protokoll verfügt über Serialisierungskonfigurationsoptionen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-813">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="c4d05-814">Die JSON-Serialisierung kann auf dem Server mithilfe der [AddJsonProtocol-Erweiterungsmethode](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) konfiguriert `Startup.ConfigureServices` werden, die nach [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in Ihrer Methode hinzugefügt werden kann.</span><span class="sxs-lookup"><span data-stu-id="c4d05-814">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="c4d05-815">Die `AddJsonProtocol` Methode nimmt einen `options` Delegaten an, der ein Objekt empfängt.</span><span class="sxs-lookup"><span data-stu-id="c4d05-815">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="c4d05-816">Die [PayloadSerializerSettings-Eigenschaft](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) für dieses `JsonSerializerSettings` Objekt ist ein JSON.NET-Objekt, das zum Konfigurieren der Serialisierung von Argumenten und Rückgabewerten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="c4d05-816">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="c4d05-817">Weitere Informationen finden Sie in der [JSON.NET-Dokumentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="c4d05-817">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="c4d05-818">Um den Serialisierungsmodul beispielsweise für die Verwendung von "PascalCase"-Eigenschaftsnamen anstelle der standardmäßigen "camelCase"-Namen zu konfigurieren, verwenden Sie den folgenden Code in: `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="c4d05-818">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="c4d05-819">Im .NET-Client ist `AddJsonProtocol` dieselbe Erweiterungsmethode auf [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)vorhanden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-819">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="c4d05-820">Der `Microsoft.Extensions.DependencyInjection` Namespace muss importiert werden, um die Erweiterungsmethode aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="c4d05-820">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="c4d05-821">Es ist derzeit nicht möglich, die JSON-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="c4d05-821">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="c4d05-822">MessagePack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="c4d05-822">MessagePack serialization options</span></span>

<span data-ttu-id="c4d05-823">Die MessagePack-Serialisierung kann konfiguriert werden, indem ein Delegat für den [AddMessagePackProtocol-Aufruf](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="c4d05-823">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="c4d05-824">Weitere Informationen finden Sie [unter MessagePack in SignalR.](xref:signalr/messagepackhubprotocol)</span><span class="sxs-lookup"><span data-stu-id="c4d05-824">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="c4d05-825">Es ist derzeit nicht möglich, die MessagePack-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="c4d05-825">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="c4d05-826">Konfigurieren von Serveroptionen</span><span class="sxs-lookup"><span data-stu-id="c4d05-826">Configure server options</span></span>

<span data-ttu-id="c4d05-827">In der folgenden Tabelle werden Optionen zum Konfigurieren von SignalR-Hubs beschrieben:</span><span class="sxs-lookup"><span data-stu-id="c4d05-827">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="c4d05-828">Option</span><span class="sxs-lookup"><span data-stu-id="c4d05-828">Option</span></span> | <span data-ttu-id="c4d05-829">Standardwert</span><span class="sxs-lookup"><span data-stu-id="c4d05-829">Default Value</span></span> | <span data-ttu-id="c4d05-830">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="c4d05-830">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="c4d05-831">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="c4d05-831">15 seconds</span></span> | <span data-ttu-id="c4d05-832">Wenn der Client innerhalb dieses Zeitintervalls keine erste Handshake-Nachricht sendet, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-832">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="c4d05-833">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn Handshake-Timeoutfehler aufgrund einer schwerwiegenden Netzwerklatenz auftreten.</span><span class="sxs-lookup"><span data-stu-id="c4d05-833">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c4d05-834">Weitere Informationen zum Handshake-Prozess finden Sie in der [SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c4d05-834">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c4d05-835">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="c4d05-835">15 seconds</span></span> | <span data-ttu-id="c4d05-836">Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten.</span><span class="sxs-lookup"><span data-stu-id="c4d05-836">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="c4d05-837">Ändern `KeepAliveInterval`Sie beim `ServerTimeout` / `serverTimeoutInMilliseconds` Ändern die Einstellung auf dem Client.</span><span class="sxs-lookup"><span data-stu-id="c4d05-837">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="c4d05-838">Der `ServerTimeout` / `serverTimeoutInMilliseconds` empfohlene Wert `KeepAliveInterval` ist das Doppelte des Wertes.</span><span class="sxs-lookup"><span data-stu-id="c4d05-838">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="c4d05-839">Alle installierten Protokolle</span><span class="sxs-lookup"><span data-stu-id="c4d05-839">All installed protocols</span></span> | <span data-ttu-id="c4d05-840">Protokolle, die von diesem Hub unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-840">Protocols supported by this hub.</span></span> <span data-ttu-id="c4d05-841">Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, Protokolle können jedoch aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="c4d05-841">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="c4d05-842">Wenn `true`, werden detaillierte Ausnahmemeldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="c4d05-842">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="c4d05-843">Der Standardwert ist `false`, da diese Ausnahmemeldungen vertrauliche Informationen enthalten können.</span><span class="sxs-lookup"><span data-stu-id="c4d05-843">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="c4d05-844">Optionen können für alle Hubs konfiguriert werden, `AddSignalR` indem `Startup.ConfigureServices`ein Optionsdelegat für den Aufruf in zur Verfügung gestellt wird.</span><span class="sxs-lookup"><span data-stu-id="c4d05-844">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="c4d05-845">Optionen für einen einzelnen Hub überschreiben `AddSignalR` die globalen Optionen, die in bereitgestellt werden und können mit <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>folgenden Optionen konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="c4d05-845">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="c4d05-846">Erweiterte HTTP-Konfigurationsoptionen</span><span class="sxs-lookup"><span data-stu-id="c4d05-846">Advanced HTTP configuration options</span></span>

<span data-ttu-id="c4d05-847">Verwenden `HttpConnectionDispatcherOptions` Sie diese Datei zum Konfigurieren erweiterter Einstellungen für Transporte und Speicherpufferverwaltung.</span><span class="sxs-lookup"><span data-stu-id="c4d05-847">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="c4d05-848">Diese Optionen werden konfiguriert, indem sie einen `Startup.Configure`Delegaten an [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in übergeben.</span><span class="sxs-lookup"><span data-stu-id="c4d05-848">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="c4d05-849">In der folgenden Tabelle werden Optionen zum Konfigurieren ASP.NET erweiterten HTTP-Optionen von Core SignalR beschrieben:</span><span class="sxs-lookup"><span data-stu-id="c4d05-849">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="c4d05-850">Option</span><span class="sxs-lookup"><span data-stu-id="c4d05-850">Option</span></span> | <span data-ttu-id="c4d05-851">Standardwert</span><span class="sxs-lookup"><span data-stu-id="c4d05-851">Default Value</span></span> | <span data-ttu-id="c4d05-852">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="c4d05-852">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="c4d05-853">32 KB</span><span class="sxs-lookup"><span data-stu-id="c4d05-853">32 KB</span></span> | <span data-ttu-id="c4d05-854">Die maximale Anzahl von Bytes, die vom Client empfangen werden, den der Server puffert.</span><span class="sxs-lookup"><span data-stu-id="c4d05-854">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="c4d05-855">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten empfangen, kann sich jedoch negativ auf den Speicherverbrauch auswirken.</span><span class="sxs-lookup"><span data-stu-id="c4d05-855">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="c4d05-856">Daten, die `Authorize` automatisch aus den Attributen gesammelt werden, die auf die Hub-Klasse angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-856">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="c4d05-857">Eine Liste von [IAuthorizeData-Objekten,](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) die verwendet werden, um zu bestimmen, ob ein Client zum Herstellen einer Verbindung mit dem Hub autorisiert ist.</span><span class="sxs-lookup"><span data-stu-id="c4d05-857">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="c4d05-858">32 KB</span><span class="sxs-lookup"><span data-stu-id="c4d05-858">32 KB</span></span> | <span data-ttu-id="c4d05-859">Die maximale Anzahl von Bytes, die von der App gesendet werden, die der Server puffert.</span><span class="sxs-lookup"><span data-stu-id="c4d05-859">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="c4d05-860">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten senden, kann sich jedoch negativ auf den Speicherverbrauch auswirken.</span><span class="sxs-lookup"><span data-stu-id="c4d05-860">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="c4d05-861">Alle Transporte sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="c4d05-861">All Transports are enabled.</span></span> | <span data-ttu-id="c4d05-862">Eine Bit-Flags-Enumere von `HttpTransportType` Werten, die die Transporte einschränken können, die ein Client zum Herstellen einer Verbindung verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="c4d05-862">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="c4d05-863">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="c4d05-863">See below.</span></span> | <span data-ttu-id="c4d05-864">Zusätzliche Optionen speziell für den Long Polling-Transport.</span><span class="sxs-lookup"><span data-stu-id="c4d05-864">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="c4d05-865">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="c4d05-865">See below.</span></span> | <span data-ttu-id="c4d05-866">Zusätzliche Optionen, die für den WebSockets-Transport spezifisch sind.</span><span class="sxs-lookup"><span data-stu-id="c4d05-866">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="c4d05-867">Der Long Polling-Transport verfügt über zusätzliche `LongPolling` Optionen, die mithilfe der Eigenschaft konfiguriert werden können:</span><span class="sxs-lookup"><span data-stu-id="c4d05-867">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="c4d05-868">Option</span><span class="sxs-lookup"><span data-stu-id="c4d05-868">Option</span></span> | <span data-ttu-id="c4d05-869">Standardwert</span><span class="sxs-lookup"><span data-stu-id="c4d05-869">Default Value</span></span> | <span data-ttu-id="c4d05-870">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="c4d05-870">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="c4d05-871">90 Sekunden</span><span class="sxs-lookup"><span data-stu-id="c4d05-871">90 seconds</span></span> | <span data-ttu-id="c4d05-872">Die maximale Zeit, die der Server auf das Senden einer Nachricht an den Client wartet, bevor eine einzelne Abfrageanforderung beendet wird.</span><span class="sxs-lookup"><span data-stu-id="c4d05-872">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="c4d05-873">Wenn Sie diesen Wert verringern, gibt der Client häufiger neue Abfrageanforderungen aus.</span><span class="sxs-lookup"><span data-stu-id="c4d05-873">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="c4d05-874">Der WebSocket-Transport verfügt über zusätzliche Optionen, die mithilfe der `WebSockets` Eigenschaft konfiguriert werden können:</span><span class="sxs-lookup"><span data-stu-id="c4d05-874">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="c4d05-875">Option</span><span class="sxs-lookup"><span data-stu-id="c4d05-875">Option</span></span> | <span data-ttu-id="c4d05-876">Standardwert</span><span class="sxs-lookup"><span data-stu-id="c4d05-876">Default Value</span></span> | <span data-ttu-id="c4d05-877">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="c4d05-877">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="c4d05-878">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="c4d05-878">5 seconds</span></span> | <span data-ttu-id="c4d05-879">Wenn der Client nicht innerhalb dieses Zeitintervalls geschlossen werden kann, wird die Verbindung beendet, nachdem der Server geschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="c4d05-879">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="c4d05-880">Ein Delegat, der `Sec-WebSocket-Protocol` verwendet werden kann, um den Header auf einen benutzerdefinierten Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-880">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="c4d05-881">Der Delegat empfängt die vom Client angeforderten Werte als Eingabe und wird voraussichtlich den gewünschten Wert zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="c4d05-881">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="c4d05-882">Konfigurieren von Clientoptionen</span><span class="sxs-lookup"><span data-stu-id="c4d05-882">Configure client options</span></span>

<span data-ttu-id="c4d05-883">Clientoptionen können für den `HubConnectionBuilder` Typ konfiguriert werden (verfügbar in den .NET- und JavaScript-Clients).</span><span class="sxs-lookup"><span data-stu-id="c4d05-883">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="c4d05-884">Es ist auch im Java-Client `HttpHubConnectionBuilder` verfügbar, aber die Unterklasse ist, was `HubConnection` die Builder-Konfigurationsoptionen enthält, sowie auf der selbst.</span><span class="sxs-lookup"><span data-stu-id="c4d05-884">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="c4d05-885">Konfigurieren der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="c4d05-885">Configure logging</span></span>

<span data-ttu-id="c4d05-886">Die Protokollierung wird im .NET-Client mit der `ConfigureLogging` Methode konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="c4d05-886">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="c4d05-887">Protokollierungsanbieter und Filter können auf die gleiche Weise registriert werden wie auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="c4d05-887">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="c4d05-888">Weitere Informationen finden Sie in der Dokumentation ["Anmelden in ASP.NET Kern".](xref:fundamentals/logging/index)</span><span class="sxs-lookup"><span data-stu-id="c4d05-888">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="c4d05-889">Um Logging-Anbieter zu registrieren, müssen Sie die erforderlichen Pakete installieren.</span><span class="sxs-lookup"><span data-stu-id="c4d05-889">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="c4d05-890">Eine vollständige Liste finden Sie im Abschnitt [Integrierte Protokollierungsanbieter](xref:fundamentals/logging/index#built-in-logging-providers) in den Dokumenten.</span><span class="sxs-lookup"><span data-stu-id="c4d05-890">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="c4d05-891">Um beispielsweise die Konsolenprotokollierung `Microsoft.Extensions.Logging.Console` zu aktivieren, installieren Sie das NuGet-Paket.</span><span class="sxs-lookup"><span data-stu-id="c4d05-891">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="c4d05-892">Rufen `AddConsole` Sie die Erweiterungsmethode auf:</span><span class="sxs-lookup"><span data-stu-id="c4d05-892">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="c4d05-893">Im JavaScript-Client ist `configureLogging` eine ähnliche Methode vorhanden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-893">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="c4d05-894">Geben `LogLevel` Sie einen Wert an, der die Mindestebene der zu erzeugenden Protokollnachrichten angibt.</span><span class="sxs-lookup"><span data-stu-id="c4d05-894">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="c4d05-895">Protokolle werden in das Browserkonsolenfenster geschrieben.</span><span class="sxs-lookup"><span data-stu-id="c4d05-895">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="c4d05-896">Um die Protokollierung `signalR.LogLevel.None` vollständig `configureLogging` zu deaktivieren, geben Sie in der Methode an.</span><span class="sxs-lookup"><span data-stu-id="c4d05-896">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="c4d05-897">Weitere Informationen zur Protokollierung finden Sie in der [SignalR-Diagnosedokumentation](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="c4d05-897">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="c4d05-898">Der SignalR Java-Client verwendet die [SLF4J-Bibliothek](https://www.slf4j.org/) für die Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="c4d05-898">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="c4d05-899">Es handelt sich um eine api auf hoher Ebene, die es Benutzern der Bibliothek ermöglicht, ihre eigene spezifische Protokollierungsimplementierung zu wählen, indem sie eine bestimmte Protokollierungsabhängigkeit einführt.</span><span class="sxs-lookup"><span data-stu-id="c4d05-899">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="c4d05-900">Der folgende Codeausschnitt zeigt, wie `java.util.logging` Sie mit dem SignalR Java-Client verwenden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-900">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="c4d05-901">Wenn Sie die Protokollierung in Ihren Abhängigkeiten nicht konfigurieren, lädt SLF4J einen standardmäßigen Nicht-Betriebsprotokollmitlogger mit der folgenden Warnmeldung:</span><span class="sxs-lookup"><span data-stu-id="c4d05-901">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="c4d05-902">Dies kann getrost ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-902">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="c4d05-903">Konfigurieren zulässiger Transporte</span><span class="sxs-lookup"><span data-stu-id="c4d05-903">Configure allowed transports</span></span>

<span data-ttu-id="c4d05-904">Die von SignalR verwendeten Transporte können `WithUrl` im`withUrl` Aufruf (in JavaScript) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-904">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="c4d05-905">Ein bitweises-ODER der `HttpTransportType` Werte von kann verwendet werden, um den Client darauf zu beschränken, nur die angegebenen Transporte zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-905">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="c4d05-906">Alle Transporte sind standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="c4d05-906">All transports are enabled by default.</span></span>

<span data-ttu-id="c4d05-907">Um z. B. den Transport servergesendeter Ereignisse zu deaktivieren, aber WebSockets- und Long Polling-Verbindungen zuzulassen:</span><span class="sxs-lookup"><span data-stu-id="c4d05-907">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="c4d05-908">Im JavaScript-Client werden Transporte konfiguriert, `transport` indem das Feld `withUrl`für das Optionsobjekt festgelegt wird, das für:</span><span class="sxs-lookup"><span data-stu-id="c4d05-908">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="c4d05-909">Konfigurieren der Bearer-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="c4d05-909">Configure bearer authentication</span></span>

<span data-ttu-id="c4d05-910">Um Authentifizierungsdaten zusammen mit SignalR-Anforderungen bereitzustellen, verwenden Sie die `AccessTokenProvider` Option (in`accessTokenFactory` JavaScript), um eine Funktion anzugeben, die das gewünschte Zugriffstoken zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="c4d05-910">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="c4d05-911">Im .NET Client wird dieses Zugriffstoken als HTTP-Token "Bearer `Authorization` Authentication" übergeben `Bearer`(Verwenden des Headers mit einem Typ von ).</span><span class="sxs-lookup"><span data-stu-id="c4d05-911">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="c4d05-912">Im JavaScript-Client wird das Zugriffstoken als Bearer-Token verwendet, **außer** in einigen Fällen, in denen Browser-APIs die Möglichkeit einschränken, Header anzuwenden (insbesondere in Server-Sent Events und WebSockets-Anforderungen).</span><span class="sxs-lookup"><span data-stu-id="c4d05-912">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="c4d05-913">In diesen Fällen wird das Zugriffstoken als `access_token`Abfragezeichenfolgenwert bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="c4d05-913">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="c4d05-914">Im .NET-Client `AccessTokenProvider` kann die Option mithilfe der `WithUrl`Optionen delegate in angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="c4d05-914">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="c4d05-915">Im JavaScript-Client wird das Zugriffstoken konfiguriert, indem das `accessTokenFactory` `withUrl`Feld für das Optionsobjekt in :</span><span class="sxs-lookup"><span data-stu-id="c4d05-915">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="c4d05-916">Im SignalR Java-Client können Sie ein Trägertoken für die Authentifizierung konfigurieren, indem Sie dem [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)eine Zugriffstokenfactory bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-916">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="c4d05-917">Verwenden Sie [withAccessTokenFactory,](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) um eine [RxJava](https://github.com/ReactiveX/RxJava) [\<Single String>](https://reactivex.io/documentation/single.html)bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-917">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="c4d05-918">Mit einem Aufruf von [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)können Sie Logik schreiben, um Zugriffstoken für Ihren Client zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-918">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="c4d05-919">Konfigurieren von Timeout- und Keep-Alive-Optionen</span><span class="sxs-lookup"><span data-stu-id="c4d05-919">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="c4d05-920">Zusätzliche Optionen zum Konfigurieren von Timeout und `HubConnection` Keep-Alive-Verhalten sind für das Objekt selbst verfügbar:</span><span class="sxs-lookup"><span data-stu-id="c4d05-920">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="c4d05-921">.NET</span><span class="sxs-lookup"><span data-stu-id="c4d05-921">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c4d05-922">Option</span><span class="sxs-lookup"><span data-stu-id="c4d05-922">Option</span></span> | <span data-ttu-id="c4d05-923">Standardwert</span><span class="sxs-lookup"><span data-stu-id="c4d05-923">Default value</span></span> | <span data-ttu-id="c4d05-924">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="c4d05-924">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="c4d05-925">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="c4d05-925">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c4d05-926">Timeout für Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="c4d05-926">Timeout for server activity.</span></span> <span data-ttu-id="c4d05-927">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der `Closed` Client`onclose` die Verbindung des Servers und löst das Ereignis aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c4d05-927">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c4d05-928">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** vom Client innerhalb des Timeoutintervalls empfangen werden kann.</span><span class="sxs-lookup"><span data-stu-id="c4d05-928">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c4d05-929">Der empfohlene Wert ist eine Zahl, `KeepAliveInterval` die mindestens doppelt so hoch ist wie der Wert des Servers, damit Zeit für Pings eintrifft.</span><span class="sxs-lookup"><span data-stu-id="c4d05-929">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="c4d05-930">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="c4d05-930">15 seconds</span></span> | <span data-ttu-id="c4d05-931">Timeout für den ersten Serverhandshake.</span><span class="sxs-lookup"><span data-stu-id="c4d05-931">Timeout for initial server handshake.</span></span> <span data-ttu-id="c4d05-932">Wenn der Server in diesem Intervall keine Handshake-Antwort sendet, bricht der `Closed` Client`onclose` den Handshake ab und löst das Ereignis aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c4d05-932">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c4d05-933">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn Handshake-Timeoutfehler aufgrund einer schwerwiegenden Netzwerklatenz auftreten.</span><span class="sxs-lookup"><span data-stu-id="c4d05-933">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c4d05-934">Weitere Informationen zum Handshake-Prozess finden Sie in der [SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c4d05-934">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="c4d05-935">Im .NET-Client werden Timeoutwerte `TimeSpan` als Werte angegeben.</span><span class="sxs-lookup"><span data-stu-id="c4d05-935">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="c4d05-936">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c4d05-936">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c4d05-937">Option</span><span class="sxs-lookup"><span data-stu-id="c4d05-937">Option</span></span> | <span data-ttu-id="c4d05-938">Standardwert</span><span class="sxs-lookup"><span data-stu-id="c4d05-938">Default value</span></span> | <span data-ttu-id="c4d05-939">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="c4d05-939">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="c4d05-940">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="c4d05-940">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c4d05-941">Timeout für Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="c4d05-941">Timeout for server activity.</span></span> <span data-ttu-id="c4d05-942">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der `onclose` Client die Verbindung zum Server und löst das Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="c4d05-942">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="c4d05-943">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** vom Client innerhalb des Timeoutintervalls empfangen werden kann.</span><span class="sxs-lookup"><span data-stu-id="c4d05-943">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c4d05-944">Der empfohlene Wert ist eine Zahl, `KeepAliveInterval` die mindestens doppelt so hoch ist wie der Wert des Servers, damit Zeit für Pings eintrifft.</span><span class="sxs-lookup"><span data-stu-id="c4d05-944">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c4d05-945">Java</span><span class="sxs-lookup"><span data-stu-id="c4d05-945">Java</span></span>](#tab/java)

| <span data-ttu-id="c4d05-946">Option</span><span class="sxs-lookup"><span data-stu-id="c4d05-946">Option</span></span> | <span data-ttu-id="c4d05-947">Standardwert</span><span class="sxs-lookup"><span data-stu-id="c4d05-947">Default value</span></span> | <span data-ttu-id="c4d05-948">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="c4d05-948">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="c4d05-949">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="c4d05-949">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="c4d05-950">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="c4d05-950">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c4d05-951">Timeout für Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="c4d05-951">Timeout for server activity.</span></span> <span data-ttu-id="c4d05-952">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der `onClose` Client die Verbindung zum Server und löst das Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="c4d05-952">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="c4d05-953">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** vom Client innerhalb des Timeoutintervalls empfangen werden kann.</span><span class="sxs-lookup"><span data-stu-id="c4d05-953">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c4d05-954">Der empfohlene Wert ist eine Zahl, `KeepAliveInterval` die mindestens das Doppelte des Serverwerts beträgt, um Zeit für Pings zu haben.</span><span class="sxs-lookup"><span data-stu-id="c4d05-954">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="c4d05-955">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="c4d05-955">15 seconds</span></span> | <span data-ttu-id="c4d05-956">Timeout für den ersten Serverhandshake.</span><span class="sxs-lookup"><span data-stu-id="c4d05-956">Timeout for initial server handshake.</span></span> <span data-ttu-id="c4d05-957">Wenn der Server in diesem Intervall keine Handshake-Antwort sendet, bricht der `onClose` Client den Handshake ab und löst das Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="c4d05-957">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="c4d05-958">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn Handshake-Timeoutfehler aufgrund einer schwerwiegenden Netzwerklatenz auftreten.</span><span class="sxs-lookup"><span data-stu-id="c4d05-958">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c4d05-959">Weitere Informationen zum Handshake-Prozess finden Sie in der [SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c4d05-959">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="c4d05-960">Konfigurieren zusätzlicher Optionen</span><span class="sxs-lookup"><span data-stu-id="c4d05-960">Configure additional options</span></span>

<span data-ttu-id="c4d05-961">Zusätzliche Optionen können in `WithUrl` der`withUrl` ( in JavaScript) Methode auf `HubConnectionBuilder` oder `HttpHubConnectionBuilder` auf den verschiedenen Konfigurations-APIs auf dem im Java-Client konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="c4d05-961">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="c4d05-962">.NET</span><span class="sxs-lookup"><span data-stu-id="c4d05-962">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c4d05-963">.NET-Option</span><span class="sxs-lookup"><span data-stu-id="c4d05-963">.NET Option</span></span> |  <span data-ttu-id="c4d05-964">Standardwert</span><span class="sxs-lookup"><span data-stu-id="c4d05-964">Default value</span></span> | <span data-ttu-id="c4d05-965">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="c4d05-965">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="c4d05-966">Eine Funktion, die eine Zeichenfolge zurückgibt, die als Bearer-Authentifizierungstoken in HTTP-Anforderungen bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="c4d05-966">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="c4d05-967">Legen Sie `true` diesen Vorgang fest, um den Aushandlungsschritt zu überspringen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-967">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c4d05-968">**Wird nur unterstützt, wenn der WebSockets-Transport der einzige aktivierte Transport ist.**</span><span class="sxs-lookup"><span data-stu-id="c4d05-968">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c4d05-969">Diese Einstellung kann nicht aktiviert werden, wenn Sie den Azure SignalR-Dienst verwenden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-969">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="c4d05-970">Leer</span><span class="sxs-lookup"><span data-stu-id="c4d05-970">Empty</span></span> | <span data-ttu-id="c4d05-971">Eine Auflistung von TLS-Zertifikaten, die an Authentifizierenvon Anforderungen gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-971">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="c4d05-972">Leer</span><span class="sxs-lookup"><span data-stu-id="c4d05-972">Empty</span></span> | <span data-ttu-id="c4d05-973">Eine Sammlung von HTTP-Cookies, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-973">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="c4d05-974">Leer</span><span class="sxs-lookup"><span data-stu-id="c4d05-974">Empty</span></span> | <span data-ttu-id="c4d05-975">Anmeldeinformationen, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-975">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="c4d05-976">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="c4d05-976">5 seconds</span></span> | <span data-ttu-id="c4d05-977">Nur WebSockets.</span><span class="sxs-lookup"><span data-stu-id="c4d05-977">WebSockets only.</span></span> <span data-ttu-id="c4d05-978">Die maximale Zeitdauer, die der Client nach dem Schließen wartet, damit der Server die Close-Anforderung bestätigt.</span><span class="sxs-lookup"><span data-stu-id="c4d05-978">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="c4d05-979">Wenn der Server den Abschluss innerhalb dieser Zeit nicht bestätigt, trennt der Client die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="c4d05-979">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="c4d05-980">Leer</span><span class="sxs-lookup"><span data-stu-id="c4d05-980">Empty</span></span> | <span data-ttu-id="c4d05-981">Eine Zuordnung zusätzlicher HTTP-Header, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-981">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="c4d05-982">Ein Delegat, der zum `HttpMessageHandler` Konfigurieren oder Ersetzen der zum Senden von HTTP-Anforderungen verwendeten Delegaten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="c4d05-982">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="c4d05-983">Wird nicht für WebSocket-Verbindungen verwendet.</span><span class="sxs-lookup"><span data-stu-id="c4d05-983">Not used for WebSocket connections.</span></span> <span data-ttu-id="c4d05-984">Dieser Delegat muss einen Wert ungleich NULL zurückgeben, und er empfängt den Standardwert als Parameter.</span><span class="sxs-lookup"><span data-stu-id="c4d05-984">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="c4d05-985">Ändern Sie entweder die Einstellungen für diesen Standardwert, und geben Sie ihn zurück, oder geben Sie eine neue `HttpMessageHandler` Instanz zurück.</span><span class="sxs-lookup"><span data-stu-id="c4d05-985">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="c4d05-986">**Wenn Sie den Handler ersetzen, sollten Sie die Einstellungen, die Sie vom bereitgestellten Handler beibehalten möchten, kopieren, andernfalls gelten die konfigurierten Optionen (z. B. Cookies und Header) nicht für den neuen Handler.**</span><span class="sxs-lookup"><span data-stu-id="c4d05-986">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="c4d05-987">Ein HTTP-Proxy, der beim Senden von HTTP-Anforderungen verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="c4d05-987">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="c4d05-988">Legen Sie diese boolesche Einstellung fest, um die Standardanmeldeinformationen für HTTP- und WebSockets-Anforderungen zu senden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-988">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="c4d05-989">Dies ermöglicht die Verwendung der Windows-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="c4d05-989">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="c4d05-990">Ein Delegat, der zum Konfigurieren zusätzlicher WebSocket-Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="c4d05-990">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="c4d05-991">Empfängt eine Instanz von [ClientWebSocketOptions,](/dotnet/api/system.net.websockets.clientwebsocketoptions) die zum Konfigurieren der Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="c4d05-991">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="c4d05-992">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c4d05-992">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c4d05-993">JavaScript-Option</span><span class="sxs-lookup"><span data-stu-id="c4d05-993">JavaScript Option</span></span> | <span data-ttu-id="c4d05-994">Standardwert</span><span class="sxs-lookup"><span data-stu-id="c4d05-994">Default Value</span></span> | <span data-ttu-id="c4d05-995">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="c4d05-995">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="c4d05-996">Eine Funktion, die eine Zeichenfolge zurückgibt, die als Bearer-Authentifizierungstoken in HTTP-Anforderungen bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="c4d05-996">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="c4d05-997">Legen Sie `true` diesen Vorgang fest, um den Aushandlungsschritt zu überspringen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-997">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c4d05-998">**Wird nur unterstützt, wenn der WebSockets-Transport der einzige aktivierte Transport ist.**</span><span class="sxs-lookup"><span data-stu-id="c4d05-998">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c4d05-999">Diese Einstellung kann nicht aktiviert werden, wenn Sie den Azure SignalR-Dienst verwenden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-999">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c4d05-1000">Java</span><span class="sxs-lookup"><span data-stu-id="c4d05-1000">Java</span></span>](#tab/java)

| <span data-ttu-id="c4d05-1001">Java-Option</span><span class="sxs-lookup"><span data-stu-id="c4d05-1001">Java Option</span></span> | <span data-ttu-id="c4d05-1002">Standardwert</span><span class="sxs-lookup"><span data-stu-id="c4d05-1002">Default Value</span></span> | <span data-ttu-id="c4d05-1003">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="c4d05-1003">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="c4d05-1004">Eine Funktion, die eine Zeichenfolge zurückgibt, die als Bearer-Authentifizierungstoken in HTTP-Anforderungen bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="c4d05-1004">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="c4d05-1005">Legen Sie `true` diesen Vorgang fest, um den Aushandlungsschritt zu überspringen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-1005">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c4d05-1006">**Wird nur unterstützt, wenn der WebSockets-Transport der einzige aktivierte Transport ist.**</span><span class="sxs-lookup"><span data-stu-id="c4d05-1006">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c4d05-1007">Diese Einstellung kann nicht aktiviert werden, wenn Sie den Azure SignalR-Dienst verwenden.</span><span class="sxs-lookup"><span data-stu-id="c4d05-1007">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="c4d05-1008">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="c4d05-1008">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="c4d05-1009">Leer</span><span class="sxs-lookup"><span data-stu-id="c4d05-1009">Empty</span></span> | <span data-ttu-id="c4d05-1010">Eine Zuordnung zusätzlicher HTTP-Header, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="c4d05-1010">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="c4d05-1011">Im .NET Client können diese Optionen durch den `WithUrl`Optionen-Delegaten geändert werden, der für:</span><span class="sxs-lookup"><span data-stu-id="c4d05-1011">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="c4d05-1012">Im JavaScript-Client können diese Optionen in einem JavaScript-Objekt bereitgestellt werden, `withUrl`das für:</span><span class="sxs-lookup"><span data-stu-id="c4d05-1012">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="c4d05-1013">Im Java-Client können diese Optionen mit den `HttpHubConnectionBuilder` Methoden auf dem zurückgegebenen`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="c4d05-1013">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="c4d05-1014">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="c4d05-1014">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end

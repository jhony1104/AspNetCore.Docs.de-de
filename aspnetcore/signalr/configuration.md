---
title: ASP.net Core SignalR Konfiguration
author: bradygaster
description: Erfahren Sie, wie Sie ASP.net Core SignalR-apps konfigurieren.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 682cc36216a4dc9b38c87b4f67100ab565a70e5c
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963981"
---
# <a name="aspnet-core-opno-locsignalr-configuration"></a><span data-ttu-id="08386-103">ASP.net Core SignalR Konfiguration</span><span class="sxs-lookup"><span data-stu-id="08386-103">ASP.NET Core SignalR configuration</span></span>

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="08386-104">JSON/messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="08386-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="08386-105">ASP.net Core SignalR unterstützt zwei Protokolle zum Codieren von Nachrichten: [JSON](https://www.json.org/) und [messagepack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="08386-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="08386-106">Jedes Protokoll verfügt über Konfigurationsoptionen für die Serialisierung.</span><span class="sxs-lookup"><span data-stu-id="08386-106">Each protocol has serialization configuration options.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="08386-107">Die JSON-Serialisierung kann mithilfe der [addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) -Erweiterungsmethode auf dem Server konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="08386-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="08386-108">`AddJsonProtocol` können nach [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="08386-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="08386-109">Die `AddJsonProtocol`-Methode nimmt einen Delegaten an, der ein `options` Objekt empfängt.</span><span class="sxs-lookup"><span data-stu-id="08386-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="08386-110">Die [payloadserializeroptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) -Eigenschaft für dieses Objekt ist ein `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> Objekt, das zum Konfigurieren der Serialisierung von Argumenten und Rückgabe Werten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="08386-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="08386-111">Weitere Informationen finden Sie in der [System. Text. JSON-Dokumentation](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="08386-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="08386-112">Wenn Sie beispielsweise das Serialisierungsprogramm so konfigurieren möchten, dass die Groß-/Kleinschreibung von Eigenschaftsnamen nicht geändert wird, verwenden Sie den folgenden Code in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="08386-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="08386-113">Im .NET-Client ist dieselbe `AddJsonProtocol` Erweiterungsmethode auf [hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)vorhanden.</span><span class="sxs-lookup"><span data-stu-id="08386-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="08386-114">Der `Microsoft.Extensions.DependencyInjection`-Namespace muss importiert werden, um die Erweiterungsmethode aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="08386-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="08386-115">Zurzeit ist es nicht möglich, JSON-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="08386-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="08386-116">Wechseln Sie zu "newtonsoft. JSON".</span><span class="sxs-lookup"><span data-stu-id="08386-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="08386-117">Wenn Sie Features von `Newtonsoft.Json` benötigen, die in `System.Text.Json`nicht unterstützt werden, finden Sie weitere Informationen unter [Wechseln zu Newton Soft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="08386-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="08386-118">Die JSON-Serialisierung kann mithilfe der [addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) -Erweiterungsmethode auf dem Server konfiguriert werden, die nach [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in ihrer `Startup.ConfigureServices`-Methode hinzugefügt werden kann.</span><span class="sxs-lookup"><span data-stu-id="08386-118">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="08386-119">Die `AddJsonProtocol`-Methode nimmt einen Delegaten an, der ein `options` Objekt empfängt.</span><span class="sxs-lookup"><span data-stu-id="08386-119">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="08386-120">Die [payloadserializersettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) -Eigenschaft für dieses Objekt ist ein JSON.net-`JsonSerializerSettings` Objekt, das zum Konfigurieren der Serialisierung von Argumenten und Rückgabe Werten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="08386-120">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="08386-121">Weitere Informationen finden Sie in der [JSON.NET-Dokumentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="08386-121">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="08386-122">Verwenden Sie z. b. den folgenden Code in `Startup.ConfigureServices`, um das Serialisierungsprogramm so zu konfigurieren, dass "PascalCase"-Eigenschaftsnamen anstelle der Standardnamen "CamelCase" verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="08386-122">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="08386-123">Im .NET-Client ist dieselbe `AddJsonProtocol` Erweiterungsmethode auf [hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)vorhanden.</span><span class="sxs-lookup"><span data-stu-id="08386-123">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="08386-124">Der `Microsoft.Extensions.DependencyInjection`-Namespace muss importiert werden, um die Erweiterungsmethode aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="08386-124">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="08386-125">Zurzeit ist es nicht möglich, JSON-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="08386-125">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

::: moniker-end

### <a name="messagepack-serialization-options"></a><span data-ttu-id="08386-126">Messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="08386-126">MessagePack serialization options</span></span>

<span data-ttu-id="08386-127">Die messagepack-Serialisierung kann konfiguriert werden, indem ein Delegat für den [addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) -Aufruf bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="08386-127">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="08386-128">Weitere Informationen finden Sie [unter messagepack in SignalR](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="08386-128">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="08386-129">Zurzeit ist es nicht möglich, die messagepack-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="08386-129">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="08386-130">Konfigurieren von Serveroptionen</span><span class="sxs-lookup"><span data-stu-id="08386-130">Configure server options</span></span>

<span data-ttu-id="08386-131">In der folgenden Tabelle werden die Optionen zum Konfigurieren von SignalR Hubs beschrieben:</span><span class="sxs-lookup"><span data-stu-id="08386-131">The following table describes options for configuring SignalR hubs:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="08386-132">Option</span><span class="sxs-lookup"><span data-stu-id="08386-132">Option</span></span> | <span data-ttu-id="08386-133">Standardwert</span><span class="sxs-lookup"><span data-stu-id="08386-133">Default Value</span></span> | <span data-ttu-id="08386-134">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="08386-134">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="08386-135">30 Sekunden</span><span class="sxs-lookup"><span data-stu-id="08386-135">30 seconds</span></span> | <span data-ttu-id="08386-136">Der Server wird vom-Client als getrennt betrachtet, wenn in diesem Intervall keine Nachricht (einschließlich Keep-Alive) empfangen wurde.</span><span class="sxs-lookup"><span data-stu-id="08386-136">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="08386-137">Es kann länger dauern, bis der Client als getrennt gekennzeichnet ist, weil dies implementiert ist.</span><span class="sxs-lookup"><span data-stu-id="08386-137">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="08386-138">Der empfohlene Wert ist Double der `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="08386-138">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="08386-139">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="08386-139">15 seconds</span></span> | <span data-ttu-id="08386-140">Wenn der Client innerhalb dieses Zeitraums keine anfängliche Hand Shake Nachricht sendet, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="08386-140">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="08386-141">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="08386-141">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="08386-142">Weitere Details zum Hand Shake Prozess finden Sie in der [SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="08386-142">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="08386-143">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="08386-143">15 seconds</span></span> | <span data-ttu-id="08386-144">Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten.</span><span class="sxs-lookup"><span data-stu-id="08386-144">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="08386-145">Wenn Sie `KeepAliveInterval`ändern, ändern Sie die Einstellung `ServerTimeout`/`serverTimeoutInMilliseconds` auf dem Client.</span><span class="sxs-lookup"><span data-stu-id="08386-145">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="08386-146">Die empfohlene `ServerTimeout`/`serverTimeoutInMilliseconds` Wert ist Double der `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="08386-146">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="08386-147">Alle installierten Protokolle</span><span class="sxs-lookup"><span data-stu-id="08386-147">All installed protocols</span></span> | <span data-ttu-id="08386-148">Protokolle, die von diesem Hub unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="08386-148">Protocols supported by this hub.</span></span> <span data-ttu-id="08386-149">Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, aber Protokolle können aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="08386-149">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="08386-150">Wenn `true`, werden detaillierte Ausnahme Meldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="08386-150">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="08386-151">Der Standardwert ist `false`, da diese Ausnahme Meldungen vertrauliche Informationen enthalten können.</span><span class="sxs-lookup"><span data-stu-id="08386-151">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="08386-152">Die maximale Anzahl von Elementen, die für Client-uploadstreams gepuffert werden können.</span><span class="sxs-lookup"><span data-stu-id="08386-152">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="08386-153">Wenn dieser Grenzwert erreicht wird, wird die Verarbeitung von Aufrufen blockiert, bis der Server streamingelemente verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="08386-153">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="08386-154">32 KB</span><span class="sxs-lookup"><span data-stu-id="08386-154">32 KB</span></span> | <span data-ttu-id="08386-155">Maximale Größe einer einzelnen eingehenden Hub-Nachricht.</span><span class="sxs-lookup"><span data-stu-id="08386-155">Maximum size of a single incoming hub message.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.2"

| <span data-ttu-id="08386-156">Option</span><span class="sxs-lookup"><span data-stu-id="08386-156">Option</span></span> | <span data-ttu-id="08386-157">Standardwert</span><span class="sxs-lookup"><span data-stu-id="08386-157">Default Value</span></span> | <span data-ttu-id="08386-158">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="08386-158">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="08386-159">30 Sekunden</span><span class="sxs-lookup"><span data-stu-id="08386-159">30 seconds</span></span> | <span data-ttu-id="08386-160">Der Server wird vom-Client als getrennt betrachtet, wenn in diesem Intervall keine Nachricht (einschließlich Keep-Alive) empfangen wurde.</span><span class="sxs-lookup"><span data-stu-id="08386-160">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="08386-161">Es kann länger dauern, bis der Client als getrennt gekennzeichnet ist, weil dies implementiert ist.</span><span class="sxs-lookup"><span data-stu-id="08386-161">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="08386-162">Der empfohlene Wert ist Double der `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="08386-162">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="08386-163">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="08386-163">15 seconds</span></span> | <span data-ttu-id="08386-164">Wenn der Client innerhalb dieses Zeitraums keine anfängliche Hand Shake Nachricht sendet, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="08386-164">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="08386-165">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="08386-165">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="08386-166">Weitere Details zum Hand Shake Prozess finden Sie in der [SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="08386-166">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="08386-167">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="08386-167">15 seconds</span></span> | <span data-ttu-id="08386-168">Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten.</span><span class="sxs-lookup"><span data-stu-id="08386-168">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="08386-169">Wenn Sie `KeepAliveInterval`ändern, ändern Sie die Einstellung `ServerTimeout`/`serverTimeoutInMilliseconds` auf dem Client.</span><span class="sxs-lookup"><span data-stu-id="08386-169">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="08386-170">Die empfohlene `ServerTimeout`/`serverTimeoutInMilliseconds` Wert ist Double der `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="08386-170">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="08386-171">Alle installierten Protokolle</span><span class="sxs-lookup"><span data-stu-id="08386-171">All installed protocols</span></span> | <span data-ttu-id="08386-172">Protokolle, die von diesem Hub unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="08386-172">Protocols supported by this hub.</span></span> <span data-ttu-id="08386-173">Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, aber Protokolle können aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="08386-173">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="08386-174">Wenn `true`, werden detaillierte Ausnahme Meldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="08386-174">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="08386-175">Der Standardwert ist `false`, da diese Ausnahme Meldungen vertrauliche Informationen enthalten können.</span><span class="sxs-lookup"><span data-stu-id="08386-175">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| <span data-ttu-id="08386-176">Option</span><span class="sxs-lookup"><span data-stu-id="08386-176">Option</span></span> | <span data-ttu-id="08386-177">Standardwert</span><span class="sxs-lookup"><span data-stu-id="08386-177">Default Value</span></span> | <span data-ttu-id="08386-178">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="08386-178">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="08386-179">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="08386-179">15 seconds</span></span> | <span data-ttu-id="08386-180">Wenn der Client innerhalb dieses Zeitraums keine anfängliche Hand Shake Nachricht sendet, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="08386-180">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="08386-181">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="08386-181">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="08386-182">Weitere Details zum Hand Shake Prozess finden Sie in der [SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="08386-182">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="08386-183">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="08386-183">15 seconds</span></span> | <span data-ttu-id="08386-184">Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten.</span><span class="sxs-lookup"><span data-stu-id="08386-184">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="08386-185">Wenn Sie `KeepAliveInterval`ändern, ändern Sie die Einstellung `ServerTimeout`/`serverTimeoutInMilliseconds` auf dem Client.</span><span class="sxs-lookup"><span data-stu-id="08386-185">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="08386-186">Die empfohlene `ServerTimeout`/`serverTimeoutInMilliseconds` Wert ist Double der `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="08386-186">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="08386-187">Alle installierten Protokolle</span><span class="sxs-lookup"><span data-stu-id="08386-187">All installed protocols</span></span> | <span data-ttu-id="08386-188">Protokolle, die von diesem Hub unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="08386-188">Protocols supported by this hub.</span></span> <span data-ttu-id="08386-189">Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, aber Protokolle können aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="08386-189">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="08386-190">Wenn `true`, werden detaillierte Ausnahme Meldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="08386-190">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="08386-191">Der Standardwert ist `false`, da diese Ausnahme Meldungen vertrauliche Informationen enthalten können.</span><span class="sxs-lookup"><span data-stu-id="08386-191">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

<span data-ttu-id="08386-192">Optionen können für alle Hubs konfiguriert werden, indem ein Options Delegat für den `AddSignalR` Aufruf in `Startup.ConfigureServices`bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="08386-192">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="08386-193">Optionen für einen einzelnen Hub überschreiben die in `AddSignalR` bereitgestellten globalen Optionen und können mithilfe <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="08386-193">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="08386-194">Erweiterte http-Konfigurationsoptionen</span><span class="sxs-lookup"><span data-stu-id="08386-194">Advanced HTTP configuration options</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="08386-195">Verwenden Sie `HttpConnectionDispatcherOptions`, um erweiterte Einstellungen im Zusammenhang mit Transporte und der Speicherpuffer Verwaltung zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="08386-195">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="08386-196">Diese Optionen werden durch Übergeben eines Delegaten an [maphub\<t->](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="08386-196">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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
::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="08386-197">Verwenden Sie `HttpConnectionDispatcherOptions`, um erweiterte Einstellungen im Zusammenhang mit Transporte und der Speicherpuffer Verwaltung zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="08386-197">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="08386-198">Diese Optionen werden durch Übergeben eines Delegaten an [maphub\<t->](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="08386-198">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

::: moniker-end

<span data-ttu-id="08386-199">In der folgenden Tabelle werden die Optionen zum Konfigurieren der erweiterten http-Optionen von ASP.net Core SignalRbeschrieben:</span><span class="sxs-lookup"><span data-stu-id="08386-199">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="08386-200">Option</span><span class="sxs-lookup"><span data-stu-id="08386-200">Option</span></span> | <span data-ttu-id="08386-201">Standardwert</span><span class="sxs-lookup"><span data-stu-id="08386-201">Default Value</span></span> | <span data-ttu-id="08386-202">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="08386-202">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="08386-203">32 KB</span><span class="sxs-lookup"><span data-stu-id="08386-203">32 KB</span></span> | <span data-ttu-id="08386-204">Die maximale Anzahl von Bytes, die vom Client vor dem Anwenden des backdrucks vom Server gepuffert werden.</span><span class="sxs-lookup"><span data-stu-id="08386-204">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="08386-205">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten schneller empfangen, ohne dass ein Rückdruck angewendet wird, der Arbeitsspeicher Verbrauch kann jedoch gesteigert werden.</span><span class="sxs-lookup"><span data-stu-id="08386-205">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="08386-206">Automatisch aus den `Authorize` Attributen gesammelte Daten, die auf die Hub-Klasse angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="08386-206">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="08386-207">Eine Liste von [iautorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) -Objekten, die verwendet werden, um zu bestimmen, ob ein Client für die Verbindung mit dem Hub autorisiert ist.</span><span class="sxs-lookup"><span data-stu-id="08386-207">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="08386-208">32 KB</span><span class="sxs-lookup"><span data-stu-id="08386-208">32 KB</span></span> | <span data-ttu-id="08386-209">Die maximale Anzahl von Bytes, die von der APP gesendet werden, die der Server puffert, bevor der Rückdruck beobachtet wird</span><span class="sxs-lookup"><span data-stu-id="08386-209">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="08386-210">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten schneller Puffern, ohne dass auf den Rückstand gewartet wird, aber die Arbeitsspeicher Nutzung erhöhen kann.</span><span class="sxs-lookup"><span data-stu-id="08386-210">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="08386-211">Alle Transporte sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="08386-211">All Transports are enabled.</span></span> | <span data-ttu-id="08386-212">Ein Bitflags-Enumeration von `HttpTransportType` Werten, mit denen die Transporte eingeschränkt werden können, die ein Client für die Verbindungs Herstellung verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="08386-212">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="08386-213">Siehe weiter unten.</span><span class="sxs-lookup"><span data-stu-id="08386-213">See below.</span></span> | <span data-ttu-id="08386-214">Zusätzliche Optionen, die für den langen Abruf Transport spezifisch sind.</span><span class="sxs-lookup"><span data-stu-id="08386-214">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="08386-215">Siehe weiter unten.</span><span class="sxs-lookup"><span data-stu-id="08386-215">See below.</span></span> | <span data-ttu-id="08386-216">Zusätzliche Optionen für den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="08386-216">Additional options specific to the WebSockets transport.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| <span data-ttu-id="08386-217">Option</span><span class="sxs-lookup"><span data-stu-id="08386-217">Option</span></span> | <span data-ttu-id="08386-218">Standardwert</span><span class="sxs-lookup"><span data-stu-id="08386-218">Default Value</span></span> | <span data-ttu-id="08386-219">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="08386-219">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="08386-220">32 KB</span><span class="sxs-lookup"><span data-stu-id="08386-220">32 KB</span></span> | <span data-ttu-id="08386-221">Die maximale Anzahl von Bytes, die vom Client empfangen werden, der vom Server gepuffert wird.</span><span class="sxs-lookup"><span data-stu-id="08386-221">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="08386-222">Das Erhöhen dieses Werts ermöglicht dem Server, größere Nachrichten zu empfangen, kann sich jedoch negativ auf die Arbeitsspeicher Auslastung auswirken.</span><span class="sxs-lookup"><span data-stu-id="08386-222">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="08386-223">Automatisch aus den `Authorize` Attributen gesammelte Daten, die auf die Hub-Klasse angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="08386-223">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="08386-224">Eine Liste von [iautorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) -Objekten, die verwendet werden, um zu bestimmen, ob ein Client für die Verbindung mit dem Hub autorisiert ist.</span><span class="sxs-lookup"><span data-stu-id="08386-224">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="08386-225">32 KB</span><span class="sxs-lookup"><span data-stu-id="08386-225">32 KB</span></span> | <span data-ttu-id="08386-226">Die maximale Anzahl von Bytes, die von der APP gesendet werden, die der Server puffert.</span><span class="sxs-lookup"><span data-stu-id="08386-226">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="08386-227">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten senden, kann sich jedoch negativ auf die Arbeitsspeicher Auslastung auswirken.</span><span class="sxs-lookup"><span data-stu-id="08386-227">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="08386-228">Alle Transporte sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="08386-228">All Transports are enabled.</span></span> | <span data-ttu-id="08386-229">Ein Bitflags-Enumeration von `HttpTransportType` Werten, mit denen die Transporte eingeschränkt werden können, die ein Client für die Verbindungs Herstellung verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="08386-229">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="08386-230">Siehe weiter unten.</span><span class="sxs-lookup"><span data-stu-id="08386-230">See below.</span></span> | <span data-ttu-id="08386-231">Zusätzliche Optionen, die für den langen Abruf Transport spezifisch sind.</span><span class="sxs-lookup"><span data-stu-id="08386-231">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="08386-232">Siehe weiter unten.</span><span class="sxs-lookup"><span data-stu-id="08386-232">See below.</span></span> | <span data-ttu-id="08386-233">Zusätzliche Optionen für den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="08386-233">Additional options specific to the WebSockets transport.</span></span> |

::: moniker-end

<span data-ttu-id="08386-234">Der lange Abruf Transport bietet zusätzliche Optionen, die mit der `LongPolling`-Eigenschaft konfiguriert werden können:</span><span class="sxs-lookup"><span data-stu-id="08386-234">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="08386-235">Option</span><span class="sxs-lookup"><span data-stu-id="08386-235">Option</span></span> | <span data-ttu-id="08386-236">Standardwert</span><span class="sxs-lookup"><span data-stu-id="08386-236">Default Value</span></span> | <span data-ttu-id="08386-237">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="08386-237">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="08386-238">90 Sekunden</span><span class="sxs-lookup"><span data-stu-id="08386-238">90 seconds</span></span> | <span data-ttu-id="08386-239">Die maximale Zeitspanne, die der Server wartet, bis eine Nachricht an den Client gesendet wird, bevor eine einzelne Abruf Anforderung beendet wird.</span><span class="sxs-lookup"><span data-stu-id="08386-239">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="08386-240">Das verringern dieses Werts bewirkt, dass der Client neue Abruf Anforderungen häufiger ausgibt.</span><span class="sxs-lookup"><span data-stu-id="08386-240">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="08386-241">Der WebSocket-Transport verfügt über zusätzliche Optionen, die mit der `WebSockets`-Eigenschaft konfiguriert werden können:</span><span class="sxs-lookup"><span data-stu-id="08386-241">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="08386-242">Option</span><span class="sxs-lookup"><span data-stu-id="08386-242">Option</span></span> | <span data-ttu-id="08386-243">Standardwert</span><span class="sxs-lookup"><span data-stu-id="08386-243">Default Value</span></span> | <span data-ttu-id="08386-244">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="08386-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="08386-245">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="08386-245">5 seconds</span></span> | <span data-ttu-id="08386-246">Wenn der Client nach dem Schließen des Servers nicht innerhalb dieses Zeitraums geschlossen werden kann, wird die Verbindung beendet.</span><span class="sxs-lookup"><span data-stu-id="08386-246">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="08386-247">Ein Delegat, der verwendet werden kann, um den `Sec-WebSocket-Protocol`-Header auf einen benutzerdefinierten Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="08386-247">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="08386-248">Der Delegat empfängt die vom Client angeforderten Werte als Eingabe und erwartet, dass der gewünschte Wert zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="08386-248">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="08386-249">Konfigurieren von Client Optionen</span><span class="sxs-lookup"><span data-stu-id="08386-249">Configure client options</span></span>

<span data-ttu-id="08386-250">Client Optionen können für den `HubConnectionBuilder` Typ konfiguriert werden (verfügbar in den .net-und JavaScript-Clients).</span><span class="sxs-lookup"><span data-stu-id="08386-250">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="08386-251">Sie steht auch im Java-Client zur Verfügung, aber die `HttpHubConnectionBuilder`-Unterklasse enthält die Generator-Konfigurationsoptionen sowie die `HubConnection` selbst.</span><span class="sxs-lookup"><span data-stu-id="08386-251">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="08386-252">Konfigurieren der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="08386-252">Configure logging</span></span>

<span data-ttu-id="08386-253">Die Protokollierung wird mit der `ConfigureLogging`-Methode im .NET-Client konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="08386-253">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="08386-254">Protokollierungs Anbieter und Filter können auf die gleiche Weise wie auf dem Server registriert werden.</span><span class="sxs-lookup"><span data-stu-id="08386-254">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="08386-255">Weitere Informationen finden Sie in der Dokumentation zur [Protokollierung ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="08386-255">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="08386-256">Um Protokollierungs Anbieter zu registrieren, müssen Sie die erforderlichen Pakete installieren.</span><span class="sxs-lookup"><span data-stu-id="08386-256">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="08386-257">Eine vollständige Liste finden Sie im Abschnitt [integrierte Protokollierungs Anbieter](xref:fundamentals/logging/index#built-in-logging-providers) der Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="08386-257">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="08386-258">Wenn Sie z. b. die Konsolen Protokollierung aktivieren möchten, installieren Sie das nuget-Paket `Microsoft.Extensions.Logging.Console`.</span><span class="sxs-lookup"><span data-stu-id="08386-258">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="08386-259">Nennen Sie die `AddConsole`-Erweiterungsmethode:</span><span class="sxs-lookup"><span data-stu-id="08386-259">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="08386-260">Im JavaScript-Client ist eine ähnliche `configureLogging` Methode vorhanden.</span><span class="sxs-lookup"><span data-stu-id="08386-260">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="08386-261">Geben Sie einen `LogLevel` Wert an, der die minimale Ebene der zu erstellenden Protokollmeldungen angibt.</span><span class="sxs-lookup"><span data-stu-id="08386-261">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="08386-262">Protokolle werden in das Browser Konsolenfenster geschrieben.</span><span class="sxs-lookup"><span data-stu-id="08386-262">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="08386-263">Anstelle eines `LogLevel` Werts können Sie auch einen `string` Wert angeben, der einen Namen für die Protokollebene darstellt.</span><span class="sxs-lookup"><span data-stu-id="08386-263">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="08386-264">Dies ist nützlich, wenn Sie SignalR Protokollierung in Umgebungen konfigurieren, in denen Sie keinen Zugriff auf die `LogLevel` Konstanten haben.</span><span class="sxs-lookup"><span data-stu-id="08386-264">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="08386-265">In der folgenden Tabelle sind die verfügbaren Protokoll Ebenen aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="08386-265">The following table lists the available log levels.</span></span> <span data-ttu-id="08386-266">Der von Ihnen bereitgestellte Wert `configureLogging` legt den **minimalen** Protokolliergrad fest, der protokolliert werden soll.</span><span class="sxs-lookup"><span data-stu-id="08386-266">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="08386-267">Nachrichten, die auf dieser Ebene protokolliert werden, **oder die nach der Tabelle aufgeführten Ebenen**werden protokolliert.</span><span class="sxs-lookup"><span data-stu-id="08386-267">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="08386-268">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="08386-268">String</span></span>                      | <span data-ttu-id="08386-269">LogLevel</span><span class="sxs-lookup"><span data-stu-id="08386-269">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="08386-270">`info` **oder** `information`</span><span class="sxs-lookup"><span data-stu-id="08386-270">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="08386-271">`warn` **oder** `warning`</span><span class="sxs-lookup"><span data-stu-id="08386-271">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

::: moniker-end

> [!NOTE]
> <span data-ttu-id="08386-272">Um die Protokollierung vollständig zu deaktivieren, geben Sie `signalR.LogLevel.None` in der `configureLogging` Methode an.</span><span class="sxs-lookup"><span data-stu-id="08386-272">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="08386-273">Weitere Informationen zur Protokollierung finden Sie in der [SignalR Diagnostics-Dokumentation](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="08386-273">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="08386-274">Der SignalR Java-Client verwendet die [SLF4J](https://www.slf4j.org/) -Bibliothek für die Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="08386-274">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="08386-275">Dabei handelt es sich um eine allgemeine Protokollierungs-API, die es Benutzern der Bibliothek ermöglicht, ihre eigene spezifische Protokollierungs Implementierung zu wählen, indem Sie eine bestimmte Protokollierungs Abhängigkeit einbinden.</span><span class="sxs-lookup"><span data-stu-id="08386-275">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="08386-276">Der folgende Code Ausschnitt zeigt, wie `java.util.logging` mit dem SignalR Java-Client verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="08386-276">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="08386-277">Wenn Sie die Protokollierung in ihren Abhängigkeiten nicht konfigurieren, lädt SLF4J eine Standard Protokollierung ohne Vorgang mit folgender Warnmeldung:</span><span class="sxs-lookup"><span data-stu-id="08386-277">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="08386-278">Dies kann sicher ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="08386-278">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="08386-279">Zulässige Transporte konfigurieren</span><span class="sxs-lookup"><span data-stu-id="08386-279">Configure allowed transports</span></span>

<span data-ttu-id="08386-280">Die von SignalR verwendeten Transporte können im `WithUrl`-Befehl (`withUrl` in JavaScript) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="08386-280">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="08386-281">Eine bitweise OR-Angabe der Werte `HttpTransportType` kann verwendet werden, um den Client so einzuschränken, dass nur die angegebenen Transporte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="08386-281">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="08386-282">Alle Transporte sind standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="08386-282">All transports are enabled by default.</span></span>

<span data-ttu-id="08386-283">So können Sie beispielsweise den Transport von Server gesendeten Ereignissen deaktivieren, aber websockets und lange Abruf Verbindungen zulassen:</span><span class="sxs-lookup"><span data-stu-id="08386-283">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="08386-284">Im JavaScript-Client werden Transporte durch Festlegen des `transport` Felds für das Options-Objekt konfiguriert, das `withUrl`bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="08386-284">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="08386-285">In dieser Version des Java-Clients ist websockets der einzige verfügbare Transport.</span><span class="sxs-lookup"><span data-stu-id="08386-285">In this version of the Java client websockets is the only available transport.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="08386-286">Im Java-Client wird der Transport mit der `withTransport`-Methode auf dem `HttpHubConnectionBuilder`ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="08386-286">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="08386-287">Der Java-Client verwendet standardmäßig den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="08386-287">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="08386-288">Der SignalR Java-Client unterstützt noch keinen Transport Fall Back.</span><span class="sxs-lookup"><span data-stu-id="08386-288">The SignalR Java client doesn't support transport fallback yet.</span></span>

::: moniker-end

### <a name="configure-bearer-authentication"></a><span data-ttu-id="08386-289">Konfigurieren der bearerauthentifizierung</span><span class="sxs-lookup"><span data-stu-id="08386-289">Configure bearer authentication</span></span>

<span data-ttu-id="08386-290">Um Authentifizierungsdaten zusammen mit SignalR Anforderungen bereitzustellen, verwenden Sie die `AccessTokenProvider`-Option (`accessTokenFactory` in JavaScript), um eine Funktion anzugeben, die das gewünschte Zugriffs Token zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="08386-290">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="08386-291">Im .NET-Client wird dieses Zugriffs Token als http-Token für die bearerauthentifizierung (unter Verwendung des `Authorization`-Headers mit dem Typ `Bearer`) übergebenen.</span><span class="sxs-lookup"><span data-stu-id="08386-291">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="08386-292">Im JavaScript-Client wird das Zugriffs Token als bearertoken verwendet, **außer** in einigen Fällen, in denen Browser-APIs die Fähigkeit zum Anwenden von Headern einschränken (insbesondere bei vom Server gesendeten Ereignissen und websockets-Anforderungen).</span><span class="sxs-lookup"><span data-stu-id="08386-292">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="08386-293">In diesen Fällen wird das Zugriffs Token als Abfrage Zeichenfolgen-Wert `access_token`bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="08386-293">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="08386-294">Im .NET-Client kann die `AccessTokenProvider`-Option mit dem Options Delegaten in `WithUrl`angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="08386-294">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="08386-295">Im JavaScript-Client wird das Zugriffs Token konfiguriert, indem das `accessTokenFactory`-Feld für das Options-Objekt in `withUrl`festgelegt wird:</span><span class="sxs-lookup"><span data-stu-id="08386-295">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="08386-296">Im SignalR Java-Client können Sie ein bearertoken für die Authentifizierung konfigurieren, indem Sie eine zugriffstokenfactory für " [httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)" bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="08386-296">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="08386-297">Verwenden Sie [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , um eine [rxjava](https://github.com/ReactiveX/RxJava) - [\<Zeichenfolge >](https://reactivex.io/documentation/single.html)bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="08386-297">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="08386-298">Beim Aufrufen von " [Single.](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)Write" können Sie Logik schreiben, um Zugriffs Token für den Client zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="08386-298">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="08386-299">Konfigurieren von Timeout-und Keep-Alive-Optionen</span><span class="sxs-lookup"><span data-stu-id="08386-299">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="08386-300">Zusätzliche Optionen zum Konfigurieren von Timeout und Keep-Alive-Verhalten sind im `HubConnection` Objekt selbst verfügbar:</span><span class="sxs-lookup"><span data-stu-id="08386-300">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>


::: moniker range=">= aspnetcore-2.2"

# <a name="nettabdotnet"></a>[<span data-ttu-id="08386-301">.NET</span><span class="sxs-lookup"><span data-stu-id="08386-301">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="08386-302">Option</span><span class="sxs-lookup"><span data-stu-id="08386-302">Option</span></span> | <span data-ttu-id="08386-303">Standardwert</span><span class="sxs-lookup"><span data-stu-id="08386-303">Default value</span></span> | <span data-ttu-id="08386-304">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="08386-304">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="08386-305">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="08386-305">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="08386-306">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="08386-306">Timeout for server activity.</span></span> <span data-ttu-id="08386-307">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `Closed` Ereignis aus (`onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="08386-307">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="08386-308">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="08386-308">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="08386-309">Der empfohlene Wert ist eine Zahl, die mindestens den `KeepAliveInterval` Wert des Servers verdoppelt, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="08386-309">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="08386-310">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="08386-310">15 seconds</span></span> | <span data-ttu-id="08386-311">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="08386-311">Timeout for initial server handshake.</span></span> <span data-ttu-id="08386-312">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `Closed` Ereignis aus (`onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="08386-312">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="08386-313">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="08386-313">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="08386-314">Weitere Details zum Hand Shake Prozess finden Sie in der [SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="08386-314">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="08386-315">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="08386-315">15 seconds</span></span> | <span data-ttu-id="08386-316">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="08386-316">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="08386-317">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="08386-317">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="08386-318">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` gesendet hat, die auf dem Server festgelegt wurde, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="08386-318">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="08386-319">Im .NET-Client werden Timeout Werte als `TimeSpan` Werte angegeben.</span><span class="sxs-lookup"><span data-stu-id="08386-319">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="08386-320">JavaScript</span><span class="sxs-lookup"><span data-stu-id="08386-320">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="08386-321">Option</span><span class="sxs-lookup"><span data-stu-id="08386-321">Option</span></span> | <span data-ttu-id="08386-322">Standardwert</span><span class="sxs-lookup"><span data-stu-id="08386-322">Default value</span></span> | <span data-ttu-id="08386-323">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="08386-323">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="08386-324">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="08386-324">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="08386-325">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="08386-325">Timeout for server activity.</span></span> <span data-ttu-id="08386-326">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `onclose`-Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="08386-326">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="08386-327">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="08386-327">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="08386-328">Der empfohlene Wert ist eine Zahl, die mindestens den `KeepAliveInterval` Wert des Servers verdoppelt, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="08386-328">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="08386-329">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="08386-329">15 seconds (15,000 miliseconds)</span></span> | <span data-ttu-id="08386-330">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="08386-330">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="08386-331">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="08386-331">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="08386-332">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` gesendet hat, die auf dem Server festgelegt wurde, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="08386-332">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="08386-333">Java</span><span class="sxs-lookup"><span data-stu-id="08386-333">Java</span></span>](#tab/java)

| <span data-ttu-id="08386-334">Option</span><span class="sxs-lookup"><span data-stu-id="08386-334">Option</span></span> | <span data-ttu-id="08386-335">Standardwert</span><span class="sxs-lookup"><span data-stu-id="08386-335">Default value</span></span> | <span data-ttu-id="08386-336">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="08386-336">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="08386-337">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="08386-337">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="08386-338">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="08386-338">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="08386-339">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="08386-339">Timeout for server activity.</span></span> <span data-ttu-id="08386-340">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `onClose`-Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="08386-340">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="08386-341">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="08386-341">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="08386-342">Der empfohlene Wert ist eine Zahl, die mindestens den `KeepAliveInterval` Wert des Servers verdoppelt, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="08386-342">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="08386-343">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="08386-343">15 seconds</span></span> | <span data-ttu-id="08386-344">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="08386-344">Timeout for initial server handshake.</span></span> <span data-ttu-id="08386-345">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `onClose`-Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="08386-345">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="08386-346">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="08386-346">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="08386-347">Weitere Details zum Hand Shake Prozess finden Sie in der [SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="08386-347">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="08386-348">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="08386-348">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="08386-349">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="08386-349">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="08386-350">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="08386-350">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="08386-351">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="08386-351">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="08386-352">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` gesendet hat, die auf dem Server festgelegt wurde, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="08386-352">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="nettabdotnet"></a>[<span data-ttu-id="08386-353">.NET</span><span class="sxs-lookup"><span data-stu-id="08386-353">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="08386-354">Option</span><span class="sxs-lookup"><span data-stu-id="08386-354">Option</span></span> | <span data-ttu-id="08386-355">Standardwert</span><span class="sxs-lookup"><span data-stu-id="08386-355">Default value</span></span> | <span data-ttu-id="08386-356">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="08386-356">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="08386-357">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="08386-357">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="08386-358">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="08386-358">Timeout for server activity.</span></span> <span data-ttu-id="08386-359">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `Closed` Ereignis aus (`onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="08386-359">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="08386-360">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="08386-360">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="08386-361">Der empfohlene Wert ist eine Zahl, die mindestens den `KeepAliveInterval` Wert des Servers verdoppelt, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="08386-361">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="08386-362">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="08386-362">15 seconds</span></span> | <span data-ttu-id="08386-363">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="08386-363">Timeout for initial server handshake.</span></span> <span data-ttu-id="08386-364">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `Closed` Ereignis aus (`onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="08386-364">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="08386-365">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="08386-365">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="08386-366">Weitere Details zum Hand Shake Prozess finden Sie in der [SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="08386-366">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="08386-367">Im .NET-Client werden Timeout Werte als `TimeSpan` Werte angegeben.</span><span class="sxs-lookup"><span data-stu-id="08386-367">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="08386-368">JavaScript</span><span class="sxs-lookup"><span data-stu-id="08386-368">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="08386-369">Option</span><span class="sxs-lookup"><span data-stu-id="08386-369">Option</span></span> | <span data-ttu-id="08386-370">Standardwert</span><span class="sxs-lookup"><span data-stu-id="08386-370">Default value</span></span> | <span data-ttu-id="08386-371">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="08386-371">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="08386-372">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="08386-372">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="08386-373">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="08386-373">Timeout for server activity.</span></span> <span data-ttu-id="08386-374">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `onclose`-Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="08386-374">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="08386-375">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="08386-375">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="08386-376">Der empfohlene Wert ist eine Zahl, die mindestens den `KeepAliveInterval` Wert des Servers verdoppelt, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="08386-376">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="08386-377">Java</span><span class="sxs-lookup"><span data-stu-id="08386-377">Java</span></span>](#tab/java)

| <span data-ttu-id="08386-378">Option</span><span class="sxs-lookup"><span data-stu-id="08386-378">Option</span></span> | <span data-ttu-id="08386-379">Standardwert</span><span class="sxs-lookup"><span data-stu-id="08386-379">Default value</span></span> | <span data-ttu-id="08386-380">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="08386-380">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="08386-381">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="08386-381">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="08386-382">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="08386-382">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="08386-383">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="08386-383">Timeout for server activity.</span></span> <span data-ttu-id="08386-384">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `onClose`-Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="08386-384">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="08386-385">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="08386-385">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="08386-386">Der empfohlene Wert ist eine Zahl, die mindestens dem `KeepAliveInterval` Wert des Servers entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="08386-386">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="08386-387">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="08386-387">15 seconds</span></span> | <span data-ttu-id="08386-388">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="08386-388">Timeout for initial server handshake.</span></span> <span data-ttu-id="08386-389">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `onClose`-Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="08386-389">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="08386-390">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="08386-390">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="08386-391">Weitere Details zum Hand Shake Prozess finden Sie in der [SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="08386-391">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

::: moniker-end

---

### <a name="configure-additional-options"></a><span data-ttu-id="08386-392">Zusätzliche Optionen konfigurieren</span><span class="sxs-lookup"><span data-stu-id="08386-392">Configure additional options</span></span>

<span data-ttu-id="08386-393">Zusätzliche Optionen können in der `WithUrl`-Methode (`withUrl` in JavaScript) auf `HubConnectionBuilder` oder auf den verschiedenen Konfigurations-APIs auf dem `HttpHubConnectionBuilder` im Java-Client konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="08386-393">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="08386-394">.NET</span><span class="sxs-lookup"><span data-stu-id="08386-394">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="08386-395">.Net-Option</span><span class="sxs-lookup"><span data-stu-id="08386-395">.NET Option</span></span> |  <span data-ttu-id="08386-396">Standardwert</span><span class="sxs-lookup"><span data-stu-id="08386-396">Default value</span></span> | <span data-ttu-id="08386-397">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="08386-397">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="08386-398">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="08386-398">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="08386-399">Legen Sie diese Einstellung auf `true`, um den Aushandlungs Schritt auszulassen.</span><span class="sxs-lookup"><span data-stu-id="08386-399">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="08386-400">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="08386-400">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="08386-401">Diese Einstellung kann nicht aktiviert werden, wenn der Azure SignalR-Dienst verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="08386-401">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="08386-402">Empty</span><span class="sxs-lookup"><span data-stu-id="08386-402">Empty</span></span> | <span data-ttu-id="08386-403">Eine Auflistung von TLS-Zertifikaten, die zum Authentifizieren von Anforderungen gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="08386-403">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="08386-404">Empty</span><span class="sxs-lookup"><span data-stu-id="08386-404">Empty</span></span> | <span data-ttu-id="08386-405">Eine Auflistung von HTTP-Cookies, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="08386-405">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="08386-406">Empty</span><span class="sxs-lookup"><span data-stu-id="08386-406">Empty</span></span> | <span data-ttu-id="08386-407">Anmelde Informationen, die mit jeder HTTP-Anforderung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="08386-407">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="08386-408">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="08386-408">5 seconds</span></span> | <span data-ttu-id="08386-409">Nur websockets.</span><span class="sxs-lookup"><span data-stu-id="08386-409">WebSockets only.</span></span> <span data-ttu-id="08386-410">Die maximale Zeitspanne, die der Client nach dem Schließen des Servers wartet, um die Anforderung zum Schließen zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="08386-410">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="08386-411">Wenn der Server die Schließung innerhalb dieses Zeitraums nicht anerkennt, trennt der Client die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="08386-411">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="08386-412">Empty</span><span class="sxs-lookup"><span data-stu-id="08386-412">Empty</span></span> | <span data-ttu-id="08386-413">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="08386-413">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="08386-414">Ein Delegat, der verwendet werden kann, um die zum Senden von HTTP-Anforderungen verwendete `HttpMessageHandler` zu konfigurieren oder zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="08386-414">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="08386-415">Wird nicht für WebSocket-Verbindungen verwendet.</span><span class="sxs-lookup"><span data-stu-id="08386-415">Not used for WebSocket connections.</span></span> <span data-ttu-id="08386-416">Dieser Delegat muss einen Wert zurückgeben, der nicht NULL ist, und erhält den Standardwert als Parameter.</span><span class="sxs-lookup"><span data-stu-id="08386-416">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="08386-417">Ändern Sie die Einstellungen für diesen Standardwert, und geben Sie ihn zurück, oder geben Sie eine neue `HttpMessageHandler` Instanz zurück.</span><span class="sxs-lookup"><span data-stu-id="08386-417">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="08386-418">**Wenn Sie den Handler ersetzen, stellen Sie sicher, dass die Einstellungen, die Sie beibehalten möchten, vom bereitgestellten Handler kopiert werden. Andernfalls gelten die konfigurierten Optionen (z. b. Cookies und Header) nicht für den neuen Handler.**</span><span class="sxs-lookup"><span data-stu-id="08386-418">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="08386-419">Ein HTTP-Proxy, der beim Senden von HTTP-Anforderungen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="08386-419">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="08386-420">Legen Sie diesen booleschen Wert fest, um die Standard Anmelde Informationen für http-und websockets-Anforderungen zu senden</span><span class="sxs-lookup"><span data-stu-id="08386-420">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="08386-421">Dies ermöglicht die Verwendung der Windows-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="08386-421">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="08386-422">Ein Delegat, der zum Konfigurieren zusätzlicher WebSocket-Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="08386-422">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="08386-423">Empfängt eine Instanz von [clientwebsocketoptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , die zum Konfigurieren der Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="08386-423">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="08386-424">JavaScript</span><span class="sxs-lookup"><span data-stu-id="08386-424">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="08386-425">JavaScript-Option</span><span class="sxs-lookup"><span data-stu-id="08386-425">JavaScript Option</span></span> | <span data-ttu-id="08386-426">Standardwert</span><span class="sxs-lookup"><span data-stu-id="08386-426">Default Value</span></span> | <span data-ttu-id="08386-427">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="08386-427">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="08386-428">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="08386-428">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="08386-429">Legen Sie diese Einstellung auf `true`, um den Aushandlungs Schritt auszulassen.</span><span class="sxs-lookup"><span data-stu-id="08386-429">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="08386-430">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="08386-430">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="08386-431">Diese Einstellung kann nicht aktiviert werden, wenn der Azure SignalR-Dienst verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="08386-431">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="08386-432">Java</span><span class="sxs-lookup"><span data-stu-id="08386-432">Java</span></span>](#tab/java)

| <span data-ttu-id="08386-433">Java-Option</span><span class="sxs-lookup"><span data-stu-id="08386-433">Java Option</span></span> | <span data-ttu-id="08386-434">Standardwert</span><span class="sxs-lookup"><span data-stu-id="08386-434">Default Value</span></span> | <span data-ttu-id="08386-435">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="08386-435">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="08386-436">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="08386-436">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="08386-437">Legen Sie diese Einstellung auf `true`, um den Aushandlungs Schritt auszulassen.</span><span class="sxs-lookup"><span data-stu-id="08386-437">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="08386-438">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="08386-438">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="08386-439">Diese Einstellung kann nicht aktiviert werden, wenn der Azure SignalR-Dienst verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="08386-439">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="08386-440">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="08386-440">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="08386-441">Empty</span><span class="sxs-lookup"><span data-stu-id="08386-441">Empty</span></span> | <span data-ttu-id="08386-442">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="08386-442">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="08386-443">Im .NET-Client können diese Optionen mit dem für `WithUrl`bereitgestellten Options Delegaten geändert werden:</span><span class="sxs-lookup"><span data-stu-id="08386-443">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="08386-444">Im JavaScript-Client können diese Optionen in einem JavaScript-Objekt bereitgestellt werden, das für `withUrl`bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="08386-444">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="08386-445">Im Java-Client können diese Optionen mit den Methoden für die `HttpHubConnectionBuilder` konfiguriert werden, die vom `HubConnectionBuilder.create("HUB URL")` zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="08386-445">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="08386-446">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="08386-446">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

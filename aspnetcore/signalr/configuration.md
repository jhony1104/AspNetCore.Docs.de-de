---
title: ASP.net Core signalr-Konfiguration
author: bradygaster
description: Erfahren Sie, wie Sie ASP.net Core signalr-apps konfigurieren.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 08/05/2019
uid: signalr/configuration
ms.openlocfilehash: 66f274fcda27392091de6b4be8c7221bc87b7585
ms.sourcegitcommit: c452e6af92e130413106c4863193f377cde4cd9c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/10/2019
ms.locfileid: "72246495"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="8720f-103">ASP.net Core signalr-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="8720f-103">ASP.NET Core SignalR configuration</span></span>

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="8720f-104">JSON/messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="8720f-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="8720f-105">ASP.net Core signalr unterstützt zwei Protokolle für das Codieren von Nachrichten: [JSON](https://www.json.org/) und [messagepack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="8720f-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="8720f-106">Jedes Protokoll verfügt über Konfigurationsoptionen für die Serialisierung.</span><span class="sxs-lookup"><span data-stu-id="8720f-106">Each protocol has serialization configuration options.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8720f-107">Die JSON-Serialisierung kann mithilfe der [addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) -Erweiterungsmethode auf dem Server konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="8720f-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="8720f-108">`AddJsonProtocol` kann nach [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices` hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="8720f-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8720f-109">Die `AddJsonProtocol`-Methode nimmt einen Delegaten an, der ein `options`-Objekt empfängt.</span><span class="sxs-lookup"><span data-stu-id="8720f-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="8720f-110">Die [payloadserializeroptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) -Eigenschaft für dieses Objekt ist ein `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions>-Objekt, das zum Konfigurieren der Serialisierung von Argumenten und Rückgabe Werten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="8720f-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="8720f-111">Weitere Informationen finden Sie in der [System. Text. JSON-Dokumentation](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="8720f-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="8720f-112">Wenn Sie z. b. das Serialisierungsprogramm so konfigurieren möchten, dass die Groß-/Kleinschreibung von Eigenschaftsnamen nicht geändert wird, verwenden Sie den folgenden Code in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="8720f-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="8720f-113">Im .NET-Client ist dieselbe `AddJsonProtocol`-Erweiterungsmethode auf [hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)vorhanden.</span><span class="sxs-lookup"><span data-stu-id="8720f-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="8720f-114">Der `Microsoft.Extensions.DependencyInjection`-Namespace muss importiert werden, um die Erweiterungsmethode aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="8720f-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="8720f-115">Wechseln Sie zu "newtonsoft. JSON".</span><span class="sxs-lookup"><span data-stu-id="8720f-115">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="8720f-116">Wenn Sie Features von `Newtonsoft.Json` benötigen, die in `System.Text.Json` nicht unterstützt werden, finden Sie weitere Informationen unter [Wechseln zu "newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson)".</span><span class="sxs-lookup"><span data-stu-id="8720f-116">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="8720f-117">Die JSON-Serialisierung kann mithilfe der [addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) -Erweiterungsmethode auf dem Server konfiguriert werden, die nach [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in der `Startup.ConfigureServices`-Methode hinzugefügt werden kann.</span><span class="sxs-lookup"><span data-stu-id="8720f-117">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="8720f-118">Die `AddJsonProtocol`-Methode nimmt einen Delegaten an, der ein `options`-Objekt empfängt.</span><span class="sxs-lookup"><span data-stu-id="8720f-118">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="8720f-119">Die [payloadserializersettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) -Eigenschaft für dieses Objekt ist ein JSON.net `JsonSerializerSettings`-Objekt, das verwendet werden kann, um die Serialisierung von Argumenten und Rückgabe Werten zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="8720f-119">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="8720f-120">Weitere Informationen finden Sie in der [JSON.NET-Dokumentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="8720f-120">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="8720f-121">Wenn Sie z. b. das Serialisierungsprogramm so konfigurieren möchten, dass "PascalCase"-Eigenschaftsnamen anstelle der Standardnamen "CamelCase" verwendet werden, verwenden Sie den folgenden Code in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="8720f-121">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="8720f-122">Im .NET-Client ist dieselbe `AddJsonProtocol`-Erweiterungsmethode auf [hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)vorhanden.</span><span class="sxs-lookup"><span data-stu-id="8720f-122">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="8720f-123">Der `Microsoft.Extensions.DependencyInjection`-Namespace muss importiert werden, um die Erweiterungsmethode aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="8720f-123">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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

::: moniker-end

> [!NOTE]
> <span data-ttu-id="8720f-124">Zurzeit ist es nicht möglich, JSON-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="8720f-124">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="8720f-125">Messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="8720f-125">MessagePack serialization options</span></span>

<span data-ttu-id="8720f-126">Die messagepack-Serialisierung kann konfiguriert werden, indem ein Delegat für den [addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) -Aufruf bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="8720f-126">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="8720f-127">Weitere Informationen finden Sie [unter messagepack in signalr](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="8720f-127">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="8720f-128">Zurzeit ist es nicht möglich, die messagepack-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="8720f-128">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="8720f-129">Konfigurieren von Serveroptionen</span><span class="sxs-lookup"><span data-stu-id="8720f-129">Configure server options</span></span>

<span data-ttu-id="8720f-130">In der folgenden Tabelle werden die Optionen zum Konfigurieren von signalr Hubs beschrieben:</span><span class="sxs-lookup"><span data-stu-id="8720f-130">The following table describes options for configuring SignalR hubs:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="8720f-131">Option</span><span class="sxs-lookup"><span data-stu-id="8720f-131">Option</span></span> | <span data-ttu-id="8720f-132">Standardwert</span><span class="sxs-lookup"><span data-stu-id="8720f-132">Default Value</span></span> | <span data-ttu-id="8720f-133">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="8720f-133">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="8720f-134">30 Sekunden</span><span class="sxs-lookup"><span data-stu-id="8720f-134">30 seconds</span></span> | <span data-ttu-id="8720f-135">Der Server wird vom-Client als getrennt betrachtet, wenn in diesem Intervall keine Nachricht (einschließlich Keep-Alive) empfangen wurde.</span><span class="sxs-lookup"><span data-stu-id="8720f-135">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="8720f-136">Es kann länger dauern, bis der Client als getrennt gekennzeichnet ist, weil dies implementiert ist.</span><span class="sxs-lookup"><span data-stu-id="8720f-136">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="8720f-137">Der empfohlene Wert ist Double der `KeepAliveInterval`-Wert.</span><span class="sxs-lookup"><span data-stu-id="8720f-137">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="8720f-138">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="8720f-138">15 seconds</span></span> | <span data-ttu-id="8720f-139">Wenn der Client innerhalb dieses Zeitraums keine anfängliche Hand Shake Nachricht sendet, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="8720f-139">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="8720f-140">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="8720f-140">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="8720f-141">Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="8720f-141">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="8720f-142">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="8720f-142">15 seconds</span></span> | <span data-ttu-id="8720f-143">Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten.</span><span class="sxs-lookup"><span data-stu-id="8720f-143">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="8720f-144">Wenn Sie `KeepAliveInterval` ändern, ändern Sie die Einstellung `ServerTimeout` @ no__t-2 @ no__t-3 auf dem Client.</span><span class="sxs-lookup"><span data-stu-id="8720f-144">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="8720f-145">Der empfohlene `ServerTimeout` @ no__t-1 @ no__t-2-Wert ist Double der `KeepAliveInterval`-Wert.</span><span class="sxs-lookup"><span data-stu-id="8720f-145">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="8720f-146">Alle installierten Protokolle</span><span class="sxs-lookup"><span data-stu-id="8720f-146">All installed protocols</span></span> | <span data-ttu-id="8720f-147">Protokolle, die von diesem Hub unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="8720f-147">Protocols supported by this hub.</span></span> <span data-ttu-id="8720f-148">Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, aber Protokolle können aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="8720f-148">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="8720f-149">Wenn `true`, werden ausführliche Ausnahme Meldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="8720f-149">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="8720f-150">Der Standardwert ist `false`, da diese Ausnahme Meldungen vertrauliche Informationen enthalten können.</span><span class="sxs-lookup"><span data-stu-id="8720f-150">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="8720f-151">Die maximale Anzahl von Elementen, die für Client-uploadstreams gepuffert werden können.</span><span class="sxs-lookup"><span data-stu-id="8720f-151">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="8720f-152">Wenn dieser Grenzwert erreicht wird, wird die Verarbeitung von Aufrufen blockiert, bis der Server streamingelemente verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="8720f-152">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="8720f-153">32 KB</span><span class="sxs-lookup"><span data-stu-id="8720f-153">32 KB</span></span> | <span data-ttu-id="8720f-154">Maximale Größe einer einzelnen eingehenden Hub-Nachricht.</span><span class="sxs-lookup"><span data-stu-id="8720f-154">Maximum size of a single incoming hub message.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.2"

| <span data-ttu-id="8720f-155">Option</span><span class="sxs-lookup"><span data-stu-id="8720f-155">Option</span></span> | <span data-ttu-id="8720f-156">Standardwert</span><span class="sxs-lookup"><span data-stu-id="8720f-156">Default Value</span></span> | <span data-ttu-id="8720f-157">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="8720f-157">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="8720f-158">30 Sekunden</span><span class="sxs-lookup"><span data-stu-id="8720f-158">30 seconds</span></span> | <span data-ttu-id="8720f-159">Der Server wird vom-Client als getrennt betrachtet, wenn in diesem Intervall keine Nachricht (einschließlich Keep-Alive) empfangen wurde.</span><span class="sxs-lookup"><span data-stu-id="8720f-159">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="8720f-160">Es kann länger dauern, bis der Client als getrennt gekennzeichnet ist, weil dies implementiert ist.</span><span class="sxs-lookup"><span data-stu-id="8720f-160">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="8720f-161">Der empfohlene Wert ist Double der `KeepAliveInterval`-Wert.</span><span class="sxs-lookup"><span data-stu-id="8720f-161">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="8720f-162">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="8720f-162">15 seconds</span></span> | <span data-ttu-id="8720f-163">Wenn der Client innerhalb dieses Zeitraums keine anfängliche Hand Shake Nachricht sendet, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="8720f-163">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="8720f-164">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="8720f-164">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="8720f-165">Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="8720f-165">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="8720f-166">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="8720f-166">15 seconds</span></span> | <span data-ttu-id="8720f-167">Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten.</span><span class="sxs-lookup"><span data-stu-id="8720f-167">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="8720f-168">Wenn Sie `KeepAliveInterval` ändern, ändern Sie die Einstellung `ServerTimeout` @ no__t-2 @ no__t-3 auf dem Client.</span><span class="sxs-lookup"><span data-stu-id="8720f-168">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="8720f-169">Der empfohlene `ServerTimeout` @ no__t-1 @ no__t-2-Wert ist Double der `KeepAliveInterval`-Wert.</span><span class="sxs-lookup"><span data-stu-id="8720f-169">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="8720f-170">Alle installierten Protokolle</span><span class="sxs-lookup"><span data-stu-id="8720f-170">All installed protocols</span></span> | <span data-ttu-id="8720f-171">Protokolle, die von diesem Hub unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="8720f-171">Protocols supported by this hub.</span></span> <span data-ttu-id="8720f-172">Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, aber Protokolle können aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="8720f-172">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="8720f-173">Wenn `true`, werden ausführliche Ausnahme Meldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="8720f-173">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="8720f-174">Der Standardwert ist `false`, da diese Ausnahme Meldungen vertrauliche Informationen enthalten können.</span><span class="sxs-lookup"><span data-stu-id="8720f-174">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| <span data-ttu-id="8720f-175">Option</span><span class="sxs-lookup"><span data-stu-id="8720f-175">Option</span></span> | <span data-ttu-id="8720f-176">Standardwert</span><span class="sxs-lookup"><span data-stu-id="8720f-176">Default Value</span></span> | <span data-ttu-id="8720f-177">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="8720f-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="8720f-178">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="8720f-178">15 seconds</span></span> | <span data-ttu-id="8720f-179">Wenn der Client innerhalb dieses Zeitraums keine anfängliche Hand Shake Nachricht sendet, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="8720f-179">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="8720f-180">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="8720f-180">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="8720f-181">Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="8720f-181">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="8720f-182">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="8720f-182">15 seconds</span></span> | <span data-ttu-id="8720f-183">Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten.</span><span class="sxs-lookup"><span data-stu-id="8720f-183">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="8720f-184">Wenn Sie `KeepAliveInterval` ändern, ändern Sie die Einstellung `ServerTimeout` @ no__t-2 @ no__t-3 auf dem Client.</span><span class="sxs-lookup"><span data-stu-id="8720f-184">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="8720f-185">Der empfohlene `ServerTimeout` @ no__t-1 @ no__t-2-Wert ist Double der `KeepAliveInterval`-Wert.</span><span class="sxs-lookup"><span data-stu-id="8720f-185">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="8720f-186">Alle installierten Protokolle</span><span class="sxs-lookup"><span data-stu-id="8720f-186">All installed protocols</span></span> | <span data-ttu-id="8720f-187">Protokolle, die von diesem Hub unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="8720f-187">Protocols supported by this hub.</span></span> <span data-ttu-id="8720f-188">Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, aber Protokolle können aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="8720f-188">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="8720f-189">Wenn `true`, werden ausführliche Ausnahme Meldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="8720f-189">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="8720f-190">Der Standardwert ist `false`, da diese Ausnahme Meldungen vertrauliche Informationen enthalten können.</span><span class="sxs-lookup"><span data-stu-id="8720f-190">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

<span data-ttu-id="8720f-191">Optionen können für alle Hubs konfiguriert werden, indem ein Options Delegat für den `AddSignalR`-Aufruf in `Startup.ConfigureServices` bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="8720f-191">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="8720f-192">Optionen für einen einzelnen Hub überschreiben die globalen Optionen, die in `AddSignalR` bereitgestellt werden, und können mithilfe von <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="8720f-192">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="8720f-193">Erweiterte http-Konfigurationsoptionen</span><span class="sxs-lookup"><span data-stu-id="8720f-193">Advanced HTTP configuration options</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8720f-194">Verwenden Sie `HttpConnectionDispatcherOptions`, um erweiterte Einstellungen im Zusammenhang mit Transporte und der Speicherpuffer Verwaltung zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="8720f-194">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="8720f-195">Diese Optionen werden konfiguriert, indem ein [Delegat an maphub @ no__t-1T >](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure` übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="8720f-195">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="8720f-196">Verwenden Sie `HttpConnectionDispatcherOptions`, um erweiterte Einstellungen im Zusammenhang mit Transporte und der Speicherpuffer Verwaltung zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="8720f-196">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="8720f-197">Diese Optionen werden konfiguriert, indem ein [Delegat an maphub @ no__t-1T >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure` übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="8720f-197">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="8720f-198">In der folgenden Tabelle werden die Optionen zum Konfigurieren der erweiterten http-Optionen von ASP.net Core signalr beschrieben:</span><span class="sxs-lookup"><span data-stu-id="8720f-198">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="8720f-199">Option</span><span class="sxs-lookup"><span data-stu-id="8720f-199">Option</span></span> | <span data-ttu-id="8720f-200">Standardwert</span><span class="sxs-lookup"><span data-stu-id="8720f-200">Default Value</span></span> | <span data-ttu-id="8720f-201">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="8720f-201">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="8720f-202">32 KB</span><span class="sxs-lookup"><span data-stu-id="8720f-202">32 KB</span></span> | <span data-ttu-id="8720f-203">Die maximale Anzahl von Bytes, die vom Client vor dem Anwenden des backdrucks vom Server gepuffert werden.</span><span class="sxs-lookup"><span data-stu-id="8720f-203">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="8720f-204">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten schneller empfangen, ohne dass ein Rückdruck angewendet wird, der Arbeitsspeicher Verbrauch kann jedoch gesteigert werden.</span><span class="sxs-lookup"><span data-stu-id="8720f-204">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="8720f-205">Automatisch aus den `Authorize`-Attributen gesammelte Daten, die auf die Hub-Klasse angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="8720f-205">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="8720f-206">Eine Liste von [iautorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) -Objekten, die verwendet werden, um zu bestimmen, ob ein Client für die Verbindung mit dem Hub autorisiert ist.</span><span class="sxs-lookup"><span data-stu-id="8720f-206">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="8720f-207">32 KB</span><span class="sxs-lookup"><span data-stu-id="8720f-207">32 KB</span></span> | <span data-ttu-id="8720f-208">Die maximale Anzahl von Bytes, die von der APP gesendet werden, die der Server puffert, bevor der Rückdruck beobachtet wird</span><span class="sxs-lookup"><span data-stu-id="8720f-208">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="8720f-209">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten schneller Puffern, ohne dass auf den Rückstand gewartet wird, aber die Arbeitsspeicher Nutzung erhöhen kann.</span><span class="sxs-lookup"><span data-stu-id="8720f-209">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="8720f-210">Alle Transporte sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="8720f-210">All Transports are enabled.</span></span> | <span data-ttu-id="8720f-211">Ein Bitflags-Enumeration von `HttpTransportType`-Werten, mit denen die Transporte eingeschränkt werden können, die ein Client für die Verbindung verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="8720f-211">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="8720f-212">Siehe weiter unten.</span><span class="sxs-lookup"><span data-stu-id="8720f-212">See below.</span></span> | <span data-ttu-id="8720f-213">Zusätzliche Optionen, die für den langen Abruf Transport spezifisch sind.</span><span class="sxs-lookup"><span data-stu-id="8720f-213">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="8720f-214">Siehe weiter unten.</span><span class="sxs-lookup"><span data-stu-id="8720f-214">See below.</span></span> | <span data-ttu-id="8720f-215">Zusätzliche Optionen für den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="8720f-215">Additional options specific to the WebSockets transport.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| <span data-ttu-id="8720f-216">Option</span><span class="sxs-lookup"><span data-stu-id="8720f-216">Option</span></span> | <span data-ttu-id="8720f-217">Standardwert</span><span class="sxs-lookup"><span data-stu-id="8720f-217">Default Value</span></span> | <span data-ttu-id="8720f-218">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="8720f-218">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="8720f-219">32 KB</span><span class="sxs-lookup"><span data-stu-id="8720f-219">32 KB</span></span> | <span data-ttu-id="8720f-220">Die maximale Anzahl von Bytes, die vom Client empfangen werden, der vom Server gepuffert wird.</span><span class="sxs-lookup"><span data-stu-id="8720f-220">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="8720f-221">Das Erhöhen dieses Werts ermöglicht dem Server, größere Nachrichten zu empfangen, kann sich jedoch negativ auf die Arbeitsspeicher Auslastung auswirken.</span><span class="sxs-lookup"><span data-stu-id="8720f-221">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="8720f-222">Automatisch aus den `Authorize`-Attributen gesammelte Daten, die auf die Hub-Klasse angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="8720f-222">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="8720f-223">Eine Liste von [iautorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) -Objekten, die verwendet werden, um zu bestimmen, ob ein Client für die Verbindung mit dem Hub autorisiert ist.</span><span class="sxs-lookup"><span data-stu-id="8720f-223">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="8720f-224">32 KB</span><span class="sxs-lookup"><span data-stu-id="8720f-224">32 KB</span></span> | <span data-ttu-id="8720f-225">Die maximale Anzahl von Bytes, die von der APP gesendet werden, die der Server puffert.</span><span class="sxs-lookup"><span data-stu-id="8720f-225">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="8720f-226">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten senden, kann sich jedoch negativ auf die Arbeitsspeicher Auslastung auswirken.</span><span class="sxs-lookup"><span data-stu-id="8720f-226">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="8720f-227">Alle Transporte sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="8720f-227">All Transports are enabled.</span></span> | <span data-ttu-id="8720f-228">Ein Bitflags-Enumeration von `HttpTransportType`-Werten, mit denen die Transporte eingeschränkt werden können, die ein Client für die Verbindung verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="8720f-228">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="8720f-229">Siehe weiter unten.</span><span class="sxs-lookup"><span data-stu-id="8720f-229">See below.</span></span> | <span data-ttu-id="8720f-230">Zusätzliche Optionen, die für den langen Abruf Transport spezifisch sind.</span><span class="sxs-lookup"><span data-stu-id="8720f-230">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="8720f-231">Siehe weiter unten.</span><span class="sxs-lookup"><span data-stu-id="8720f-231">See below.</span></span> | <span data-ttu-id="8720f-232">Zusätzliche Optionen für den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="8720f-232">Additional options specific to the WebSockets transport.</span></span> |

::: moniker-end

<span data-ttu-id="8720f-233">Der lange Abruf Transport verfügt über zusätzliche Optionen, die mit der `LongPolling`-Eigenschaft konfiguriert werden können:</span><span class="sxs-lookup"><span data-stu-id="8720f-233">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="8720f-234">Option</span><span class="sxs-lookup"><span data-stu-id="8720f-234">Option</span></span> | <span data-ttu-id="8720f-235">Standardwert</span><span class="sxs-lookup"><span data-stu-id="8720f-235">Default Value</span></span> | <span data-ttu-id="8720f-236">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="8720f-236">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="8720f-237">90 Sekunden</span><span class="sxs-lookup"><span data-stu-id="8720f-237">90 seconds</span></span> | <span data-ttu-id="8720f-238">Die maximale Zeitspanne, die der Server wartet, bis eine Nachricht an den Client gesendet wird, bevor eine einzelne Abruf Anforderung beendet wird.</span><span class="sxs-lookup"><span data-stu-id="8720f-238">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="8720f-239">Das verringern dieses Werts bewirkt, dass der Client neue Abruf Anforderungen häufiger ausgibt.</span><span class="sxs-lookup"><span data-stu-id="8720f-239">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="8720f-240">Der WebSocket-Transport verfügt über zusätzliche Optionen, die mit der `WebSockets`-Eigenschaft konfiguriert werden können:</span><span class="sxs-lookup"><span data-stu-id="8720f-240">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="8720f-241">Option</span><span class="sxs-lookup"><span data-stu-id="8720f-241">Option</span></span> | <span data-ttu-id="8720f-242">Standardwert</span><span class="sxs-lookup"><span data-stu-id="8720f-242">Default Value</span></span> | <span data-ttu-id="8720f-243">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="8720f-243">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="8720f-244">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="8720f-244">5 seconds</span></span> | <span data-ttu-id="8720f-245">Wenn der Client nach dem Schließen des Servers nicht innerhalb dieses Zeitraums geschlossen werden kann, wird die Verbindung beendet.</span><span class="sxs-lookup"><span data-stu-id="8720f-245">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="8720f-246">Ein Delegat, der verwendet werden kann, um den `Sec-WebSocket-Protocol`-Header auf einen benutzerdefinierten Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="8720f-246">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="8720f-247">Der Delegat empfängt die vom Client angeforderten Werte als Eingabe und erwartet, dass der gewünschte Wert zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="8720f-247">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="8720f-248">Konfigurieren von Client Optionen</span><span class="sxs-lookup"><span data-stu-id="8720f-248">Configure client options</span></span>

<span data-ttu-id="8720f-249">Client Optionen können für den Typ "`HubConnectionBuilder`" konfiguriert werden (verfügbar in den .net-und JavaScript-Clients).</span><span class="sxs-lookup"><span data-stu-id="8720f-249">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="8720f-250">Sie steht auch im Java-Client zur Verfügung, aber die `HttpHubConnectionBuilder`-Unterklasse enthält die Generator-Konfigurationsoptionen sowie die `HubConnection` selbst.</span><span class="sxs-lookup"><span data-stu-id="8720f-250">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="8720f-251">Konfigurieren der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="8720f-251">Configure logging</span></span>

<span data-ttu-id="8720f-252">Die Protokollierung wird mit der `ConfigureLogging`-Methode im .NET-Client konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="8720f-252">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="8720f-253">Protokollierungs Anbieter und Filter können auf die gleiche Weise wie auf dem Server registriert werden.</span><span class="sxs-lookup"><span data-stu-id="8720f-253">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="8720f-254">Weitere Informationen finden Sie in der Dokumentation zur [Protokollierung ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="8720f-254">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="8720f-255">Um Protokollierungs Anbieter zu registrieren, müssen Sie die erforderlichen Pakete installieren.</span><span class="sxs-lookup"><span data-stu-id="8720f-255">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="8720f-256">Eine vollständige Liste finden Sie im Abschnitt [integrierte Protokollierungs Anbieter](xref:fundamentals/logging/index#built-in-logging-providers) der Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="8720f-256">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="8720f-257">Um beispielsweise die Konsolen Protokollierung zu aktivieren, installieren Sie das nuget-Paket "`Microsoft.Extensions.Logging.Console`".</span><span class="sxs-lookup"><span data-stu-id="8720f-257">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="8720f-258">Nennen Sie die `AddConsole`-Erweiterungsmethode:</span><span class="sxs-lookup"><span data-stu-id="8720f-258">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="8720f-259">Im JavaScript-Client ist eine ähnliche `configureLogging`-Methode vorhanden.</span><span class="sxs-lookup"><span data-stu-id="8720f-259">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="8720f-260">Geben Sie einen `LogLevel`-Wert an, der die minimale Ebene der zu erstellenden Protokollmeldungen angibt.</span><span class="sxs-lookup"><span data-stu-id="8720f-260">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="8720f-261">Protokolle werden in das Browser Konsolenfenster geschrieben.</span><span class="sxs-lookup"><span data-stu-id="8720f-261">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8720f-262">Anstelle eines `LogLevel`-Werts können Sie auch einen `string`-Wert bereitstellen, der den Namen der Protokollebene darstellt.</span><span class="sxs-lookup"><span data-stu-id="8720f-262">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="8720f-263">Dies ist hilfreich beim Konfigurieren der signalr-Protokollierung in Umgebungen, in denen Sie keinen Zugriff auf die `LogLevel`-Konstanten haben.</span><span class="sxs-lookup"><span data-stu-id="8720f-263">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="8720f-264">In der folgenden Tabelle sind die verfügbaren Protokoll Ebenen aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="8720f-264">The following table lists the available log levels.</span></span> <span data-ttu-id="8720f-265">Der Wert, den Sie für `configureLogging` angeben, legt den **minimalen** Protokolliergrad fest, der protokolliert wird.</span><span class="sxs-lookup"><span data-stu-id="8720f-265">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="8720f-266">Nachrichten, die auf dieser Ebene protokolliert werden, **oder die nach der Tabelle aufgeführten Ebenen**werden protokolliert.</span><span class="sxs-lookup"><span data-stu-id="8720f-266">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="8720f-267">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="8720f-267">String</span></span>                      | <span data-ttu-id="8720f-268">LogLevel</span><span class="sxs-lookup"><span data-stu-id="8720f-268">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="8720f-269">`info` **oder** `information`</span><span class="sxs-lookup"><span data-stu-id="8720f-269">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="8720f-270">`warn` **oder** `warning`</span><span class="sxs-lookup"><span data-stu-id="8720f-270">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

::: moniker-end

> [!NOTE]
> <span data-ttu-id="8720f-271">Um die Protokollierung vollständig zu deaktivieren, geben Sie `signalR.LogLevel.None` in der `configureLogging`-Methode an.</span><span class="sxs-lookup"><span data-stu-id="8720f-271">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="8720f-272">Weitere Informationen zur Protokollierung finden Sie in der [signalr-Diagnose Dokumentation](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="8720f-272">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="8720f-273">Der signalr-Java-Client verwendet die [SLF4J](https://www.slf4j.org/) -Bibliothek für die Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="8720f-273">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="8720f-274">Dabei handelt es sich um eine allgemeine Protokollierungs-API, die es Benutzern der Bibliothek ermöglicht, ihre eigene spezifische Protokollierungs Implementierung zu wählen, indem Sie eine bestimmte Protokollierungs Abhängigkeit einbinden.</span><span class="sxs-lookup"><span data-stu-id="8720f-274">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="8720f-275">Der folgende Code Ausschnitt zeigt, wie `java.util.logging` mit dem signalr-Java-Client verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="8720f-275">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="8720f-276">Wenn Sie die Protokollierung in ihren Abhängigkeiten nicht konfigurieren, lädt SLF4J eine Standard Protokollierung ohne Vorgang mit folgender Warnmeldung:</span><span class="sxs-lookup"><span data-stu-id="8720f-276">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="8720f-277">Dies kann sicher ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="8720f-277">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="8720f-278">Zulässige Transporte konfigurieren</span><span class="sxs-lookup"><span data-stu-id="8720f-278">Configure allowed transports</span></span>

<span data-ttu-id="8720f-279">Die von signalr verwendeten Transporte können im `WithUrl`-Aufruf(`withUrl` in JavaScript) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="8720f-279">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="8720f-280">Eine bitweise OR-Angabe der Werte `HttpTransportType` kann verwendet werden, um den Client so einzuschränken, dass nur die angegebenen Transporte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="8720f-280">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="8720f-281">Alle Transporte sind standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="8720f-281">All transports are enabled by default.</span></span>

<span data-ttu-id="8720f-282">So können Sie beispielsweise den Transport von Server gesendeten Ereignissen deaktivieren, aber websockets und lange Abruf Verbindungen zulassen:</span><span class="sxs-lookup"><span data-stu-id="8720f-282">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="8720f-283">Im JavaScript-Client werden Transporte durch Festlegen des Felds `transport` für das Options-Objekt, das für `withUrl` bereitgestellt wird, konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="8720f-283">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="8720f-284">In dieser Version des Java-Clients ist websockets der einzige verfügbare Transport.</span><span class="sxs-lookup"><span data-stu-id="8720f-284">In this version of the Java client websockets is the only available transport.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="8720f-285">Im Java-Client wird der Transport mit der `withTransport`-Methode auf `HttpHubConnectionBuilder` ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="8720f-285">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="8720f-286">Der Java-Client verwendet standardmäßig den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="8720f-286">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="8720f-287">Der signalr-Java-Client unterstützt noch keinen Transport Fall Back.</span><span class="sxs-lookup"><span data-stu-id="8720f-287">The SignalR Java client doesn't support transport fallback yet.</span></span>

::: moniker-end

### <a name="configure-bearer-authentication"></a><span data-ttu-id="8720f-288">Konfigurieren der bearerauthentifizierung</span><span class="sxs-lookup"><span data-stu-id="8720f-288">Configure bearer authentication</span></span>

<span data-ttu-id="8720f-289">Um Authentifizierungsdaten zusammen mit signalr-Anforderungen bereitzustellen, verwenden Sie die Option `AccessTokenProvider` (`accessTokenFactory` in JavaScript), um eine Funktion anzugeben, die das gewünschte Zugriffs Token zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="8720f-289">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="8720f-290">Im .NET-Client wird dieses Zugriffs Token als http-Token für die bearerauthentifizierung (mit dem `Authorization`-Header mit dem Typ `Bearer`) übergebenen.</span><span class="sxs-lookup"><span data-stu-id="8720f-290">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="8720f-291">Im JavaScript-Client wird das Zugriffs Token als bearertoken verwendet, **außer** in einigen Fällen, in denen Browser-APIs die Fähigkeit zum Anwenden von Headern einschränken (insbesondere bei vom Server gesendeten Ereignissen und websockets-Anforderungen).</span><span class="sxs-lookup"><span data-stu-id="8720f-291">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="8720f-292">In diesen Fällen wird das Zugriffs Token als Abfrage Zeichenfolgen-Wert `access_token` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="8720f-292">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="8720f-293">Im .NET-Client kann die Option "`AccessTokenProvider`" mithilfe des Options Delegaten in `WithUrl` angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="8720f-293">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="8720f-294">Im JavaScript-Client wird das Zugriffs Token konfiguriert, indem das Feld "`accessTokenFactory`" für das Options-Objekt in `withUrl` festgelegt wird:</span><span class="sxs-lookup"><span data-stu-id="8720f-294">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="8720f-295">Im signalr-Java-Client können Sie ein bearertoken für die Authentifizierung konfigurieren, indem Sie eine zugriffstokenfactory für " [httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)" bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="8720f-295">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="8720f-296">Verwenden Sie [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__), um einen [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html) bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="8720f-296">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="8720f-297">Beim Aufrufen von " [Single.](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)Write" können Sie Logik schreiben, um Zugriffs Token für den Client zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="8720f-297">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="8720f-298">Konfigurieren von Timeout-und Keep-Alive-Optionen</span><span class="sxs-lookup"><span data-stu-id="8720f-298">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="8720f-299">Zusätzliche Optionen zum Konfigurieren von Timeout und Keep-Alive-Verhalten sind für das Objekt "`HubConnection`" verfügbar:</span><span class="sxs-lookup"><span data-stu-id="8720f-299">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>


::: moniker range=">= aspnetcore-2.2"

# <a name="nettabdotnet"></a>[<span data-ttu-id="8720f-300">.NET</span><span class="sxs-lookup"><span data-stu-id="8720f-300">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="8720f-301">Option</span><span class="sxs-lookup"><span data-stu-id="8720f-301">Option</span></span> | <span data-ttu-id="8720f-302">Standardwert</span><span class="sxs-lookup"><span data-stu-id="8720f-302">Default value</span></span> | <span data-ttu-id="8720f-303">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="8720f-303">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="8720f-304">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="8720f-304">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="8720f-305">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="8720f-305">Timeout for server activity.</span></span> <span data-ttu-id="8720f-306">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `Closed`-Ereignis aus (`onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="8720f-306">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="8720f-307">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="8720f-307">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="8720f-308">Der empfohlene Wert ist eine Zahl, die mindestens den `KeepAliveInterval`-Wert des Servers verdoppelt, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="8720f-308">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="8720f-309">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="8720f-309">15 seconds</span></span> | <span data-ttu-id="8720f-310">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="8720f-310">Timeout for initial server handshake.</span></span> <span data-ttu-id="8720f-311">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `Closed`-Ereignis aus (`onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="8720f-311">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="8720f-312">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="8720f-312">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="8720f-313">Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="8720f-313">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="8720f-314">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="8720f-314">15 seconds</span></span> | <span data-ttu-id="8720f-315">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="8720f-315">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="8720f-316">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="8720f-316">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="8720f-317">Wenn der Client keine Nachricht in der auf dem Server festgelegten `ClientTimeoutInterval` gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="8720f-317">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="8720f-318">Im .NET-Client werden Timeout Werte als `TimeSpan`-Werte angegeben.</span><span class="sxs-lookup"><span data-stu-id="8720f-318">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="8720f-319">JavaScript</span><span class="sxs-lookup"><span data-stu-id="8720f-319">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="8720f-320">Option</span><span class="sxs-lookup"><span data-stu-id="8720f-320">Option</span></span> | <span data-ttu-id="8720f-321">Standardwert</span><span class="sxs-lookup"><span data-stu-id="8720f-321">Default value</span></span> | <span data-ttu-id="8720f-322">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="8720f-322">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="8720f-323">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="8720f-323">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="8720f-324">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="8720f-324">Timeout for server activity.</span></span> <span data-ttu-id="8720f-325">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `onclose`-Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="8720f-325">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="8720f-326">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="8720f-326">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="8720f-327">Der empfohlene Wert ist eine Zahl, die mindestens den `KeepAliveInterval`-Wert des Servers verdoppelt, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="8720f-327">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="8720f-328">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="8720f-328">15 seconds (15,000 miliseconds)</span></span> | <span data-ttu-id="8720f-329">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="8720f-329">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="8720f-330">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="8720f-330">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="8720f-331">Wenn der Client keine Nachricht in der auf dem Server festgelegten `ClientTimeoutInterval` gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="8720f-331">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="8720f-332">Java</span><span class="sxs-lookup"><span data-stu-id="8720f-332">Java</span></span>](#tab/java)

| <span data-ttu-id="8720f-333">Option</span><span class="sxs-lookup"><span data-stu-id="8720f-333">Option</span></span> | <span data-ttu-id="8720f-334">Standardwert</span><span class="sxs-lookup"><span data-stu-id="8720f-334">Default value</span></span> | <span data-ttu-id="8720f-335">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="8720f-335">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="8720f-336">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="8720f-336">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="8720f-337">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="8720f-337">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="8720f-338">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="8720f-338">Timeout for server activity.</span></span> <span data-ttu-id="8720f-339">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `onClose`-Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="8720f-339">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="8720f-340">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="8720f-340">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="8720f-341">Der empfohlene Wert ist eine Zahl, die mindestens den `KeepAliveInterval`-Wert des Servers verdoppelt, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="8720f-341">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="8720f-342">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="8720f-342">15 seconds</span></span> | <span data-ttu-id="8720f-343">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="8720f-343">Timeout for initial server handshake.</span></span> <span data-ttu-id="8720f-344">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `onClose`-Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="8720f-344">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="8720f-345">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="8720f-345">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="8720f-346">Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="8720f-346">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="8720f-347">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="8720f-347">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="8720f-348">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="8720f-348">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="8720f-349">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="8720f-349">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="8720f-350">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="8720f-350">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="8720f-351">Wenn der Client keine Nachricht in der auf dem Server festgelegten `ClientTimeoutInterval` gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="8720f-351">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="nettabdotnet"></a>[<span data-ttu-id="8720f-352">.NET</span><span class="sxs-lookup"><span data-stu-id="8720f-352">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="8720f-353">Option</span><span class="sxs-lookup"><span data-stu-id="8720f-353">Option</span></span> | <span data-ttu-id="8720f-354">Standardwert</span><span class="sxs-lookup"><span data-stu-id="8720f-354">Default value</span></span> | <span data-ttu-id="8720f-355">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="8720f-355">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="8720f-356">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="8720f-356">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="8720f-357">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="8720f-357">Timeout for server activity.</span></span> <span data-ttu-id="8720f-358">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `Closed`-Ereignis aus (`onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="8720f-358">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="8720f-359">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="8720f-359">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="8720f-360">Der empfohlene Wert ist eine Zahl, die mindestens den `KeepAliveInterval`-Wert des Servers verdoppelt, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="8720f-360">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="8720f-361">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="8720f-361">15 seconds</span></span> | <span data-ttu-id="8720f-362">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="8720f-362">Timeout for initial server handshake.</span></span> <span data-ttu-id="8720f-363">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `Closed`-Ereignis aus (`onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="8720f-363">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="8720f-364">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="8720f-364">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="8720f-365">Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="8720f-365">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="8720f-366">Im .NET-Client werden Timeout Werte als `TimeSpan`-Werte angegeben.</span><span class="sxs-lookup"><span data-stu-id="8720f-366">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="8720f-367">JavaScript</span><span class="sxs-lookup"><span data-stu-id="8720f-367">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="8720f-368">Option</span><span class="sxs-lookup"><span data-stu-id="8720f-368">Option</span></span> | <span data-ttu-id="8720f-369">Standardwert</span><span class="sxs-lookup"><span data-stu-id="8720f-369">Default value</span></span> | <span data-ttu-id="8720f-370">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="8720f-370">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="8720f-371">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="8720f-371">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="8720f-372">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="8720f-372">Timeout for server activity.</span></span> <span data-ttu-id="8720f-373">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `onclose`-Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="8720f-373">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="8720f-374">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="8720f-374">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="8720f-375">Der empfohlene Wert ist eine Zahl, die mindestens den `KeepAliveInterval`-Wert des Servers verdoppelt, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="8720f-375">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="8720f-376">Java</span><span class="sxs-lookup"><span data-stu-id="8720f-376">Java</span></span>](#tab/java)

| <span data-ttu-id="8720f-377">Option</span><span class="sxs-lookup"><span data-stu-id="8720f-377">Option</span></span> | <span data-ttu-id="8720f-378">Standardwert</span><span class="sxs-lookup"><span data-stu-id="8720f-378">Default value</span></span> | <span data-ttu-id="8720f-379">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="8720f-379">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="8720f-380">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="8720f-380">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="8720f-381">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="8720f-381">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="8720f-382">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="8720f-382">Timeout for server activity.</span></span> <span data-ttu-id="8720f-383">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `onClose`-Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="8720f-383">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="8720f-384">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="8720f-384">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="8720f-385">Der empfohlene Wert ist eine Zahl, die mindestens dem `KeepAliveInterval`-Wert des Servers entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="8720f-385">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="8720f-386">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="8720f-386">15 seconds</span></span> | <span data-ttu-id="8720f-387">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="8720f-387">Timeout for initial server handshake.</span></span> <span data-ttu-id="8720f-388">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `onClose`-Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="8720f-388">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="8720f-389">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="8720f-389">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="8720f-390">Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="8720f-390">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

::: moniker-end

---

### <a name="configure-additional-options"></a><span data-ttu-id="8720f-391">Zusätzliche Optionen konfigurieren</span><span class="sxs-lookup"><span data-stu-id="8720f-391">Configure additional options</span></span>

<span data-ttu-id="8720f-392">Zusätzliche Optionen können in der `WithUrl`-Methode (`withUrl` in JavaScript) auf `HubConnectionBuilder` oder auf den verschiedenen Konfigurations-APIs auf dem `HttpHubConnectionBuilder` im Java-Client konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="8720f-392">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="8720f-393">.NET</span><span class="sxs-lookup"><span data-stu-id="8720f-393">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="8720f-394">.Net-Option</span><span class="sxs-lookup"><span data-stu-id="8720f-394">.NET Option</span></span> |  <span data-ttu-id="8720f-395">Standardwert</span><span class="sxs-lookup"><span data-stu-id="8720f-395">Default value</span></span> | <span data-ttu-id="8720f-396">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="8720f-396">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="8720f-397">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="8720f-397">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="8720f-398">Legen Sie dies auf `true` fest, um den Aushandlungs Schritt zu überspringen.</span><span class="sxs-lookup"><span data-stu-id="8720f-398">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="8720f-399">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="8720f-399">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="8720f-400">Diese Einstellung kann nicht aktiviert werden, wenn der Azure signalr-Dienst verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="8720f-400">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="8720f-401">Empty</span><span class="sxs-lookup"><span data-stu-id="8720f-401">Empty</span></span> | <span data-ttu-id="8720f-402">Eine Auflistung von TLS-Zertifikaten, die zum Authentifizieren von Anforderungen gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="8720f-402">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="8720f-403">Empty</span><span class="sxs-lookup"><span data-stu-id="8720f-403">Empty</span></span> | <span data-ttu-id="8720f-404">Eine Auflistung von HTTP-Cookies, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="8720f-404">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="8720f-405">Empty</span><span class="sxs-lookup"><span data-stu-id="8720f-405">Empty</span></span> | <span data-ttu-id="8720f-406">Anmelde Informationen, die mit jeder HTTP-Anforderung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="8720f-406">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="8720f-407">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="8720f-407">5 seconds</span></span> | <span data-ttu-id="8720f-408">Nur websockets.</span><span class="sxs-lookup"><span data-stu-id="8720f-408">WebSockets only.</span></span> <span data-ttu-id="8720f-409">Die maximale Zeitspanne, die der Client nach dem Schließen des Servers wartet, um die Anforderung zum Schließen zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="8720f-409">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="8720f-410">Wenn der Server die Schließung innerhalb dieses Zeitraums nicht anerkennt, trennt der Client die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="8720f-410">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="8720f-411">Empty</span><span class="sxs-lookup"><span data-stu-id="8720f-411">Empty</span></span> | <span data-ttu-id="8720f-412">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="8720f-412">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="8720f-413">Ein Delegat, der verwendet werden kann, um die zum Senden von HTTP-Anforderungen verwendete `HttpMessageHandler` zu konfigurieren oder zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="8720f-413">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="8720f-414">Wird nicht für WebSocket-Verbindungen verwendet.</span><span class="sxs-lookup"><span data-stu-id="8720f-414">Not used for WebSocket connections.</span></span> <span data-ttu-id="8720f-415">Dieser Delegat muss einen Wert zurückgeben, der nicht NULL ist, und erhält den Standardwert als Parameter.</span><span class="sxs-lookup"><span data-stu-id="8720f-415">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="8720f-416">Ändern Sie die Einstellungen für diesen Standardwert, und geben Sie ihn zurück, oder geben Sie eine neue Instanz `HttpMessageHandler` zurück.</span><span class="sxs-lookup"><span data-stu-id="8720f-416">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="8720f-417">**Wenn Sie den Handler ersetzen, stellen Sie sicher, dass die Einstellungen, die Sie beibehalten möchten, vom bereitgestellten Handler kopiert werden. Andernfalls gelten die konfigurierten Optionen (z. b. Cookies und Header) nicht für den neuen Handler.**</span><span class="sxs-lookup"><span data-stu-id="8720f-417">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="8720f-418">Ein HTTP-Proxy, der beim Senden von HTTP-Anforderungen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="8720f-418">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="8720f-419">Legen Sie diesen booleschen Wert fest, um die Standard Anmelde Informationen für http-und websockets-Anforderungen zu senden</span><span class="sxs-lookup"><span data-stu-id="8720f-419">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="8720f-420">Dies ermöglicht die Verwendung der Windows-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="8720f-420">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="8720f-421">Ein Delegat, der zum Konfigurieren zusätzlicher WebSocket-Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="8720f-421">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="8720f-422">Empfängt eine Instanz von [clientwebsocketoptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , die zum Konfigurieren der Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="8720f-422">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="8720f-423">JavaScript</span><span class="sxs-lookup"><span data-stu-id="8720f-423">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="8720f-424">JavaScript-Option</span><span class="sxs-lookup"><span data-stu-id="8720f-424">JavaScript Option</span></span> | <span data-ttu-id="8720f-425">Standardwert</span><span class="sxs-lookup"><span data-stu-id="8720f-425">Default Value</span></span> | <span data-ttu-id="8720f-426">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="8720f-426">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="8720f-427">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="8720f-427">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="8720f-428">Legen Sie dies auf `true` fest, um den Aushandlungs Schritt zu überspringen.</span><span class="sxs-lookup"><span data-stu-id="8720f-428">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="8720f-429">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="8720f-429">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="8720f-430">Diese Einstellung kann nicht aktiviert werden, wenn der Azure signalr-Dienst verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="8720f-430">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="8720f-431">Java</span><span class="sxs-lookup"><span data-stu-id="8720f-431">Java</span></span>](#tab/java)

| <span data-ttu-id="8720f-432">Java-Option</span><span class="sxs-lookup"><span data-stu-id="8720f-432">Java Option</span></span> | <span data-ttu-id="8720f-433">Standardwert</span><span class="sxs-lookup"><span data-stu-id="8720f-433">Default Value</span></span> | <span data-ttu-id="8720f-434">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="8720f-434">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="8720f-435">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="8720f-435">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="8720f-436">Legen Sie dies auf `true` fest, um den Aushandlungs Schritt zu überspringen.</span><span class="sxs-lookup"><span data-stu-id="8720f-436">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="8720f-437">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="8720f-437">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="8720f-438">Diese Einstellung kann nicht aktiviert werden, wenn der Azure signalr-Dienst verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="8720f-438">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="8720f-439">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="8720f-439">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="8720f-440">Empty</span><span class="sxs-lookup"><span data-stu-id="8720f-440">Empty</span></span> | <span data-ttu-id="8720f-441">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="8720f-441">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="8720f-442">Im .NET-Client können diese Optionen durch den für `WithUrl` bereitgestellten Options Delegaten geändert werden:</span><span class="sxs-lookup"><span data-stu-id="8720f-442">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="8720f-443">Im JavaScript-Client können diese Optionen in einem JavaScript-Objekt bereitgestellt werden, das für `withUrl` bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="8720f-443">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="8720f-444">Im Java-Client können diese Optionen mit den Methoden auf dem vom `HubConnectionBuilder.create("HUB URL")` zurückgegebenen `HttpHubConnectionBuilder` konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="8720f-444">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="8720f-445">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="8720f-445">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

---
title: ASP.net Core signalr-Konfiguration
author: bradygaster
description: Erfahren Sie, wie Sie ASP.net Core signalr-apps konfigurieren.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 08/05/2019
uid: signalr/configuration
ms.openlocfilehash: 156ffac83fbdf61fd88ad8acc307c2c701c46bca
ms.sourcegitcommit: f65d8765e4b7c894481db9b37aa6969abc625a48
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773930"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="47817-103">ASP.net Core signalr-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="47817-103">ASP.NET Core SignalR configuration</span></span>

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="47817-104">JSON/messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="47817-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="47817-105">ASP.net Core signalr unterstützt zwei Protokolle für das Codieren von Nachrichten: [JSON](https://www.json.org/) und [messagepack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="47817-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="47817-106">Jedes Protokoll verfügt über Konfigurationsoptionen für die Serialisierung.</span><span class="sxs-lookup"><span data-stu-id="47817-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="47817-107">Die JSON-Serialisierung kann mithilfe der [addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) -Erweiterungsmethode auf dem Server konfiguriert werden, die nach [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices` der-Methode hinzugefügt werden kann.</span><span class="sxs-lookup"><span data-stu-id="47817-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="47817-108">Die `AddJsonProtocol` -Methode nimmt einen Delegaten an `options` , der ein-Objekt empfängt.</span><span class="sxs-lookup"><span data-stu-id="47817-108">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="47817-109">Die [payloadserializersettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) -Eigenschaft für dieses Objekt ist ein `JsonSerializerSettings` JSON.NET-Objekt, das zum Konfigurieren der Serialisierung von Argumenten und Rückgabe Werten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="47817-109">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="47817-110">Weitere Informationen finden Sie in der [JSON.NET-Dokumentation](https://www.newtonsoft.com/json/help/html/Introduction.htm) .</span><span class="sxs-lookup"><span data-stu-id="47817-110">See the [JSON.NET Documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm) for more details.</span></span>

<span data-ttu-id="47817-111">Verwenden Sie beispielsweise den folgenden Code, um das Serialisierungsprogramm so zu konfigurieren, dass "PascalCase"-Eigenschaftsnamen anstelle der Standardnamen "CamelCase" verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="47817-111">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
        new DefaultContractResolver();
    });
```

<span data-ttu-id="47817-112">Im .NET-Client ist dieselbe `AddJsonProtocol` Erweiterungsmethode auf [hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)vorhanden.</span><span class="sxs-lookup"><span data-stu-id="47817-112">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="47817-113">Der `Microsoft.Extensions.DependencyInjection` Namespace muss importiert werden, um die Erweiterungsmethode aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="47817-113">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="47817-114">Zurzeit ist es nicht möglich, JSON-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="47817-114">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="47817-115">Messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="47817-115">MessagePack serialization options</span></span>

<span data-ttu-id="47817-116">Die messagepack-Serialisierung kann konfiguriert werden, indem ein Delegat für den [addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) -Aufruf bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="47817-116">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="47817-117">Weitere Informationen finden Sie [unter messagepack in signalr](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="47817-117">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="47817-118">Zurzeit ist es nicht möglich, die messagepack-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="47817-118">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="47817-119">Konfigurieren von Serveroptionen</span><span class="sxs-lookup"><span data-stu-id="47817-119">Configure server options</span></span>

<span data-ttu-id="47817-120">In der folgenden Tabelle werden die Optionen zum Konfigurieren von signalr Hubs beschrieben:</span><span class="sxs-lookup"><span data-stu-id="47817-120">The following table describes options for configuring SignalR hubs:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="47817-121">Option</span><span class="sxs-lookup"><span data-stu-id="47817-121">Option</span></span> | <span data-ttu-id="47817-122">Standardwert</span><span class="sxs-lookup"><span data-stu-id="47817-122">Default Value</span></span> | <span data-ttu-id="47817-123">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="47817-123">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="47817-124">30 Sekunden</span><span class="sxs-lookup"><span data-stu-id="47817-124">30 seconds</span></span> | <span data-ttu-id="47817-125">Der Server wird vom-Client als getrennt betrachtet, wenn in diesem Intervall keine Nachricht (einschließlich Keep-Alive) empfangen wurde.</span><span class="sxs-lookup"><span data-stu-id="47817-125">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="47817-126">Es kann länger dauern, bis der Client als getrennt gekennzeichnet ist, weil dies implementiert ist.</span><span class="sxs-lookup"><span data-stu-id="47817-126">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="47817-127">Der empfohlene Wert ist Double `KeepAliveInterval` -Wert.</span><span class="sxs-lookup"><span data-stu-id="47817-127">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="47817-128">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="47817-128">15 seconds</span></span> | <span data-ttu-id="47817-129">Wenn der Client innerhalb dieses Zeitraums keine anfängliche Hand Shake Nachricht sendet, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="47817-129">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="47817-130">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="47817-130">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="47817-131">Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="47817-131">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="47817-132">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="47817-132">15 seconds</span></span> | <span data-ttu-id="47817-133">Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten.</span><span class="sxs-lookup"><span data-stu-id="47817-133">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="47817-134">Ändern Sie `KeepAliveInterval`die `ServerTimeout` Einstellungaufdem`serverTimeoutInMilliseconds` Client, wenn Sie sich ändern. /</span><span class="sxs-lookup"><span data-stu-id="47817-134">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="47817-135">Der empfohlene `ServerTimeout` / Wertist`KeepAliveInterval` Double-Wert. `serverTimeoutInMilliseconds`</span><span class="sxs-lookup"><span data-stu-id="47817-135">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="47817-136">Alle installierten Protokolle</span><span class="sxs-lookup"><span data-stu-id="47817-136">All installed protocols</span></span> | <span data-ttu-id="47817-137">Protokolle, die von diesem Hub unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="47817-137">Protocols supported by this hub.</span></span> <span data-ttu-id="47817-138">Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, aber Protokolle können aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="47817-138">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="47817-139">Wenn `true`der Wert ist, werden ausführliche Ausnahme Meldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="47817-139">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="47817-140">Der Standardwert `false`ist, da diese Ausnahme Meldungen vertrauliche Informationen enthalten können.</span><span class="sxs-lookup"><span data-stu-id="47817-140">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="47817-141">Die maximale Anzahl von Elementen, die für Client-uploadstreams gepuffert werden können.</span><span class="sxs-lookup"><span data-stu-id="47817-141">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="47817-142">Wenn dieser Grenzwert erreicht wird, wird die Verarbeitung von Aufrufen blockiert, bis der Server streamingelemente verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="47817-142">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="47817-143">32 KB</span><span class="sxs-lookup"><span data-stu-id="47817-143">32 KB</span></span> | <span data-ttu-id="47817-144">Maximale Größe einer einzelnen eingehenden Hub-Nachricht.</span><span class="sxs-lookup"><span data-stu-id="47817-144">Maximum size of a single incoming hub message.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.2"

| <span data-ttu-id="47817-145">Option</span><span class="sxs-lookup"><span data-stu-id="47817-145">Option</span></span> | <span data-ttu-id="47817-146">Standardwert</span><span class="sxs-lookup"><span data-stu-id="47817-146">Default Value</span></span> | <span data-ttu-id="47817-147">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="47817-147">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="47817-148">30 Sekunden</span><span class="sxs-lookup"><span data-stu-id="47817-148">30 seconds</span></span> | <span data-ttu-id="47817-149">Der Server wird vom-Client als getrennt betrachtet, wenn in diesem Intervall keine Nachricht (einschließlich Keep-Alive) empfangen wurde.</span><span class="sxs-lookup"><span data-stu-id="47817-149">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="47817-150">Es kann länger dauern, bis der Client als getrennt gekennzeichnet ist, weil dies implementiert ist.</span><span class="sxs-lookup"><span data-stu-id="47817-150">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="47817-151">Der empfohlene Wert ist Double `KeepAliveInterval` -Wert.</span><span class="sxs-lookup"><span data-stu-id="47817-151">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="47817-152">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="47817-152">15 seconds</span></span> | <span data-ttu-id="47817-153">Wenn der Client innerhalb dieses Zeitraums keine anfängliche Hand Shake Nachricht sendet, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="47817-153">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="47817-154">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="47817-154">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="47817-155">Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="47817-155">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="47817-156">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="47817-156">15 seconds</span></span> | <span data-ttu-id="47817-157">Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten.</span><span class="sxs-lookup"><span data-stu-id="47817-157">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="47817-158">Ändern Sie `KeepAliveInterval`die `ServerTimeout` Einstellungaufdem`serverTimeoutInMilliseconds` Client, wenn Sie sich ändern. /</span><span class="sxs-lookup"><span data-stu-id="47817-158">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="47817-159">Der empfohlene `ServerTimeout` / Wertist`KeepAliveInterval` Double-Wert. `serverTimeoutInMilliseconds`</span><span class="sxs-lookup"><span data-stu-id="47817-159">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="47817-160">Alle installierten Protokolle</span><span class="sxs-lookup"><span data-stu-id="47817-160">All installed protocols</span></span> | <span data-ttu-id="47817-161">Protokolle, die von diesem Hub unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="47817-161">Protocols supported by this hub.</span></span> <span data-ttu-id="47817-162">Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, aber Protokolle können aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="47817-162">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="47817-163">Wenn `true`der Wert ist, werden ausführliche Ausnahme Meldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="47817-163">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="47817-164">Der Standardwert `false`ist, da diese Ausnahme Meldungen vertrauliche Informationen enthalten können.</span><span class="sxs-lookup"><span data-stu-id="47817-164">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| <span data-ttu-id="47817-165">Option</span><span class="sxs-lookup"><span data-stu-id="47817-165">Option</span></span> | <span data-ttu-id="47817-166">Standardwert</span><span class="sxs-lookup"><span data-stu-id="47817-166">Default Value</span></span> | <span data-ttu-id="47817-167">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="47817-167">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="47817-168">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="47817-168">15 seconds</span></span> | <span data-ttu-id="47817-169">Wenn der Client innerhalb dieses Zeitraums keine anfängliche Hand Shake Nachricht sendet, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="47817-169">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="47817-170">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="47817-170">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="47817-171">Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="47817-171">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="47817-172">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="47817-172">15 seconds</span></span> | <span data-ttu-id="47817-173">Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten.</span><span class="sxs-lookup"><span data-stu-id="47817-173">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="47817-174">Ändern Sie `KeepAliveInterval`die `ServerTimeout` Einstellungaufdem`serverTimeoutInMilliseconds` Client, wenn Sie sich ändern. /</span><span class="sxs-lookup"><span data-stu-id="47817-174">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="47817-175">Der empfohlene `ServerTimeout` / Wertist`KeepAliveInterval` Double-Wert. `serverTimeoutInMilliseconds`</span><span class="sxs-lookup"><span data-stu-id="47817-175">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="47817-176">Alle installierten Protokolle</span><span class="sxs-lookup"><span data-stu-id="47817-176">All installed protocols</span></span> | <span data-ttu-id="47817-177">Protokolle, die von diesem Hub unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="47817-177">Protocols supported by this hub.</span></span> <span data-ttu-id="47817-178">Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, aber Protokolle können aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="47817-178">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="47817-179">Wenn `true`der Wert ist, werden ausführliche Ausnahme Meldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="47817-179">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="47817-180">Der Standardwert `false`ist, da diese Ausnahme Meldungen vertrauliche Informationen enthalten können.</span><span class="sxs-lookup"><span data-stu-id="47817-180">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

<span data-ttu-id="47817-181">Optionen können für alle Hubs konfiguriert werden, indem ein Options Delegat für `AddSignalR` den Aufruf `Startup.ConfigureServices`in bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="47817-181">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="47817-182">Optionen für einen einzelnen Hub überschreiben die in `AddSignalR` bereitgestellten globalen Optionen und können mithilfe <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>von konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="47817-182">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="47817-183">Erweiterte http-Konfigurationsoptionen</span><span class="sxs-lookup"><span data-stu-id="47817-183">Advanced HTTP configuration options</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="47817-184">Verwenden `HttpConnectionDispatcherOptions` Sie, um erweiterte Einstellungen im Zusammenhang mit Transporte und der Speicherpuffer Verwaltung zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="47817-184">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="47817-185">Diese Optionen werden konfiguriert, indem ein [Delegat an\<maphub T >](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="47817-185">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="47817-186">Verwenden `HttpConnectionDispatcherOptions` Sie, um erweiterte Einstellungen im Zusammenhang mit Transporte und der Speicherpuffer Verwaltung zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="47817-186">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="47817-187">Diese Optionen werden konfiguriert, indem ein [Delegat an\<maphub T >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="47817-187">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="47817-188">In der folgenden Tabelle werden die Optionen zum Konfigurieren der erweiterten http-Optionen von ASP.net Core signalr beschrieben:</span><span class="sxs-lookup"><span data-stu-id="47817-188">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="47817-189">Option</span><span class="sxs-lookup"><span data-stu-id="47817-189">Option</span></span> | <span data-ttu-id="47817-190">Standardwert</span><span class="sxs-lookup"><span data-stu-id="47817-190">Default Value</span></span> | <span data-ttu-id="47817-191">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="47817-191">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="47817-192">32 KB</span><span class="sxs-lookup"><span data-stu-id="47817-192">32 KB</span></span> | <span data-ttu-id="47817-193">Die maximale Anzahl von Bytes, die vom Client vor dem Anwenden des backdrucks vom Server gepuffert werden.</span><span class="sxs-lookup"><span data-stu-id="47817-193">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="47817-194">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten schneller empfangen, ohne dass ein Rückdruck angewendet wird, der Arbeitsspeicher Verbrauch kann jedoch gesteigert werden.</span><span class="sxs-lookup"><span data-stu-id="47817-194">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="47817-195">Daten, die automatisch von `Authorize` den auf die Hub-Klasse angewendeten Attributen gesammelt werden.</span><span class="sxs-lookup"><span data-stu-id="47817-195">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="47817-196">Eine Liste von [iautorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) -Objekten, die verwendet werden, um zu bestimmen, ob ein Client für die Verbindung mit dem Hub autorisiert ist.</span><span class="sxs-lookup"><span data-stu-id="47817-196">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="47817-197">32 KB</span><span class="sxs-lookup"><span data-stu-id="47817-197">32 KB</span></span> | <span data-ttu-id="47817-198">Die maximale Anzahl von Bytes, die von der APP gesendet werden, die der Server puffert, bevor der Rückdruck beobachtet wird</span><span class="sxs-lookup"><span data-stu-id="47817-198">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="47817-199">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten schneller Puffern, ohne dass auf den Rückstand gewartet wird, aber die Arbeitsspeicher Nutzung erhöhen kann.</span><span class="sxs-lookup"><span data-stu-id="47817-199">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="47817-200">Alle Transporte sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="47817-200">All Transports are enabled.</span></span> | <span data-ttu-id="47817-201">Ein Bitflags-Enumeration `HttpTransportType` von Werten, die die Transporte einschränken können, die ein Client für die Verbindungs Herstellung verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="47817-201">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="47817-202">Siehe weiter unten.</span><span class="sxs-lookup"><span data-stu-id="47817-202">See below.</span></span> | <span data-ttu-id="47817-203">Zusätzliche Optionen, die für den langen Abruf Transport spezifisch sind.</span><span class="sxs-lookup"><span data-stu-id="47817-203">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="47817-204">Siehe weiter unten.</span><span class="sxs-lookup"><span data-stu-id="47817-204">See below.</span></span> | <span data-ttu-id="47817-205">Zusätzliche Optionen für den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="47817-205">Additional options specific to the WebSockets transport.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| <span data-ttu-id="47817-206">Option</span><span class="sxs-lookup"><span data-stu-id="47817-206">Option</span></span> | <span data-ttu-id="47817-207">Standardwert</span><span class="sxs-lookup"><span data-stu-id="47817-207">Default Value</span></span> | <span data-ttu-id="47817-208">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="47817-208">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="47817-209">32 KB</span><span class="sxs-lookup"><span data-stu-id="47817-209">32 KB</span></span> | <span data-ttu-id="47817-210">Die maximale Anzahl von Bytes, die vom Client empfangen werden, der vom Server gepuffert wird.</span><span class="sxs-lookup"><span data-stu-id="47817-210">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="47817-211">Das Erhöhen dieses Werts ermöglicht dem Server, größere Nachrichten zu empfangen, kann sich jedoch negativ auf die Arbeitsspeicher Auslastung auswirken.</span><span class="sxs-lookup"><span data-stu-id="47817-211">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="47817-212">Daten, die automatisch von `Authorize` den auf die Hub-Klasse angewendeten Attributen gesammelt werden.</span><span class="sxs-lookup"><span data-stu-id="47817-212">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="47817-213">Eine Liste von [iautorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) -Objekten, die verwendet werden, um zu bestimmen, ob ein Client für die Verbindung mit dem Hub autorisiert ist.</span><span class="sxs-lookup"><span data-stu-id="47817-213">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="47817-214">32 KB</span><span class="sxs-lookup"><span data-stu-id="47817-214">32 KB</span></span> | <span data-ttu-id="47817-215">Die maximale Anzahl von Bytes, die von der APP gesendet werden, die der Server puffert.</span><span class="sxs-lookup"><span data-stu-id="47817-215">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="47817-216">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten senden, kann sich jedoch negativ auf die Arbeitsspeicher Auslastung auswirken.</span><span class="sxs-lookup"><span data-stu-id="47817-216">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="47817-217">Alle Transporte sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="47817-217">All Transports are enabled.</span></span> | <span data-ttu-id="47817-218">Ein Bitflags-Enumeration `HttpTransportType` von Werten, die die Transporte einschränken können, die ein Client für die Verbindungs Herstellung verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="47817-218">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="47817-219">Siehe weiter unten.</span><span class="sxs-lookup"><span data-stu-id="47817-219">See below.</span></span> | <span data-ttu-id="47817-220">Zusätzliche Optionen, die für den langen Abruf Transport spezifisch sind.</span><span class="sxs-lookup"><span data-stu-id="47817-220">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="47817-221">Siehe weiter unten.</span><span class="sxs-lookup"><span data-stu-id="47817-221">See below.</span></span> | <span data-ttu-id="47817-222">Zusätzliche Optionen für den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="47817-222">Additional options specific to the WebSockets transport.</span></span> |

::: moniker-end

<span data-ttu-id="47817-223">Der lange Abruf Transport bietet zusätzliche Optionen, die mit der `LongPolling` -Eigenschaft konfiguriert werden können:</span><span class="sxs-lookup"><span data-stu-id="47817-223">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="47817-224">Option</span><span class="sxs-lookup"><span data-stu-id="47817-224">Option</span></span> | <span data-ttu-id="47817-225">Standardwert</span><span class="sxs-lookup"><span data-stu-id="47817-225">Default Value</span></span> | <span data-ttu-id="47817-226">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="47817-226">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="47817-227">90 Sekunden</span><span class="sxs-lookup"><span data-stu-id="47817-227">90 seconds</span></span> | <span data-ttu-id="47817-228">Die maximale Zeitspanne, die der Server wartet, bis eine Nachricht an den Client gesendet wird, bevor eine einzelne Abruf Anforderung beendet wird.</span><span class="sxs-lookup"><span data-stu-id="47817-228">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="47817-229">Das verringern dieses Werts bewirkt, dass der Client neue Abruf Anforderungen häufiger ausgibt.</span><span class="sxs-lookup"><span data-stu-id="47817-229">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="47817-230">Der WebSocket-Transport verfügt über zusätzliche Optionen, die mit der `WebSockets` -Eigenschaft konfiguriert werden können:</span><span class="sxs-lookup"><span data-stu-id="47817-230">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="47817-231">Option</span><span class="sxs-lookup"><span data-stu-id="47817-231">Option</span></span> | <span data-ttu-id="47817-232">Standardwert</span><span class="sxs-lookup"><span data-stu-id="47817-232">Default Value</span></span> | <span data-ttu-id="47817-233">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="47817-233">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="47817-234">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="47817-234">5 seconds</span></span> | <span data-ttu-id="47817-235">Wenn der Client nach dem Schließen des Servers nicht innerhalb dieses Zeitraums geschlossen werden kann, wird die Verbindung beendet.</span><span class="sxs-lookup"><span data-stu-id="47817-235">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="47817-236">Ein Delegat, der verwendet werden kann, `Sec-WebSocket-Protocol` um den-Header auf einen benutzerdefinierten Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="47817-236">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="47817-237">Der Delegat empfängt die vom Client angeforderten Werte als Eingabe und erwartet, dass der gewünschte Wert zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="47817-237">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="47817-238">Konfigurieren von Client Optionen</span><span class="sxs-lookup"><span data-stu-id="47817-238">Configure client options</span></span>

<span data-ttu-id="47817-239">Client Optionen können für den `HubConnectionBuilder` Typ konfiguriert werden (verfügbar in den .net-und JavaScript-Clients).</span><span class="sxs-lookup"><span data-stu-id="47817-239">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="47817-240">Sie steht auch im Java-Client zur Verfügung, aber `HttpHubConnectionBuilder` die Unterklasse enthält die Generator-Konfigurationsoptionen sowie die `HubConnection` selbst.</span><span class="sxs-lookup"><span data-stu-id="47817-240">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="47817-241">Konfigurieren der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="47817-241">Configure logging</span></span>

<span data-ttu-id="47817-242">Die Protokollierung wird mithilfe der `ConfigureLogging` -Methode im .NET-Client konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="47817-242">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="47817-243">Protokollierungs Anbieter und Filter können auf die gleiche Weise wie auf dem Server registriert werden.</span><span class="sxs-lookup"><span data-stu-id="47817-243">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="47817-244">Weitere Informationen finden Sie in der Dokumentation zur [Protokollierung ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="47817-244">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="47817-245">Um Protokollierungs Anbieter zu registrieren, müssen Sie die erforderlichen Pakete installieren.</span><span class="sxs-lookup"><span data-stu-id="47817-245">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="47817-246">Eine vollständige Liste finden Sie im Abschnitt [integrierte Protokollierungs Anbieter](xref:fundamentals/logging/index#built-in-logging-providers) der Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="47817-246">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="47817-247">Um z. b. die Konsolen Protokollierung zu `Microsoft.Extensions.Logging.Console` aktivieren, installieren Sie das nuget-Paket.</span><span class="sxs-lookup"><span data-stu-id="47817-247">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="47817-248">Die `AddConsole` Erweiterungsmethode aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="47817-248">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="47817-249">Im JavaScript-Client ist eine ähnliche `configureLogging` Methode vorhanden.</span><span class="sxs-lookup"><span data-stu-id="47817-249">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="47817-250">Geben Sie `LogLevel` einen Wert an, der die minimale Ebene der zu erstellenden Protokollmeldungen angibt.</span><span class="sxs-lookup"><span data-stu-id="47817-250">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="47817-251">Protokolle werden in das Browser Konsolenfenster geschrieben.</span><span class="sxs-lookup"><span data-stu-id="47817-251">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="47817-252">Anstelle eines `LogLevel` Werts können Sie auch einen `string` Wert angeben, der den Namen einer Protokollebene darstellt.</span><span class="sxs-lookup"><span data-stu-id="47817-252">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="47817-253">Dies ist hilfreich beim Konfigurieren der signalr-Protokollierung in Umgebungen, in denen Sie `LogLevel` keinen Zugriff auf die Konstanten haben.</span><span class="sxs-lookup"><span data-stu-id="47817-253">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="47817-254">In der folgenden Tabelle sind die verfügbaren Protokoll Ebenen aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="47817-254">The following table lists the available log levels.</span></span> <span data-ttu-id="47817-255">Der von Ihnen bereitgestellte `configureLogging` Wert, der die **minimale** Protokollierung festlegt, die protokolliert werden soll.</span><span class="sxs-lookup"><span data-stu-id="47817-255">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="47817-256">Nachrichten, die auf dieser Ebene protokolliert werden, **oder die nach der Tabelle aufgeführten Ebenen**werden protokolliert.</span><span class="sxs-lookup"><span data-stu-id="47817-256">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="47817-257">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="47817-257">String</span></span>                      | <span data-ttu-id="47817-258">LogLevel</span><span class="sxs-lookup"><span data-stu-id="47817-258">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="47817-259">`info`**oder**`information`</span><span class="sxs-lookup"><span data-stu-id="47817-259">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="47817-260">`warn`**oder**`warning`</span><span class="sxs-lookup"><span data-stu-id="47817-260">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

::: moniker-end

> [!NOTE]
> <span data-ttu-id="47817-261">Um die Protokollierung vollständig `signalR.LogLevel.None` zu deaktivieren `configureLogging` , geben Sie in der Methode an</span><span class="sxs-lookup"><span data-stu-id="47817-261">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="47817-262">Weitere Informationen zur Protokollierung finden Sie in der [signalr-Diagnose Dokumentation](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="47817-262">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="47817-263">Der signalr-Java-Client verwendet die [SLF4J](https://www.slf4j.org/) -Bibliothek für die Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="47817-263">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="47817-264">Dabei handelt es sich um eine allgemeine Protokollierungs-API, die es Benutzern der Bibliothek ermöglicht, ihre eigene spezifische Protokollierungs Implementierung zu wählen, indem Sie eine bestimmte Protokollierungs Abhängigkeit einbinden.</span><span class="sxs-lookup"><span data-stu-id="47817-264">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="47817-265">Der folgende Code Ausschnitt zeigt, wie mit dem `java.util.logging` signalr-Java-Client verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="47817-265">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="47817-266">Wenn Sie die Protokollierung in ihren Abhängigkeiten nicht konfigurieren, lädt SLF4J eine Standard Protokollierung ohne Vorgang mit folgender Warnmeldung:</span><span class="sxs-lookup"><span data-stu-id="47817-266">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="47817-267">Dies kann sicher ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="47817-267">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="47817-268">Zulässige Transporte konfigurieren</span><span class="sxs-lookup"><span data-stu-id="47817-268">Configure allowed transports</span></span>

<span data-ttu-id="47817-269">Die von signalr verwendeten Transporte können im `WithUrl` -Befehl (`withUrl` in JavaScript) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="47817-269">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="47817-270">Ein bitweises OR der Werte von `HttpTransportType` kann verwendet werden, um den Client so einzuschränken, dass nur die angegebenen Transporte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="47817-270">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="47817-271">Alle Transporte sind standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="47817-271">All transports are enabled by default.</span></span>

<span data-ttu-id="47817-272">So können Sie beispielsweise den Transport von Server gesendeten Ereignissen deaktivieren, aber websockets und lange Abruf Verbindungen zulassen:</span><span class="sxs-lookup"><span data-stu-id="47817-272">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="47817-273">Im JavaScript-Client werden Transporte durch Festlegen des-Felds `transport` für das Options-Objekt konfiguriert, `withUrl`das für bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="47817-273">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="47817-274">In dieser Version des Java-Clients ist websockets der einzige verfügbare Transport.</span><span class="sxs-lookup"><span data-stu-id="47817-274">In this version of the Java client websockets is the only available transport.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="47817-275">Im Java-Client wird der Transport mit der `withTransport` -Methode `HttpHubConnectionBuilder`im ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="47817-275">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="47817-276">Der Java-Client verwendet standardmäßig den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="47817-276">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="47817-277">Der signalr-Java-Client unterstützt noch keinen Transport Fall Back.</span><span class="sxs-lookup"><span data-stu-id="47817-277">The SignalR Java client doesn't support transport fallback yet.</span></span>

::: moniker-end

### <a name="configure-bearer-authentication"></a><span data-ttu-id="47817-278">Konfigurieren der bearerauthentifizierung</span><span class="sxs-lookup"><span data-stu-id="47817-278">Configure bearer authentication</span></span>

<span data-ttu-id="47817-279">Um Authentifizierungsdaten zusammen mit signalr-Anforderungen bereitzustellen, `AccessTokenProvider` verwenden Sie`accessTokenFactory` die-Option (in JavaScript), um eine Funktion anzugeben, die das gewünschte Zugriffs Token zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="47817-279">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="47817-280">Im .NET-Client wird dieses Zugriffs Token als HTTP- `Authorization` `Bearer`tokenauthentifizierungstoken (mit dem-Header mit dem Typ) an das Token "bearerauthentifizierung" geleitet.</span><span class="sxs-lookup"><span data-stu-id="47817-280">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="47817-281">Im JavaScript-Client wird das Zugriffs Token als bearertoken verwendet, **außer** in einigen Fällen, in denen Browser-APIs die Fähigkeit zum Anwenden von Headern einschränken (insbesondere bei vom Server gesendeten Ereignissen und websockets-Anforderungen).</span><span class="sxs-lookup"><span data-stu-id="47817-281">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="47817-282">In diesen Fällen wird das Zugriffs Token als Abfrage Zeichenfolgen-Wert `access_token`bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="47817-282">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="47817-283">Im .NET-Client kann die `AccessTokenProvider` Option mithilfe des Options Delegaten in `WithUrl`angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="47817-283">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="47817-284">Im JavaScript-Client wird das Zugriffs Token konfiguriert, indem das `accessTokenFactory` -Feld für das Options-Objekt in `withUrl`festgelegt wird:</span><span class="sxs-lookup"><span data-stu-id="47817-284">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="47817-285">Im signalr-Java-Client können Sie ein bearertoken für die Authentifizierung konfigurieren, indem Sie eine zugriffstokenfactory für " [httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)" bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="47817-285">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="47817-286">Verwenden Sie [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__), um einen [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html) bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="47817-286">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="47817-287">Beim Aufrufen von " [Single.](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)Write" können Sie Logik schreiben, um Zugriffs Token für den Client zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="47817-287">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="47817-288">Konfigurieren von Timeout-und Keep-Alive-Optionen</span><span class="sxs-lookup"><span data-stu-id="47817-288">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="47817-289">Zusätzliche Optionen zum Konfigurieren von Timeout und Keep-Alive-Verhalten sind für `HubConnection` das Objekt selbst verfügbar:</span><span class="sxs-lookup"><span data-stu-id="47817-289">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>


::: moniker range=">= aspnetcore-2.2"

# <a name="nettabdotnet"></a>[<span data-ttu-id="47817-290">.NET</span><span class="sxs-lookup"><span data-stu-id="47817-290">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="47817-291">Option</span><span class="sxs-lookup"><span data-stu-id="47817-291">Option</span></span> | <span data-ttu-id="47817-292">Standardwert</span><span class="sxs-lookup"><span data-stu-id="47817-292">Default value</span></span> | <span data-ttu-id="47817-293">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="47817-293">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="47817-294">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="47817-294">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="47817-295">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="47817-295">Timeout for server activity.</span></span> <span data-ttu-id="47817-296">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst `Closed` das Ereignis`onclose` aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="47817-296">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="47817-297">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="47817-297">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="47817-298">Der empfohlene Wert ist eine Zahl, die mindestens dem `KeepAliveInterval` Wert des Servers entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="47817-298">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="47817-299">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="47817-299">15 seconds</span></span> | <span data-ttu-id="47817-300">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="47817-300">Timeout for initial server handshake.</span></span> <span data-ttu-id="47817-301">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `Closed` Ereignis aus`onclose` (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="47817-301">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="47817-302">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="47817-302">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="47817-303">Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="47817-303">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="47817-304">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="47817-304">15 seconds</span></span> | <span data-ttu-id="47817-305">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="47817-305">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="47817-306">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="47817-306">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="47817-307">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="47817-307">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="47817-308">Im .NET-Client werden Timeout Werte als `TimeSpan` Werte angegeben.</span><span class="sxs-lookup"><span data-stu-id="47817-308">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="47817-309">JavaScript</span><span class="sxs-lookup"><span data-stu-id="47817-309">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="47817-310">Option</span><span class="sxs-lookup"><span data-stu-id="47817-310">Option</span></span> | <span data-ttu-id="47817-311">Standardwert</span><span class="sxs-lookup"><span data-stu-id="47817-311">Default value</span></span> | <span data-ttu-id="47817-312">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="47817-312">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="47817-313">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="47817-313">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="47817-314">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="47817-314">Timeout for server activity.</span></span> <span data-ttu-id="47817-315">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst `onclose` das Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="47817-315">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="47817-316">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="47817-316">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="47817-317">Der empfohlene Wert ist eine Zahl, die mindestens dem `KeepAliveInterval` Wert des Servers entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="47817-317">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="47817-318">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="47817-318">15 seconds (15,000 miliseconds)</span></span> | <span data-ttu-id="47817-319">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="47817-319">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="47817-320">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="47817-320">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="47817-321">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="47817-321">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="47817-322">Java</span><span class="sxs-lookup"><span data-stu-id="47817-322">Java</span></span>](#tab/java)

| <span data-ttu-id="47817-323">Option</span><span class="sxs-lookup"><span data-stu-id="47817-323">Option</span></span> | <span data-ttu-id="47817-324">Standardwert</span><span class="sxs-lookup"><span data-stu-id="47817-324">Default value</span></span> | <span data-ttu-id="47817-325">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="47817-325">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="47817-326">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="47817-326">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="47817-327">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="47817-327">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="47817-328">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="47817-328">Timeout for server activity.</span></span> <span data-ttu-id="47817-329">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst `onClose` das Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="47817-329">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="47817-330">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="47817-330">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="47817-331">Der empfohlene Wert ist eine Zahl, die mindestens dem `KeepAliveInterval` Wert des Servers entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="47817-331">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="47817-332">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="47817-332">15 seconds</span></span> | <span data-ttu-id="47817-333">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="47817-333">Timeout for initial server handshake.</span></span> <span data-ttu-id="47817-334">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `onClose` -Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="47817-334">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="47817-335">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="47817-335">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="47817-336">Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="47817-336">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="47817-337">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="47817-337">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="47817-338">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="47817-338">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="47817-339">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="47817-339">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="47817-340">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="47817-340">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="47817-341">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="47817-341">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="nettabdotnet"></a>[<span data-ttu-id="47817-342">.NET</span><span class="sxs-lookup"><span data-stu-id="47817-342">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="47817-343">Option</span><span class="sxs-lookup"><span data-stu-id="47817-343">Option</span></span> | <span data-ttu-id="47817-344">Standardwert</span><span class="sxs-lookup"><span data-stu-id="47817-344">Default value</span></span> | <span data-ttu-id="47817-345">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="47817-345">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="47817-346">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="47817-346">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="47817-347">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="47817-347">Timeout for server activity.</span></span> <span data-ttu-id="47817-348">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst `Closed` das Ereignis`onclose` aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="47817-348">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="47817-349">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="47817-349">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="47817-350">Der empfohlene Wert ist eine Zahl, die mindestens dem `KeepAliveInterval` Wert des Servers entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="47817-350">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="47817-351">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="47817-351">15 seconds</span></span> | <span data-ttu-id="47817-352">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="47817-352">Timeout for initial server handshake.</span></span> <span data-ttu-id="47817-353">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `Closed` Ereignis aus`onclose` (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="47817-353">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="47817-354">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="47817-354">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="47817-355">Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="47817-355">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="47817-356">Im .NET-Client werden Timeout Werte als `TimeSpan` Werte angegeben.</span><span class="sxs-lookup"><span data-stu-id="47817-356">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="47817-357">JavaScript</span><span class="sxs-lookup"><span data-stu-id="47817-357">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="47817-358">Option</span><span class="sxs-lookup"><span data-stu-id="47817-358">Option</span></span> | <span data-ttu-id="47817-359">Standardwert</span><span class="sxs-lookup"><span data-stu-id="47817-359">Default value</span></span> | <span data-ttu-id="47817-360">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="47817-360">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="47817-361">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="47817-361">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="47817-362">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="47817-362">Timeout for server activity.</span></span> <span data-ttu-id="47817-363">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst `onclose` das Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="47817-363">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="47817-364">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="47817-364">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="47817-365">Der empfohlene Wert ist eine Zahl, die mindestens dem `KeepAliveInterval` Wert des Servers entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="47817-365">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="47817-366">Java</span><span class="sxs-lookup"><span data-stu-id="47817-366">Java</span></span>](#tab/java)

| <span data-ttu-id="47817-367">Option</span><span class="sxs-lookup"><span data-stu-id="47817-367">Option</span></span> | <span data-ttu-id="47817-368">Standardwert</span><span class="sxs-lookup"><span data-stu-id="47817-368">Default value</span></span> | <span data-ttu-id="47817-369">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="47817-369">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="47817-370">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="47817-370">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="47817-371">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="47817-371">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="47817-372">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="47817-372">Timeout for server activity.</span></span> <span data-ttu-id="47817-373">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst `onClose` das Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="47817-373">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="47817-374">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="47817-374">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="47817-375">Der empfohlene Wert ist eine Zahl, die mindestens dem `KeepAliveInterval` Wert des Servers entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="47817-375">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="47817-376">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="47817-376">15 seconds</span></span> | <span data-ttu-id="47817-377">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="47817-377">Timeout for initial server handshake.</span></span> <span data-ttu-id="47817-378">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `onClose` -Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="47817-378">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="47817-379">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="47817-379">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="47817-380">Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="47817-380">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

::: moniker-end

---

### <a name="configure-additional-options"></a><span data-ttu-id="47817-381">Zusätzliche Optionen konfigurieren</span><span class="sxs-lookup"><span data-stu-id="47817-381">Configure additional options</span></span>

<span data-ttu-id="47817-382">Zusätzliche `WithUrl` Optionen können in der-Methode (`withUrl` in JavaScript) für `HubConnectionBuilder` `HttpHubConnectionBuilder` oder für die verschiedenen Konfigurations-APIs auf dem im Java-Client konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="47817-382">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="47817-383">.NET</span><span class="sxs-lookup"><span data-stu-id="47817-383">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="47817-384">.Net-Option</span><span class="sxs-lookup"><span data-stu-id="47817-384">.NET Option</span></span> |  <span data-ttu-id="47817-385">Standardwert</span><span class="sxs-lookup"><span data-stu-id="47817-385">Default value</span></span> | <span data-ttu-id="47817-386">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="47817-386">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="47817-387">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="47817-387">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="47817-388">Legen Sie dies `true` auf fest, um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="47817-388">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="47817-389">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="47817-389">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="47817-390">Diese Einstellung kann nicht aktiviert werden, wenn der Azure signalr-Dienst verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="47817-390">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="47817-391">Empty</span><span class="sxs-lookup"><span data-stu-id="47817-391">Empty</span></span> | <span data-ttu-id="47817-392">Eine Auflistung von TLS-Zertifikaten, die zum Authentifizieren von Anforderungen gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="47817-392">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="47817-393">Empty</span><span class="sxs-lookup"><span data-stu-id="47817-393">Empty</span></span> | <span data-ttu-id="47817-394">Eine Auflistung von HTTP-Cookies, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="47817-394">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="47817-395">Empty</span><span class="sxs-lookup"><span data-stu-id="47817-395">Empty</span></span> | <span data-ttu-id="47817-396">Anmelde Informationen, die mit jeder HTTP-Anforderung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="47817-396">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="47817-397">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="47817-397">5 seconds</span></span> | <span data-ttu-id="47817-398">Nur websockets.</span><span class="sxs-lookup"><span data-stu-id="47817-398">WebSockets only.</span></span> <span data-ttu-id="47817-399">Die maximale Zeitspanne, die der Client nach dem Schließen des Servers wartet, um die Anforderung zum Schließen zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="47817-399">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="47817-400">Wenn der Server die Schließung innerhalb dieses Zeitraums nicht anerkennt, trennt der Client die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="47817-400">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="47817-401">Empty</span><span class="sxs-lookup"><span data-stu-id="47817-401">Empty</span></span> | <span data-ttu-id="47817-402">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="47817-402">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="47817-403">Ein Delegat, der verwendet werden kann, um den `HttpMessageHandler` zum Senden von HTTP-Anforderungen verwendeten zu konfigurieren oder zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="47817-403">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="47817-404">Wird nicht für WebSocket-Verbindungen verwendet.</span><span class="sxs-lookup"><span data-stu-id="47817-404">Not used for WebSocket connections.</span></span> <span data-ttu-id="47817-405">Dieser Delegat muss einen Wert zurückgeben, der nicht NULL ist, und erhält den Standardwert als Parameter.</span><span class="sxs-lookup"><span data-stu-id="47817-405">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="47817-406">Ändern Sie die Einstellungen für diesen Standardwert, und geben Sie ihn zurück, `HttpMessageHandler` oder geben Sie eine neue Instanz zurück.</span><span class="sxs-lookup"><span data-stu-id="47817-406">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="47817-407">**Wenn Sie den Handler ersetzen, stellen Sie sicher, dass die Einstellungen, die Sie beibehalten möchten, vom bereitgestellten Handler kopiert werden. Andernfalls gelten die konfigurierten Optionen (z. b. Cookies und Header) nicht für den neuen Handler.**</span><span class="sxs-lookup"><span data-stu-id="47817-407">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="47817-408">Ein HTTP-Proxy, der beim Senden von HTTP-Anforderungen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="47817-408">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="47817-409">Legen Sie diesen booleschen Wert fest, um die Standard Anmelde Informationen für http-und websockets-Anforderungen zu senden</span><span class="sxs-lookup"><span data-stu-id="47817-409">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="47817-410">Dies ermöglicht die Verwendung der Windows-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="47817-410">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="47817-411">Ein Delegat, der zum Konfigurieren zusätzlicher WebSocket-Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="47817-411">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="47817-412">Empfängt eine Instanz von [clientwebsocketoptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , die zum Konfigurieren der Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="47817-412">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="47817-413">JavaScript</span><span class="sxs-lookup"><span data-stu-id="47817-413">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="47817-414">JavaScript-Option</span><span class="sxs-lookup"><span data-stu-id="47817-414">JavaScript Option</span></span> | <span data-ttu-id="47817-415">Standardwert</span><span class="sxs-lookup"><span data-stu-id="47817-415">Default Value</span></span> | <span data-ttu-id="47817-416">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="47817-416">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="47817-417">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="47817-417">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="47817-418">Legen Sie dies `true` auf fest, um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="47817-418">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="47817-419">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="47817-419">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="47817-420">Diese Einstellung kann nicht aktiviert werden, wenn der Azure signalr-Dienst verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="47817-420">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="47817-421">Java</span><span class="sxs-lookup"><span data-stu-id="47817-421">Java</span></span>](#tab/java)

| <span data-ttu-id="47817-422">Java-Option</span><span class="sxs-lookup"><span data-stu-id="47817-422">Java Option</span></span> | <span data-ttu-id="47817-423">Standardwert</span><span class="sxs-lookup"><span data-stu-id="47817-423">Default Value</span></span> | <span data-ttu-id="47817-424">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="47817-424">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="47817-425">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="47817-425">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="47817-426">Legen Sie dies `true` auf fest, um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="47817-426">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="47817-427">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="47817-427">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="47817-428">Diese Einstellung kann nicht aktiviert werden, wenn der Azure signalr-Dienst verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="47817-428">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="47817-429">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="47817-429">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="47817-430">Empty</span><span class="sxs-lookup"><span data-stu-id="47817-430">Empty</span></span> | <span data-ttu-id="47817-431">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="47817-431">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="47817-432">Im .NET-Client können diese Optionen durch den für `WithUrl`bereitgestellten Options Delegaten geändert werden:</span><span class="sxs-lookup"><span data-stu-id="47817-432">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="47817-433">Im JavaScript-Client können diese Optionen in einem JavaScript-Objekt bereitgestellt werden, `withUrl`das für Folgendes bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="47817-433">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="47817-434">Im Java-Client können diese Optionen mit den Methoden auf der `HttpHubConnectionBuilder` konfiguriert werden, die von der`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="47817-434">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="47817-435">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="47817-435">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

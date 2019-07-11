---
title: ASP.NET Core SignalR-Konfiguration
author: bradygaster
description: Erfahren Sie, wie Sie ASP.NET Core SignalR-apps konfigurieren.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/03/2019
uid: signalr/configuration
ms.openlocfilehash: 8c9fcaecb04555718f5da6a42a8e56c258e795af
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813455"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="e6e7b-103">ASP.NET Core SignalR-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="e6e7b-103">ASP.NET Core SignalR configuration</span></span>

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="e6e7b-104">JSON/MessagePack Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="e6e7b-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="e6e7b-105">ASP.NET Core SignalR unterstützt zwei Protokolle für die Codierung von Nachrichten: [JSON](https://www.json.org/) und [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="e6e7b-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="e6e7b-106">Jedes Protokoll verfügt über Konfigurationsoptionen für die Serialisierung.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="e6e7b-107">JSON-Serialisierung kann konfiguriert werden, auf dem Server mithilfe der [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) Erweiterungsmethode, die nach dem hinzuzufügenden [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in Ihre `Startup.ConfigureServices` Methode.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="e6e7b-108">Die `AddJsonProtocol` Methode verwendet einen Delegaten, der empfängt eine `options` Objekt.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-108">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="e6e7b-109">Die [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) -Eigenschaft für dieses Objekt ist ein JSON.NET `JsonSerializerSettings` -Objekt, das zum Konfigurieren der Serialisierung der Argumente und Rückgabewerte verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-109">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="e6e7b-110">Finden Sie unter den [JSON.NET-Dokumentation](https://www.newtonsoft.com/json/help/html/Introduction.htm) Weitere Details.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-110">See the [JSON.NET Documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm) for more details.</span></span>

<span data-ttu-id="e6e7b-111">Verwenden Sie beispielsweise zum Konfigurieren der Serialisierer, verwenden Sie "PascalCase" Eigenschaftennamen, anstelle der Standardnamen "CamelCase" den folgenden Code ein:</span><span class="sxs-lookup"><span data-stu-id="e6e7b-111">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
        new DefaultContractResolver();
    });
```

<span data-ttu-id="e6e7b-112">In der .NET Client `AddJsonProtocol` Erweiterungsmethode vorhanden ist, auf [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="e6e7b-112">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="e6e7b-113">Die `Microsoft.Extensions.DependencyInjection` Namespace muss importiert werden, um die Erweiterungsmethode zu beheben:</span><span class="sxs-lookup"><span data-stu-id="e6e7b-113">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="e6e7b-114">Es ist nicht möglich, das JSON-Serialisierung in der JavaScript-Client zu diesem Zeitpunkt zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-114">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="e6e7b-115">MessagePack Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="e6e7b-115">MessagePack serialization options</span></span>

<span data-ttu-id="e6e7b-116">MessagePack Serialisierung kann konfiguriert werden, indem ein Delegat, der die [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) aufrufen.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-116">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="e6e7b-117">Finden Sie unter [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) Weitere Details.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-117">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="e6e7b-118">Es ist nicht möglich, MessagePack Serialisierung zu diesem Zeitpunkt im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-118">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="e6e7b-119">Konfigurieren Sie Serveroptionen</span><span class="sxs-lookup"><span data-stu-id="e6e7b-119">Configure server options</span></span>

<span data-ttu-id="e6e7b-120">Die folgende Tabelle beschreibt die Optionen zum Konfigurieren von SignalR-Hubs:</span><span class="sxs-lookup"><span data-stu-id="e6e7b-120">The following table describes options for configuring SignalR hubs:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="e6e7b-121">Option</span><span class="sxs-lookup"><span data-stu-id="e6e7b-121">Option</span></span> | <span data-ttu-id="e6e7b-122">Standardwert</span><span class="sxs-lookup"><span data-stu-id="e6e7b-122">Default Value</span></span> | <span data-ttu-id="e6e7b-123">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="e6e7b-123">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="e6e7b-124">30 Sekunden</span><span class="sxs-lookup"><span data-stu-id="e6e7b-124">30 seconds</span></span> | <span data-ttu-id="e6e7b-125">Der Server berücksichtigt den Client getrennt wird, wenn sie eine Nachricht (einschließlich Keep-alive-) in einem bestimmten Intervall empfangen hat.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-125">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="e6e7b-126">Es kann länger als diese Timeoutintervall für den Client tatsächlich gekennzeichnet werden nicht verbunden ist, aufgrund der Implementierung dauern.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-126">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="e6e7b-127">Der empfohlene Wert ist doppelte die `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-127">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="e6e7b-128">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="e6e7b-128">15 seconds</span></span> | <span data-ttu-id="e6e7b-129">Wenn der Client innerhalb dieses Zeitraums eine anfängliche Handshake-Nachricht senden, nicht, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-129">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="e6e7b-130">Dies ist eine erweiterte Einstellung, die nur geändert werden soll, wenn der Handshake-Timeout-Fehlern aufgrund von schweren Netzwerklatenz auftreten.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-130">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="e6e7b-131">Weitere Einzelheiten für den handshakeprozess finden Sie in der [SignalR-Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="e6e7b-131">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="e6e7b-132">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="e6e7b-132">15 seconds</span></span> | <span data-ttu-id="e6e7b-133">Wenn der Server eine Nachricht nicht innerhalb dieses Intervalls gesendet noch nicht, wird eine Ping-Nachricht automatisch gesendet, die Verbindung geöffnet bleiben.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-133">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="e6e7b-134">Wenn Sie `KeepAliveInterval`, Ändern der `ServerTimeout` / `serverTimeoutInMilliseconds` auf dem Client festlegen.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-134">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="e6e7b-135">Die empfohlene `ServerTimeout` / `serverTimeoutInMilliseconds` Wert double der `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-135">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="e6e7b-136">Alle installierten Protokolle</span><span class="sxs-lookup"><span data-stu-id="e6e7b-136">All installed protocols</span></span> | <span data-ttu-id="e6e7b-137">Von diesen Hub unterstützten Protokolle.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-137">Protocols supported by this hub.</span></span> <span data-ttu-id="e6e7b-138">Standardmäßig alle Protokolle, die auf dem Server registriert sind zulässig, aber Protokolle können aus dieser Liste Deaktivieren bestimmter Protokolle für individuelle Hubs entfernt werden.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-138">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="e6e7b-139">Wenn `true`, detaillierte Ausnahme Nachrichten werden an Clients zurückgegeben, wenn in einer hubmethode eine Ausnahme ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-139">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="e6e7b-140">Der Standardwert ist `false`, wie diese ausnahmemeldungen können vertraulichen Informationen enthalten.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-140">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="e6e7b-141">Die maximale Anzahl von Elementen, die für den Client gepuffert werden können hochladen Streams.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-141">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="e6e7b-142">Wenn dieser Grenzwert erreicht wird, wird die Verarbeitung der Aufrufe blockiert, bis der Server Stream Elemente verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-142">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|

::: moniker-end

::: moniker range="= aspnetcore-2.2"

| <span data-ttu-id="e6e7b-143">Option</span><span class="sxs-lookup"><span data-stu-id="e6e7b-143">Option</span></span> | <span data-ttu-id="e6e7b-144">Standardwert</span><span class="sxs-lookup"><span data-stu-id="e6e7b-144">Default Value</span></span> | <span data-ttu-id="e6e7b-145">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="e6e7b-145">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="e6e7b-146">30 Sekunden</span><span class="sxs-lookup"><span data-stu-id="e6e7b-146">30 seconds</span></span> | <span data-ttu-id="e6e7b-147">Der Server berücksichtigt den Client getrennt wird, wenn sie eine Nachricht (einschließlich Keep-alive-) in einem bestimmten Intervall empfangen hat.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-147">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="e6e7b-148">Es kann länger als diese Timeoutintervall für den Client tatsächlich gekennzeichnet werden nicht verbunden ist, aufgrund der Implementierung dauern.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-148">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="e6e7b-149">Der empfohlene Wert ist doppelte die `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-149">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="e6e7b-150">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="e6e7b-150">15 seconds</span></span> | <span data-ttu-id="e6e7b-151">Wenn der Client innerhalb dieses Zeitraums eine anfängliche Handshake-Nachricht senden, nicht, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-151">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="e6e7b-152">Dies ist eine erweiterte Einstellung, die nur geändert werden soll, wenn der Handshake-Timeout-Fehlern aufgrund von schweren Netzwerklatenz auftreten.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-152">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="e6e7b-153">Weitere Einzelheiten für den handshakeprozess finden Sie in der [SignalR-Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="e6e7b-153">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="e6e7b-154">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="e6e7b-154">15 seconds</span></span> | <span data-ttu-id="e6e7b-155">Wenn der Server eine Nachricht nicht innerhalb dieses Intervalls gesendet noch nicht, wird eine Ping-Nachricht automatisch gesendet, die Verbindung geöffnet bleiben.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-155">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="e6e7b-156">Wenn Sie `KeepAliveInterval`, Ändern der `ServerTimeout` / `serverTimeoutInMilliseconds` auf dem Client festlegen.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-156">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="e6e7b-157">Die empfohlene `ServerTimeout` / `serverTimeoutInMilliseconds` Wert double der `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-157">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="e6e7b-158">Alle installierten Protokolle</span><span class="sxs-lookup"><span data-stu-id="e6e7b-158">All installed protocols</span></span> | <span data-ttu-id="e6e7b-159">Von diesen Hub unterstützten Protokolle.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-159">Protocols supported by this hub.</span></span> <span data-ttu-id="e6e7b-160">Standardmäßig alle Protokolle, die auf dem Server registriert sind zulässig, aber Protokolle können aus dieser Liste Deaktivieren bestimmter Protokolle für individuelle Hubs entfernt werden.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-160">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="e6e7b-161">Wenn `true`, detaillierte Ausnahme Nachrichten werden an Clients zurückgegeben, wenn in einer hubmethode eine Ausnahme ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-161">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="e6e7b-162">Der Standardwert ist `false`, wie diese ausnahmemeldungen können vertraulichen Informationen enthalten.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-162">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| <span data-ttu-id="e6e7b-163">Option</span><span class="sxs-lookup"><span data-stu-id="e6e7b-163">Option</span></span> | <span data-ttu-id="e6e7b-164">Standardwert</span><span class="sxs-lookup"><span data-stu-id="e6e7b-164">Default Value</span></span> | <span data-ttu-id="e6e7b-165">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="e6e7b-165">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="e6e7b-166">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="e6e7b-166">15 seconds</span></span> | <span data-ttu-id="e6e7b-167">Wenn der Client innerhalb dieses Zeitraums eine anfängliche Handshake-Nachricht senden, nicht, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-167">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="e6e7b-168">Dies ist eine erweiterte Einstellung, die nur geändert werden soll, wenn der Handshake-Timeout-Fehlern aufgrund von schweren Netzwerklatenz auftreten.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-168">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="e6e7b-169">Weitere Einzelheiten für den handshakeprozess finden Sie in der [SignalR-Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="e6e7b-169">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="e6e7b-170">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="e6e7b-170">15 seconds</span></span> | <span data-ttu-id="e6e7b-171">Wenn der Server eine Nachricht nicht innerhalb dieses Intervalls gesendet noch nicht, wird eine Ping-Nachricht automatisch gesendet, die Verbindung geöffnet bleiben.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-171">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="e6e7b-172">Wenn Sie `KeepAliveInterval`, Ändern der `ServerTimeout` / `serverTimeoutInMilliseconds` auf dem Client festlegen.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-172">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="e6e7b-173">Die empfohlene `ServerTimeout` / `serverTimeoutInMilliseconds` Wert double der `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-173">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="e6e7b-174">Alle installierten Protokolle</span><span class="sxs-lookup"><span data-stu-id="e6e7b-174">All installed protocols</span></span> | <span data-ttu-id="e6e7b-175">Von diesen Hub unterstützten Protokolle.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-175">Protocols supported by this hub.</span></span> <span data-ttu-id="e6e7b-176">Standardmäßig alle Protokolle, die auf dem Server registriert sind zulässig, aber Protokolle können aus dieser Liste Deaktivieren bestimmter Protokolle für individuelle Hubs entfernt werden.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-176">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="e6e7b-177">Wenn `true`, detaillierte Ausnahme Nachrichten werden an Clients zurückgegeben, wenn in einer hubmethode eine Ausnahme ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-177">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="e6e7b-178">Der Standardwert ist `false`, wie diese ausnahmemeldungen können vertraulichen Informationen enthalten.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-178">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

<span data-ttu-id="e6e7b-179">Optionen für alle Hubs konfiguriert werden können, durch die Bereitstellung einer Optionen-Delegat, der die `AddSignalR` Aufrufen in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-179">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="e6e7b-180">Optionen für einen einzelnen Hub überschreiben die globale Optionen im `AddSignalR` und kann konfiguriert werden, mithilfe von <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span><span class="sxs-lookup"><span data-stu-id="e6e7b-180">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="e6e7b-181">Erweiterte HTTP-Konfigurationsoptionen</span><span class="sxs-lookup"><span data-stu-id="e6e7b-181">Advanced HTTP configuration options</span></span>

<span data-ttu-id="e6e7b-182">Verwendung `HttpConnectionDispatcherOptions` so konfigurieren Sie erweiterte Einstellungen, die im Zusammenhang mit Transporte und Speicherverwaltung für Puffer.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-182">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="e6e7b-183">Diese Optionen werden durch Übergeben eines Delegaten, konfiguriert [MapHub\<T >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-183">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="e6e7b-184">Die folgende Tabelle beschreibt die Optionen zum Konfigurieren von ASP.NET Core SignalR erweiterten HTTP-Optionen:</span><span class="sxs-lookup"><span data-stu-id="e6e7b-184">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="e6e7b-185">Option</span><span class="sxs-lookup"><span data-stu-id="e6e7b-185">Option</span></span> | <span data-ttu-id="e6e7b-186">Standardwert</span><span class="sxs-lookup"><span data-stu-id="e6e7b-186">Default Value</span></span> | <span data-ttu-id="e6e7b-187">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="e6e7b-187">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="e6e7b-188">32 KB</span><span class="sxs-lookup"><span data-stu-id="e6e7b-188">32 KB</span></span> | <span data-ttu-id="e6e7b-189">Die maximale Anzahl von Bytes, die vom Client empfangen, die die Server-Puffer.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-189">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="e6e7b-190">Durch Erhöhen dieses Wertes können den Server, größere Nachrichten zu empfangen, aber arbeitsspeichernutzung negativ beeinträchtigt werden kann.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-190">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="e6e7b-191">Sammeln von Daten automatisch aus der `Authorize` Attribute, die auf die hubklasse angewendet.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-191">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="e6e7b-192">Eine Liste der [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) Objekte verwendet, um zu bestimmen, ob ein Client autorisiert ist, eine Verbindung mit dem Hub herstellen.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-192">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="e6e7b-193">32 KB</span><span class="sxs-lookup"><span data-stu-id="e6e7b-193">32 KB</span></span> | <span data-ttu-id="e6e7b-194">Die maximale Anzahl von Bytes, die von der app gesendet werden, die die Server-Puffer.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-194">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="e6e7b-195">Durch Erhöhen dieses Wertes können den Server aus, um größere Nachrichten zu senden, aber arbeitsspeichernutzung negativ beeinträchtigt werden kann.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-195">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="e6e7b-196">Alle Transporte sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-196">All Transports are enabled.</span></span> | <span data-ttu-id="e6e7b-197">Eine Bitmaske der `HttpTransportType` Werte, die die Transporte einschränken, können ein Client kann die Verbindung verwenden.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-197">A bitmask of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="e6e7b-198">Siehe weiter unten.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-198">See below.</span></span> | <span data-ttu-id="e6e7b-199">Zusätzliche Optionen, die speziell für den Transport Long Polling.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-199">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="e6e7b-200">Siehe weiter unten.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-200">See below.</span></span> | <span data-ttu-id="e6e7b-201">Zusätzliche Optionen, die speziell für die WebSockets-Übertragung.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-201">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="e6e7b-202">Der Transport Long Polling verfügt über zusätzliche Optionen, die mit konfiguriert werden können die `LongPolling` Eigenschaft:</span><span class="sxs-lookup"><span data-stu-id="e6e7b-202">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="e6e7b-203">Option</span><span class="sxs-lookup"><span data-stu-id="e6e7b-203">Option</span></span> | <span data-ttu-id="e6e7b-204">Standardwert</span><span class="sxs-lookup"><span data-stu-id="e6e7b-204">Default Value</span></span> | <span data-ttu-id="e6e7b-205">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="e6e7b-205">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="e6e7b-206">90 Sekunden</span><span class="sxs-lookup"><span data-stu-id="e6e7b-206">90 seconds</span></span> | <span data-ttu-id="e6e7b-207">Die maximale Zeitspanne wartet der Server eine Nachricht an den Client gesendet werden soll, bevor eine Anforderung für die einzelnen Abfrage beendet.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-207">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="e6e7b-208">Durch senken dieses Werts bewirkt, dass den Client neue abrufanforderungen häufiger ausgeben.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-208">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="e6e7b-209">Der WebSocket-Transport verfügt über zusätzliche Optionen, die mit konfiguriert werden können die `WebSockets` Eigenschaft:</span><span class="sxs-lookup"><span data-stu-id="e6e7b-209">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="e6e7b-210">Option</span><span class="sxs-lookup"><span data-stu-id="e6e7b-210">Option</span></span> | <span data-ttu-id="e6e7b-211">Standardwert</span><span class="sxs-lookup"><span data-stu-id="e6e7b-211">Default Value</span></span> | <span data-ttu-id="e6e7b-212">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="e6e7b-212">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="e6e7b-213">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="e6e7b-213">5 seconds</span></span> | <span data-ttu-id="e6e7b-214">Nachdem der Server wird geschlossen, wenn der Client nicht innerhalb dieses Zeitraums zu schließen, wird die Verbindung beendet.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-214">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="e6e7b-215">Ein Delegat, der verwendet werden kann, Festlegen der `Sec-WebSocket-Protocol` -Header auf einen benutzerdefinierten Wert.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-215">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="e6e7b-216">Der Delegat empfängt die Werte, die vom Client als Eingabe angefordert werden, und es wird erwartet, um den gewünschten Wert zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-216">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="e6e7b-217">Konfigurieren Sie Clientoptionen</span><span class="sxs-lookup"><span data-stu-id="e6e7b-217">Configure client options</span></span>

<span data-ttu-id="e6e7b-218">Clientoptionen können konfiguriert werden, auf die `HubConnectionBuilder` Typ (verfügbar in den .NET- und JavaScript-Clients).</span><span class="sxs-lookup"><span data-stu-id="e6e7b-218">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="e6e7b-219">Es ist auch verfügbar im Java-Client, aber die `HttpHubConnectionBuilder` Unterklasse ist, was die Builder-Konfigurationsoptionen, sowie auf enthält die `HubConnection` selbst.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-219">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="e6e7b-220">Konfigurieren der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="e6e7b-220">Configure logging</span></span>

<span data-ttu-id="e6e7b-221">Protokollierung wurde so konfiguriert, der .NET Client die `ConfigureLogging` Methode.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-221">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="e6e7b-222">Protokollierung von Anbietern und Filter kann auf die gleiche Weise registriert werden, wie sie auf dem Server sind.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-222">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="e6e7b-223">Finden Sie unter den [Protokollierung in ASP.NET Core](xref:fundamentals/logging/index) Dokumentation zu informieren.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-223">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="e6e7b-224">Um Anbieter für die Protokollierung zu registrieren, müssen Sie die erforderlichen Pakete installieren.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-224">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="e6e7b-225">Finden Sie unter den [integrierte Protokollierungsanbieter](xref:fundamentals/logging/index#built-in-logging-providers) Abschnitt der Dokumentation für eine vollständige Liste.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-225">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="e6e7b-226">Beispielsweise um konsolenprotokollierung zu aktivieren, installieren die `Microsoft.Extensions.Logging.Console` NuGet-Paket.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-226">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="e6e7b-227">Rufen Sie die `AddConsole` Erweiterungsmethode:</span><span class="sxs-lookup"><span data-stu-id="e6e7b-227">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="e6e7b-228">Im JavaScript-Client, eine ähnliche `configureLogging` Methode vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-228">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="e6e7b-229">Geben Sie einen `LogLevel` Wert, der angibt, der Mindestebene von protokollmeldungen zu erzeugen.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-229">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="e6e7b-230">Protokolle werden in das Browserfenster für die Konsole geschrieben.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-230">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e6e7b-231">Anstelle von eine `LogLevel` Wert, Sie bieten auch eine `string` Wert, der einen Ebenennamen Protokoll darstellt.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-231">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="e6e7b-232">Dies ist nützlich, wenn Sie SignalR, die Protokollierung in Umgebungen konfigurieren, in dem Sie haben keinen Zugriff auf, die `LogLevel` Konstanten.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-232">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="e6e7b-233">Die folgende Tabelle enthält die verfügbaren Protokolliergrade.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-233">The following table lists the available log levels.</span></span> <span data-ttu-id="e6e7b-234">Wert zu `configureLogging` legt die **minimale** Protokollebene, die protokolliert werden.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-234">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="e6e7b-235">Auf dieser Ebene protokollierte Nachrichten **oder die Ebenen, die danach in der Tabelle aufgeführten**, protokolliert werden.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-235">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="e6e7b-236">string</span><span class="sxs-lookup"><span data-stu-id="e6e7b-236">string</span></span> | <span data-ttu-id="e6e7b-237">LogLevel</span><span class="sxs-lookup"><span data-stu-id="e6e7b-237">LogLevel</span></span> |
| - | - |
| `"trace"` | `LogLevel.Trace` |
| `"debug"` | `LogLevel.Debug` |
| <span data-ttu-id="e6e7b-238">`"info"` **Oder** `"information"`</span><span class="sxs-lookup"><span data-stu-id="e6e7b-238">`"info"` **or** `"information"`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="e6e7b-239">`"warn"` **Oder** `"warning"`</span><span class="sxs-lookup"><span data-stu-id="e6e7b-239">`"warn"` **or** `"warning"`</span></span> | `LogLevel.Warning` |
| `"error"` | `LogLevel.Error` |
| `"critical"` | `LogLevel.Critical` |
| `"none"` | `LogLevel.None` |

::: moniker-end

> [!NOTE]
> <span data-ttu-id="e6e7b-240">Geben Sie zum Deaktivieren der Protokollierung vollständig, `signalR.LogLevel.None` in die `configureLogging` Methode.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-240">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="e6e7b-241">Weitere Informationen zur Protokollierung finden Sie unter den [SignalR-Diagnose – Dokumentation](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="e6e7b-241">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="e6e7b-242">Der SignalR-Java-Client verwendet die [SLF4J](https://www.slf4j.org/) Bibliothek für die Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-242">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="e6e7b-243">Es ist eine allgemeine protokollierungs-API an, die Benutzer der Bibliothek, wählen ihre eigenen spezifischen protokollierungsimplementierung bereitgestellt werden, indem Sie in einer bestimmten Protokollierung Abhängigkeit bringen kann.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-243">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="e6e7b-244">Der folgende Codeausschnitt zeigt, wie Sie mit `java.util.logging` mit dem SignalR-Java-Client.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-244">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="e6e7b-245">Wenn Sie die Anmeldung bei Ihrer Abhängigkeiten nicht konfigurieren, lädt SLF4J eine Protokollierung standardmäßig nicht-Vorgang die folgende Warnmeldung an:</span><span class="sxs-lookup"><span data-stu-id="e6e7b-245">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="e6e7b-246">Dies kann problemlos ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-246">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="e6e7b-247">Konfigurieren von zulässigen Transporte</span><span class="sxs-lookup"><span data-stu-id="e6e7b-247">Configure allowed transports</span></span>

<span data-ttu-id="e6e7b-248">Die von SignalR verwendete Transporte können konfiguriert werden, der `WithUrl` aufrufen (`withUrl` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="e6e7b-248">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="e6e7b-249">Eine bitweise OR-Operation der Werte von `HttpTransportType` kann verwendet werden, um den Client nur die angegebenen Transporte verwenden zu beschränken.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-249">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="e6e7b-250">Alle Transporte sind standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-250">All transports are enabled by default.</span></span>

<span data-ttu-id="e6e7b-251">Zulassen Sie z. B. um den Transport Server-Sent-Ereignisse zu deaktivieren, aber WebSockets oder Long Polling Verbindungen:</span><span class="sxs-lookup"><span data-stu-id="e6e7b-251">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="e6e7b-252">In JavaScript-Client-Transporte werden konfiguriert, durch Festlegen der `transport` Feld für das Options-Objekt, das bereitgestellt, um `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="e6e7b-252">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="e6e7b-253">In dieser Version von Java ist Client Websockets der einzige verfügbare Transport.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-253">In this version of the Java client websockets is the only available transport.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="e6e7b-254">Im Java-Client der Transport aktiviert ist, und die `withTransport` Methode für die `HttpHubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-254">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="e6e7b-255">Die Java-Client verwendet standardmäßig die WebSockets-Übertragung.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-255">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="e6e7b-256">Der SignalR-Java-Client-unterstützt fallback Transport noch keine.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-256">The SignalR Java client doesn't support transport fallback yet.</span></span>

::: moniker-end

### <a name="configure-bearer-authentication"></a><span data-ttu-id="e6e7b-257">Konfigurieren von Bearer-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="e6e7b-257">Configure bearer authentication</span></span>

<span data-ttu-id="e6e7b-258">Um Authentifizierungsdaten zusammen mit Anforderungen für SignalR bereitzustellen, verwenden Sie die `AccessTokenProvider` Option (`accessTokenFactory` in JavaScript) an eine Funktion, die den gewünschten Zugangs-Token zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-258">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="e6e7b-259">In der .NET Client dieses Zugriffstoken wird als übergeben, eine HTTP-"Bearer-Authentifizierung" token (mithilfe der `Authorization` Header vom Typ `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="e6e7b-259">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="e6e7b-260">In JavaScript-Client wird das Zugriffstoken als bearertoken, verwendet **außer** in einigen Fällen, in dem Browser APIs beschränken die Möglichkeit, anzuwenden Header (insbesondere im Server-Sent-Ereignisse und WebSockets-Anforderungen).</span><span class="sxs-lookup"><span data-stu-id="e6e7b-260">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="e6e7b-261">In diesen Fällen wird das Zugriffstoken als einen Abfragezeichenfolgenwert bereitgestellt `access_token`.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-261">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="e6e7b-262">In der .NET Client der `AccessTokenProvider` Option kann angegeben werden, mithilfe der Optionen Delegaten in `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="e6e7b-262">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="e6e7b-263">In JavaScript-Client das Zugriffstoken ist konfiguriert, indem die `accessTokenFactory` Feld das Optionsobjekt in `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="e6e7b-263">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="e6e7b-264">In den SignalR-Java-Client, können Sie ein trägertoken, das zur Authentifizierung verwendet wird, durch die Bereitstellung einer Access token Factory konfigurieren die [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="e6e7b-264">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="e6e7b-265">Verwendung [WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) zu einer [RxJava](https://github.com/ReactiveX/RxJava) [einzelne\<Zeichenfolge >](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="e6e7b-265">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="e6e7b-266">Mit einem Aufruf von [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), Sie können Logik schreiben, um Zugriffstoken für den Client zu generieren.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-266">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="e6e7b-267">Timeout und Keep-alive-Optionen konfigurieren</span><span class="sxs-lookup"><span data-stu-id="e6e7b-267">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="e6e7b-268">Zusätzliche Optionen zum Konfigurieren von Timeouts und Keep-alive-Verhalten befinden sich die `HubConnection` Objekt selbst:</span><span class="sxs-lookup"><span data-stu-id="e6e7b-268">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="e6e7b-269">.NET</span><span class="sxs-lookup"><span data-stu-id="e6e7b-269">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="e6e7b-270">Option</span><span class="sxs-lookup"><span data-stu-id="e6e7b-270">Option</span></span> | <span data-ttu-id="e6e7b-271">Standardwert</span><span class="sxs-lookup"><span data-stu-id="e6e7b-271">Default value</span></span> | <span data-ttu-id="e6e7b-272">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="e6e7b-272">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="e6e7b-273">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="e6e7b-273">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="e6e7b-274">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-274">Timeout for server activity.</span></span> <span data-ttu-id="e6e7b-275">Wenn der Server eine Nachricht in einem bestimmten Intervall gesendet noch nicht, der Client betrachtet, der Server getrennt und der Trigger die `Closed` Ereignis (`onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="e6e7b-275">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="e6e7b-276">Dieser Wert muss groß genug für eine Ping-Nachricht, die vom Server gesendet werden **und** innerhalb des Timeoutintervalls vom Client empfangenen.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-276">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="e6e7b-277">Der empfohlene Wert ist einer Zahl mindestens doppelt auf dem Server `KeepAliveInterval` Wert fest, damit Zeit für Ping-Nachrichten eingehen.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-277">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="e6e7b-278">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="e6e7b-278">15 seconds</span></span> | <span data-ttu-id="e6e7b-279">Timeout für die erstmalige Server-Handshake.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-279">Timeout for initial server handshake.</span></span> <span data-ttu-id="e6e7b-280">Wenn der Server eine Antwort Handshake in einem bestimmten Intervall nicht sendet, wird vom Client abgebrochen, der Handshake und der Trigger die `Closed` Ereignis (`onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="e6e7b-280">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="e6e7b-281">Dies ist eine erweiterte Einstellung, die nur geändert werden soll, wenn der Handshake-Timeout-Fehlern aufgrund von schweren Netzwerklatenz auftreten.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-281">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="e6e7b-282">Weitere Einzelheiten für den Handshake-Prozess finden Sie in der [SignalR-Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="e6e7b-282">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="e6e7b-283">Im .NET Client Timeoutwerte angegeben sind, als `TimeSpan` Werte.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-283">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="e6e7b-284">JavaScript</span><span class="sxs-lookup"><span data-stu-id="e6e7b-284">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="e6e7b-285">Option</span><span class="sxs-lookup"><span data-stu-id="e6e7b-285">Option</span></span> | <span data-ttu-id="e6e7b-286">Standardwert</span><span class="sxs-lookup"><span data-stu-id="e6e7b-286">Default value</span></span> | <span data-ttu-id="e6e7b-287">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="e6e7b-287">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="e6e7b-288">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="e6e7b-288">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="e6e7b-289">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-289">Timeout for server activity.</span></span> <span data-ttu-id="e6e7b-290">Wenn der Server eine Nachricht in einem bestimmten Intervall gesendet noch nicht, der Client betrachtet, der Server getrennt und der Trigger die `onclose` Ereignis.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-290">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="e6e7b-291">Dieser Wert muss groß genug für eine Ping-Nachricht, die vom Server gesendet werden **und** innerhalb des Timeoutintervalls vom Client empfangenen.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-291">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="e6e7b-292">Der empfohlene Wert ist einer Zahl mindestens doppelt auf dem Server `KeepAliveInterval` Wert fest, damit Zeit für Ping-Nachrichten eingehen.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-292">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="e6e7b-293">Java</span><span class="sxs-lookup"><span data-stu-id="e6e7b-293">Java</span></span>](#tab/java)

| <span data-ttu-id="e6e7b-294">Option</span><span class="sxs-lookup"><span data-stu-id="e6e7b-294">Option</span></span> | <span data-ttu-id="e6e7b-295">Standardwert</span><span class="sxs-lookup"><span data-stu-id="e6e7b-295">Default value</span></span> | <span data-ttu-id="e6e7b-296">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="e6e7b-296">Description</span></span> |
| ----------- | ------------- | ----------- |
|<span data-ttu-id="e6e7b-297">`getServerTimeout` `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="e6e7b-297">`getServerTimeout` `setServerTimeout`</span></span> | <span data-ttu-id="e6e7b-298">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="e6e7b-298">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="e6e7b-299">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-299">Timeout for server activity.</span></span> <span data-ttu-id="e6e7b-300">Wenn der Server eine Nachricht in einem bestimmten Intervall gesendet noch nicht, der Client betrachtet, der Server getrennt und der Trigger die `onClose` Ereignis.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-300">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="e6e7b-301">Dieser Wert muss groß genug für eine Ping-Nachricht, die vom Server gesendet werden **und** innerhalb des Timeoutintervalls vom Client empfangenen.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-301">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="e6e7b-302">Der empfohlene Wert ist einer Zahl mindestens doppelt auf dem Server `KeepAliveInterval` Wert fest, damit Zeit für Ping-Nachrichten eingehen.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-302">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="e6e7b-303">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="e6e7b-303">15 seconds</span></span> | <span data-ttu-id="e6e7b-304">Timeout für die erstmalige Server-Handshake.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-304">Timeout for initial server handshake.</span></span> <span data-ttu-id="e6e7b-305">Wenn der Server eine Antwort Handshake in einem bestimmten Intervall nicht sendet, wird vom Client abgebrochen, der Handshake und der Trigger die `onClose` Ereignis.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-305">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="e6e7b-306">Dies ist eine erweiterte Einstellung, die nur geändert werden soll, wenn der Handshake-Timeout-Fehlern aufgrund von schweren Netzwerklatenz auftreten.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-306">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="e6e7b-307">Weitere Einzelheiten für den Handshake-Prozess finden Sie in der [SignalR-Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="e6e7b-307">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="e6e7b-308">Konfigurieren zusätzlicher Optionen</span><span class="sxs-lookup"><span data-stu-id="e6e7b-308">Configure additional options</span></span>

<span data-ttu-id="e6e7b-309">Zusätzliche Optionen können konfiguriert werden, der `WithUrl` (`withUrl` in JavaScript)-Methode `HubConnectionBuilder` oder auf die verschiedenen Konfigurations-APIs auf die `HttpHubConnectionBuilder` im Java-Client:</span><span class="sxs-lookup"><span data-stu-id="e6e7b-309">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="e6e7b-310">.NET</span><span class="sxs-lookup"><span data-stu-id="e6e7b-310">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="e6e7b-311">Option für .NET</span><span class="sxs-lookup"><span data-stu-id="e6e7b-311">.NET Option</span></span> |  <span data-ttu-id="e6e7b-312">Standardwert</span><span class="sxs-lookup"><span data-stu-id="e6e7b-312">Default value</span></span> | <span data-ttu-id="e6e7b-313">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="e6e7b-313">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="e6e7b-314">Eine Funktion zurückgeben einer Zeichenfolge, die als bearertoken in HTTP-Anforderungen Authentifizierung bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-314">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="e6e7b-315">Legen Sie diesen `true` um die Aushandlung Schritt zu überspringen.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-315">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="e6e7b-316">**Nur unterstützt, wenn die WebSockets-Übertragung der einzige aktiviert-Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-316">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="e6e7b-317">Diese Einstellung kann nicht aktiviert werden, wenn mithilfe des Azure SignalR Service.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-317">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="e6e7b-318">Empty</span><span class="sxs-lookup"><span data-stu-id="e6e7b-318">Empty</span></span> | <span data-ttu-id="e6e7b-319">Eine Auflistung von TLS-Zertifikate zum Authentifizieren von Anforderungen senden.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-319">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="e6e7b-320">Empty</span><span class="sxs-lookup"><span data-stu-id="e6e7b-320">Empty</span></span> | <span data-ttu-id="e6e7b-321">Eine Auflistung von HTTP-Cookies, die mit jeder HTTP-Anforderung gesendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-321">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="e6e7b-322">Empty</span><span class="sxs-lookup"><span data-stu-id="e6e7b-322">Empty</span></span> | <span data-ttu-id="e6e7b-323">Die Anmeldeinformationen mit jeder HTTP-Anforderung senden.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-323">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="e6e7b-324">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="e6e7b-324">5 seconds</span></span> | <span data-ttu-id="e6e7b-325">Nur WebSockets.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-325">WebSockets only.</span></span> <span data-ttu-id="e6e7b-326">Die maximale Zeitspanne wartet der Client nach dem Schließen für den Server die Anforderung schließende bestätigt.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-326">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="e6e7b-327">Wenn der Server das Schließen nicht innerhalb dieses Zeitraums bestätigt nicht, trennt den Client.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-327">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="e6e7b-328">Empty</span><span class="sxs-lookup"><span data-stu-id="e6e7b-328">Empty</span></span> | <span data-ttu-id="e6e7b-329">Eine Zuordnung von zusätzlichen HTTP-Header, die mit jeder HTTP-Anforderung gesendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-329">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="e6e7b-330">Ein Delegat, der verwendet werden kann, um zu konfigurieren, oder Ersetzen Sie die `HttpMessageHandler` zum Senden von HTTP-Anforderungen verwendet.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-330">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="e6e7b-331">WebSocket-Verbindungen verwendet nicht.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-331">Not used for WebSocket connections.</span></span> <span data-ttu-id="e6e7b-332">Dieser Delegat muss einen Wert ungleich Null zurückgeben, und den Standardwert als Parameter erhält.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-332">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="e6e7b-333">Ändern Sie die Einstellungen auf dieser Standardwert und zurückgeben oder ein neues zurückgeben `HttpMessageHandler` Instanz.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-333">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="e6e7b-334">**Beim Ersetzen der Handler, stellen Sie sicher, kopieren Sie die Einstellungen, die Sie aus der bereitgestellte Handler beibehalten möchten, anwenden nicht die konfigurierten Optionen (z. B. Cookies und Header), andernfalls auf den neuen Handler.**</span><span class="sxs-lookup"><span data-stu-id="e6e7b-334">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="e6e7b-335">Ein HTTP-Proxy beim Senden von HTTP-Anforderungen verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-335">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="e6e7b-336">Legen Sie diese boolescher Wert, um die Standardanmeldeinformationen für HTTP und WebSockets-Anforderungen zu senden.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-336">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="e6e7b-337">Dadurch wird die Verwendung der Windows-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-337">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="e6e7b-338">Ein Delegat, der so konfigurieren Sie zusätzliche "WebSocket"-Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-338">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="e6e7b-339">Empfängt eine Instanz von [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , die verwendet werden kann, um Optionen zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-339">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="e6e7b-340">JavaScript</span><span class="sxs-lookup"><span data-stu-id="e6e7b-340">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="e6e7b-341">JavaScript-Option</span><span class="sxs-lookup"><span data-stu-id="e6e7b-341">JavaScript Option</span></span> | <span data-ttu-id="e6e7b-342">Standardwert</span><span class="sxs-lookup"><span data-stu-id="e6e7b-342">Default Value</span></span> | <span data-ttu-id="e6e7b-343">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="e6e7b-343">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="e6e7b-344">Eine Funktion zurückgeben einer Zeichenfolge, die als bearertoken in HTTP-Anforderungen Authentifizierung bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-344">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="e6e7b-345">Legen Sie diesen `true` um die Aushandlung Schritt zu überspringen.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-345">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="e6e7b-346">**Nur unterstützt, wenn die WebSockets-Übertragung der einzige aktiviert-Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-346">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="e6e7b-347">Diese Einstellung kann nicht aktiviert werden, wenn mithilfe des Azure SignalR Service.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-347">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="e6e7b-348">Java</span><span class="sxs-lookup"><span data-stu-id="e6e7b-348">Java</span></span>](#tab/java)

| <span data-ttu-id="e6e7b-349">Java Option</span><span class="sxs-lookup"><span data-stu-id="e6e7b-349">Java Option</span></span> | <span data-ttu-id="e6e7b-350">Standardwert</span><span class="sxs-lookup"><span data-stu-id="e6e7b-350">Default Value</span></span> | <span data-ttu-id="e6e7b-351">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="e6e7b-351">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="e6e7b-352">Eine Funktion zurückgeben einer Zeichenfolge, die als bearertoken in HTTP-Anforderungen Authentifizierung bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-352">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="e6e7b-353">Legen Sie diesen `true` um die Aushandlung Schritt zu überspringen.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-353">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="e6e7b-354">**Nur unterstützt, wenn die WebSockets-Übertragung der einzige aktiviert-Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-354">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="e6e7b-355">Diese Einstellung kann nicht aktiviert werden, wenn mithilfe des Azure SignalR Service.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-355">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="e6e7b-356">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="e6e7b-356">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="e6e7b-357">Empty</span><span class="sxs-lookup"><span data-stu-id="e6e7b-357">Empty</span></span> | <span data-ttu-id="e6e7b-358">Eine Zuordnung von zusätzlichen HTTP-Header, die mit jeder HTTP-Anforderung gesendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="e6e7b-358">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="e6e7b-359">In der .NET Client können diese Optionen von der Delegat Optionen geändert werden `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="e6e7b-359">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="e6e7b-360">In JavaScript-Client können in einem JavaScript-Objekt, das bereitgestellt, um diese Optionen bereitgestellt werden `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="e6e7b-360">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="e6e7b-361">Im Java-Client diese Optionen können konfiguriert werden mit den Methoden für die `HttpHubConnectionBuilder` zurückgegeben, die von der `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="e6e7b-361">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="e6e7b-362">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="e6e7b-362">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

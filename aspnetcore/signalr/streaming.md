---
title: Streaming in ASP.net Core signalr verwenden
author: bradygaster
description: Erfahren Sie, wie Sie Daten zwischen dem Client und dem Server streamen.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/05/2019
uid: signalr/streaming
ms.openlocfilehash: d520c8eec3e777acb9604bdcb5969268deabf8da
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2019
ms.locfileid: "71186930"
---
# <a name="use-streaming-in-aspnet-core-signalr"></a><span data-ttu-id="1d274-103">Streaming in ASP.net Core signalr verwenden</span><span class="sxs-lookup"><span data-stu-id="1d274-103">Use streaming in ASP.NET Core SignalR</span></span>

<span data-ttu-id="1d274-104">Von [Brennan](https://github.com/BrennanConroy) "a"</span><span class="sxs-lookup"><span data-stu-id="1d274-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1d274-105">ASP.net Core signalr unterstützt das Streaming vom Client zum Server und vom Server zum Client.</span><span class="sxs-lookup"><span data-stu-id="1d274-105">ASP.NET Core SignalR supports streaming from client to server and from server to client.</span></span> <span data-ttu-id="1d274-106">Dies ist nützlich für Szenarien, in denen Daten Fragmente im Laufe der Zeit eintreffen.</span><span class="sxs-lookup"><span data-stu-id="1d274-106">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="1d274-107">Beim Streaming wird jedes Fragment an den Client oder Server gesendet, sobald es verfügbar wird, anstatt darauf zu warten, dass alle Daten verfügbar werden.</span><span class="sxs-lookup"><span data-stu-id="1d274-107">When streaming, each fragment is sent to the client or server as soon as it becomes available, rather than waiting for all of the data to become available.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1d274-108">ASP.net Core signalr unterstützt das Streamen von Rückgabe Werten von Server Methoden.</span><span class="sxs-lookup"><span data-stu-id="1d274-108">ASP.NET Core SignalR supports streaming return values of server methods.</span></span> <span data-ttu-id="1d274-109">Dies ist nützlich für Szenarien, in denen Daten Fragmente im Laufe der Zeit eintreffen.</span><span class="sxs-lookup"><span data-stu-id="1d274-109">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="1d274-110">Wenn ein Rückgabewert an den Client gestreamt wird, wird jedes Fragment an den Client gesendet, sobald es verfügbar wird, anstatt darauf zu warten, dass alle Daten verfügbar werden.</span><span class="sxs-lookup"><span data-stu-id="1d274-110">When a return value is streamed to the client, each fragment is sent to the client as soon as it becomes available, rather than waiting for all the data to become available.</span></span>

::: moniker-end

<span data-ttu-id="1d274-111">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1d274-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="set-up-a-hub-for-streaming"></a><span data-ttu-id="1d274-112">Einrichten eines Hubs für das Streaming</span><span class="sxs-lookup"><span data-stu-id="1d274-112">Set up a hub for streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1d274-113">Eine Hub-Methode wird automatisch zu einer streaminghub- <xref:System.Collections.Generic.IAsyncEnumerable`1>Methode <xref:System.Threading.Channels.ChannelReader%601>, wenn Sie `Task<ChannelReader<T>>`,, `Task<IAsyncEnumerable<T>>`oder zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="1d274-113">A hub method automatically becomes a streaming hub method when it returns <xref:System.Collections.Generic.IAsyncEnumerable`1>, <xref:System.Threading.Channels.ChannelReader%601>, `Task<IAsyncEnumerable<T>>`, or `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1d274-114">Eine Hub-Methode wird automatisch zu einer streaminghub-Methode <xref:System.Threading.Channels.ChannelReader%601> , wenn `Task<ChannelReader<T>>`Sie einen oder ein zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="1d274-114">A hub method automatically becomes a streaming hub method when it returns a <xref:System.Threading.Channels.ChannelReader%601> or a `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

### <a name="server-to-client-streaming"></a><span data-ttu-id="1d274-115">Streaming zwischen Server und Client</span><span class="sxs-lookup"><span data-stu-id="1d274-115">Server-to-client streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1d274-116">Streaming Hub-Methoden können `IAsyncEnumerable<T>` zusätzlich zu `ChannelReader<T>`zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="1d274-116">Streaming hub methods can return `IAsyncEnumerable<T>` in addition to `ChannelReader<T>`.</span></span> <span data-ttu-id="1d274-117">Die einfachste Möglichkeit zur Rückgabe `IAsyncEnumerable<T>` besteht darin, dass Sie die Hub-Methode zu einer asynchronen Iteratormethode wird, wie im folgenden Beispiel veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="1d274-117">The simplest way to return `IAsyncEnumerable<T>` is by making the hub method an async iterator method as the following sample demonstrates.</span></span> <span data-ttu-id="1d274-118">Asynchrone Hub-Iteratormethoden können `CancellationToken` einen Parameter akzeptieren, der ausgelöst wird, wenn der Client den Datenstrom abonniert.</span><span class="sxs-lookup"><span data-stu-id="1d274-118">Hub async iterator methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="1d274-119">Asynchrone Iteratormethoden vermeiden häufige Probleme, die mit Kanälen auftreten, wie `ChannelReader` z. b. das frühzeitige zurückgeben <xref:System.Threading.Channels.ChannelWriter`1>oder Beenden der Methode ohne das Abschließen von.</span><span class="sxs-lookup"><span data-stu-id="1d274-119">Async iterator methods avoid problems common with Channels, such as not returning the `ChannelReader` early enough or exiting the method without completing the <xref:System.Threading.Channels.ChannelWriter`1>.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

<span data-ttu-id="1d274-120">Das folgende Beispiel zeigt die Grundlagen des Streamings von Daten an den Client mithilfe von Kanälen.</span><span class="sxs-lookup"><span data-stu-id="1d274-120">The following sample shows the basics of streaming data to the client using Channels.</span></span> <span data-ttu-id="1d274-121">Jedes Mal, wenn ein Objekt in <xref:System.Threading.Channels.ChannelWriter%601>das geschrieben wird, wird das Objekt sofort an den Client gesendet.</span><span class="sxs-lookup"><span data-stu-id="1d274-121">Whenever an object is written to the <xref:System.Threading.Channels.ChannelWriter%601>, the object is immediately sent to the client.</span></span> <span data-ttu-id="1d274-122">Am Ende `ChannelWriter` ist abgeschlossen, um dem Client mitzuteilen, dass der Stream geschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="1d274-122">At the end, the `ChannelWriter` is completed to tell the client the stream is closed.</span></span>

> [!NOTE]
> <span data-ttu-id="1d274-123">Schreiben Sie in `ChannelWriter<T>` einem Hintergrund Thread in den, und `ChannelReader` geben Sie so bald wie möglich zurück.</span><span class="sxs-lookup"><span data-stu-id="1d274-123">Write to the `ChannelWriter<T>` on a background thread and return the `ChannelReader` as soon as possible.</span></span> <span data-ttu-id="1d274-124">Andere hubaufrufe werden blockiert, bis eine `ChannelReader` zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="1d274-124">Other hub invocations are blocked until a `ChannelReader` is returned.</span></span>
>
> <span data-ttu-id="1d274-125">Packen Sie die Logik `try ... catch`in einer.</span><span class="sxs-lookup"><span data-stu-id="1d274-125">Wrap logic in a `try ... catch`.</span></span> <span data-ttu-id="1d274-126">Vervollständigen Sie `Channel` `catch` in und außerhalb von, um sicherzustellen, dass der Aufruf der hubmethode ordnungsgemäß abgeschlossen ist. `catch`</span><span class="sxs-lookup"><span data-stu-id="1d274-126">Complete the `Channel` in the `catch` and outside the `catch` to make sure the hub method invocation is completed properly.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Streaming hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[Streaming hub method](streaming/samples/2.2/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[Streaming hub method](streaming/samples/2.1/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="1d274-127">Server-zu-Client-streaminghubmethoden können `CancellationToken` einen Parameter akzeptieren, der ausgelöst wird, wenn der Client den Datenstrom abonniert.</span><span class="sxs-lookup"><span data-stu-id="1d274-127">Server-to-client streaming hub methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="1d274-128">Verwenden Sie dieses Token, um den Server Vorgang zu beenden und alle Ressourcen freizugeben, wenn der Client die Verbindung vor dem Ende des Streams trennt.</span><span class="sxs-lookup"><span data-stu-id="1d274-128">Use this token to stop the server operation and release any resources if the client disconnects before the end of the stream.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="1d274-129">Client-zu-Server-Streaming</span><span class="sxs-lookup"><span data-stu-id="1d274-129">Client-to-server streaming</span></span>

<span data-ttu-id="1d274-130">Eine Hub-Methode wird automatisch zu einer Client-zu-Server-streaminghub Methode, wenn Sie ein oder <xref:System.Threading.Channels.ChannelReader%601> mehrere <xref:System.Collections.Generic.IAsyncEnumerable%601>Objekte des Typs oder akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="1d274-130">A hub method automatically becomes a client-to-server streaming hub method when it accepts one or more objects of type <xref:System.Threading.Channels.ChannelReader%601> or <xref:System.Collections.Generic.IAsyncEnumerable%601>.</span></span> <span data-ttu-id="1d274-131">Das folgende Beispiel zeigt die Grundlagen zum Lesen von Streamingdaten, die vom Client gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="1d274-131">The following sample shows the basics of reading streaming data sent from the client.</span></span> <span data-ttu-id="1d274-132">Wenn der Client in den <xref:System.Threading.Channels.ChannelWriter%601>schreibt, werden die Daten auf dem Server, von dem die Hub-Methode liest, in den `ChannelReader` geschrieben.</span><span class="sxs-lookup"><span data-stu-id="1d274-132">Whenever the client writes to the <xref:System.Threading.Channels.ChannelWriter%601>, the data is written into the `ChannelReader` on the server from which the hub method is reading.</span></span>

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

<span data-ttu-id="1d274-133">Eine <xref:System.Collections.Generic.IAsyncEnumerable%601> Version der-Methode folgt.</span><span class="sxs-lookup"><span data-stu-id="1d274-133">An <xref:System.Collections.Generic.IAsyncEnumerable%601> version of the method follows.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        Console.WriteLine(item);
    }
}
```

::: moniker-end

## <a name="net-client"></a><span data-ttu-id="1d274-134">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="1d274-134">.NET client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="1d274-135">Streaming zwischen Server und Client</span><span class="sxs-lookup"><span data-stu-id="1d274-135">Server-to-client streaming</span></span>


::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1d274-136">Die `StreamAsync` - `StreamAsChannelAsync` Methode und `HubConnection` die-Methode in werden zum Aufrufen von Server-zu-Client-Streamingmethoden verwendet.</span><span class="sxs-lookup"><span data-stu-id="1d274-136">The `StreamAsync` and `StreamAsChannelAsync` methods on `HubConnection` are used to invoke server-to-client streaming methods.</span></span> <span data-ttu-id="1d274-137">Übergeben Sie den in der Hub-Methode definierten Namen und die Argumente `StreamAsync` der `StreamAsChannelAsync`Hub-Methode an oder.</span><span class="sxs-lookup"><span data-stu-id="1d274-137">Pass the hub method name and arguments defined in the hub method to `StreamAsync` or `StreamAsChannelAsync`.</span></span> <span data-ttu-id="1d274-138">Der generische Parameter für `StreamAsync<T>` und `StreamAsChannelAsync<T>` gibt den Typ von Objekten an, die von der Streamingmethode zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="1d274-138">The generic parameter on `StreamAsync<T>` and `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="1d274-139">Ein Objekt vom Typ `IAsyncEnumerable<T>` oder `ChannelReader<T>` wird vom Datenstrom Aufruf zurückgegeben und stellt den Datenstrom auf dem Client dar.</span><span class="sxs-lookup"><span data-stu-id="1d274-139">An object of type `IAsyncEnumerable<T>` or `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

<span data-ttu-id="1d274-140">Ein `StreamAsync` Beispiel, das `IAsyncEnumerable<int>`zurückgibt:</span><span class="sxs-lookup"><span data-stu-id="1d274-140">A `StreamAsync` example that returns `IAsyncEnumerable<int>`:</span></span>

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var stream = await hubConnection.StreamAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

await foreach (var count in stream)
{
    Console.WriteLine($"{count}");
}

Console.WriteLine("Streaming completed");
```

<span data-ttu-id="1d274-141">Ein entsprechendes `StreamAsChannelAsync` Beispiel, das `ChannelReader<int>`zurückgibt:</span><span class="sxs-lookup"><span data-stu-id="1d274-141">A corresponding `StreamAsChannelAsync` example that returns `ChannelReader<int>`:</span></span>

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var channel = await hubConnection.StreamAsChannelAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="1d274-142">Die `StreamAsChannelAsync` -Methode `HubConnection` für wird verwendet, um eine Server-zu-Client-Streamingmethode aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="1d274-142">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="1d274-143">Übergeben Sie den in der Hub-Methode definierten Namen und die Argumente `StreamAsChannelAsync`der Hub-Methode an.</span><span class="sxs-lookup"><span data-stu-id="1d274-143">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="1d274-144">Der generische Parameter für `StreamAsChannelAsync<T>` gibt den Typ von Objekten an, die von der Streamingmethode zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="1d274-144">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="1d274-145">Ein `ChannelReader<T>` wird vom Datenstrom Aufruf zurückgegeben und stellt den Datenstrom auf dem Client dar.</span><span class="sxs-lookup"><span data-stu-id="1d274-145">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var channel = await hubConnection.StreamAsChannelAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="1d274-146">Die `StreamAsChannelAsync` -Methode `HubConnection` für wird verwendet, um eine Server-zu-Client-Streamingmethode aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="1d274-146">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="1d274-147">Übergeben Sie den in der Hub-Methode definierten Namen und die Argumente `StreamAsChannelAsync`der Hub-Methode an.</span><span class="sxs-lookup"><span data-stu-id="1d274-147">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="1d274-148">Der generische Parameter für `StreamAsChannelAsync<T>` gibt den Typ von Objekten an, die von der Streamingmethode zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="1d274-148">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="1d274-149">Ein `ChannelReader<T>` wird vom Datenstrom Aufruf zurückgegeben und stellt den Datenstrom auf dem Client dar.</span><span class="sxs-lookup"><span data-stu-id="1d274-149">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

```csharp
var channel = await hubConnection
    .StreamAsChannelAsync<int>("Counter", 10, 500, CancellationToken.None);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="1d274-150">Client-zu-Server-Streaming</span><span class="sxs-lookup"><span data-stu-id="1d274-150">Client-to-server streaming</span></span>

<span data-ttu-id="1d274-151">Es gibt zwei Möglichkeiten, eine Client-zu-Server-streaminghub Methode aus dem .NET-Client aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="1d274-151">There are two ways to invoke a client-to-server streaming hub method from the .NET client.</span></span> <span data-ttu-id="1d274-152">`IAsyncEnumerable<T>` Sie können entweder ein oder ein `ChannelReader` als Argument an `SendAsync`, `InvokeAsync`oder `StreamAsChannelAsync`übergeben, abhängig von der aufgerufenen Hub-Methode.</span><span class="sxs-lookup"><span data-stu-id="1d274-152">You can either pass in an `IAsyncEnumerable<T>` or a `ChannelReader` as an argument to `SendAsync`, `InvokeAsync`, or `StreamAsChannelAsync`, depending on the hub method invoked.</span></span>

<span data-ttu-id="1d274-153">Wenn Daten in das-Objekt `IAsyncEnumerable` oder `ChannelWriter` das-Objekt geschrieben werden, empfängt die Hub-Methode auf dem Server ein neues Element mit den Daten vom Client.</span><span class="sxs-lookup"><span data-stu-id="1d274-153">Whenever data is written to the `IAsyncEnumerable` or `ChannelWriter` object, the hub method on the server receives a new item with the data from the client.</span></span>

<span data-ttu-id="1d274-154">Wenn ein `IAsyncEnumerable` Objekt verwendet wird, endet der Stream, nachdem die Methode, die streamelemente zurückgibt, beendet wird.</span><span class="sxs-lookup"><span data-stu-id="1d274-154">If using an `IAsyncEnumerable` object, the stream ends after the method returning stream items exits.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
    //After the for loop has completed and the local function exits the stream completion will be sent.
}

await connection.SendAsync("UploadStream", clientStreamData());
```

<span data-ttu-id="1d274-155">Wenn Sie einen `ChannelWriter`verwenden, vervollständigen Sie den Kanal mit `channel.Writer.Complete()`folgenden Aktionen:</span><span class="sxs-lookup"><span data-stu-id="1d274-155">Or if you're using a `ChannelWriter`, you complete the channel with `channel.Writer.Complete()`:</span></span>

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a><span data-ttu-id="1d274-156">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="1d274-156">JavaScript client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="1d274-157">Streaming zwischen Server und Client</span><span class="sxs-lookup"><span data-stu-id="1d274-157">Server-to-client streaming</span></span>

<span data-ttu-id="1d274-158">JavaScript-Clients wenden Server-zu-Client-Streamingmethoden `connection.stream`auf Hubs mit an.</span><span class="sxs-lookup"><span data-stu-id="1d274-158">JavaScript clients call server-to-client streaming methods on hubs with `connection.stream`.</span></span> <span data-ttu-id="1d274-159">Die `stream` -Methode akzeptiert zwei Argumente:</span><span class="sxs-lookup"><span data-stu-id="1d274-159">The `stream` method accepts two arguments:</span></span>

* <span data-ttu-id="1d274-160">Der Name der hubmethode.</span><span class="sxs-lookup"><span data-stu-id="1d274-160">The name of the hub method.</span></span> <span data-ttu-id="1d274-161">Im folgenden Beispiel lautet `Counter`der Name der Hub-Methode.</span><span class="sxs-lookup"><span data-stu-id="1d274-161">In the following example, the hub method name is `Counter`.</span></span>
* <span data-ttu-id="1d274-162">In der Hub-Methode definierte Argumente.</span><span class="sxs-lookup"><span data-stu-id="1d274-162">Arguments defined in the hub method.</span></span> <span data-ttu-id="1d274-163">Im folgenden Beispiel sind die Argumente eine Anzahl für die Anzahl der zu empfangenden Datenstrom Elemente und die Verzögerung zwischen streamingelementen.</span><span class="sxs-lookup"><span data-stu-id="1d274-163">In the following example, the arguments are a count for the number of stream items to receive and the delay between stream items.</span></span>

<span data-ttu-id="1d274-164">`connection.stream`Gibt einen `IStreamResult`zurück, der eine `subscribe` -Methode enthält.</span><span class="sxs-lookup"><span data-stu-id="1d274-164">`connection.stream` returns an `IStreamResult`, which contains a `subscribe` method.</span></span> <span data-ttu-id="1d274-165">Übergeben Sie `IStreamSubscriber` einen `subscribe` an, und `next`legen `error`Sie die `complete` Rückrufe, und fest, um `stream` Benachrichtigungen vom Aufruf zu empfangen.</span><span class="sxs-lookup"><span data-stu-id="1d274-165">Pass an `IStreamSubscriber` to `subscribe` and set the `next`, `error`, and `complete` callbacks to receive notifications from the `stream` invocation.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="1d274-166">Um den Stream vom Client zu beenden, müssen Sie `dispose` die-Methode `ISubscription` für das-Objekt abrufen `subscribe` , das von der-Methode zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="1d274-166">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span> <span data-ttu-id="1d274-167">Wenn Sie diese Methode `CancellationToken` aufrufen, wird der-Parameter der Hub-Methode abgebrochen.</span><span class="sxs-lookup"><span data-stu-id="1d274-167">Calling this method causes cancellation of the `CancellationToken` parameter of the Hub method, if you provided one.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="1d274-168">Um den Stream vom Client zu beenden, müssen Sie `dispose` die-Methode `ISubscription` für das-Objekt abrufen `subscribe` , das von der-Methode zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="1d274-168">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="1d274-169">Client-zu-Server-Streaming</span><span class="sxs-lookup"><span data-stu-id="1d274-169">Client-to-server streaming</span></span>

<span data-ttu-id="1d274-170">Von JavaScript-Clients werden Client-zu-Server-Streamingmethoden für Hubs `Subject` aufgerufen, indem ein `send`als `invoke`Argument an `stream`, oder übergeben wird, abhängig von der aufgerufenen hubmethode.</span><span class="sxs-lookup"><span data-stu-id="1d274-170">JavaScript clients call client-to-server streaming methods on hubs by passing in a `Subject` as an argument to `send`, `invoke`, or `stream`, depending on the hub method invoked.</span></span> <span data-ttu-id="1d274-171">Ist eine Klasse, die wie eine `Subject`aussieht. `Subject`</span><span class="sxs-lookup"><span data-stu-id="1d274-171">The `Subject` is a class that looks like a `Subject`.</span></span> <span data-ttu-id="1d274-172">Beispielsweise können Sie in rxjs die Klasse " [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) " aus dieser Bibliothek verwenden.</span><span class="sxs-lookup"><span data-stu-id="1d274-172">For example in RxJS, you can use the [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) class from that library.</span></span>

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

<span data-ttu-id="1d274-173">Wenn `subject.next(item)` Sie mit einem Element aufrufen, wird das Element in den Stream geschrieben, und die Hub-Methode empfängt das Element auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="1d274-173">Calling `subject.next(item)` with an item writes the item to the stream, and the hub method receives the item on the server.</span></span>

<span data-ttu-id="1d274-174">Um den Stream zu beenden, `subject.complete()`wird aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="1d274-174">To end the stream, call `subject.complete()`.</span></span>

## <a name="java-client"></a><span data-ttu-id="1d274-175">Java-Client</span><span class="sxs-lookup"><span data-stu-id="1d274-175">Java client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="1d274-176">Streaming zwischen Server und Client</span><span class="sxs-lookup"><span data-stu-id="1d274-176">Server-to-client streaming</span></span>

<span data-ttu-id="1d274-177">Der signalr-Java-Client `stream` verwendet die-Methode, um Streamingmethoden aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="1d274-177">The SignalR Java client uses the `stream` method to invoke streaming methods.</span></span> <span data-ttu-id="1d274-178">`stream`Es werden drei oder mehr Argumente akzeptiert:</span><span class="sxs-lookup"><span data-stu-id="1d274-178">`stream` accepts three or more arguments:</span></span>

* <span data-ttu-id="1d274-179">Der erwartete Typ der streamelemente.</span><span class="sxs-lookup"><span data-stu-id="1d274-179">The expected type of the stream items.</span></span>
* <span data-ttu-id="1d274-180">Der Name der hubmethode.</span><span class="sxs-lookup"><span data-stu-id="1d274-180">The name of the hub method.</span></span>
* <span data-ttu-id="1d274-181">In der Hub-Methode definierte Argumente.</span><span class="sxs-lookup"><span data-stu-id="1d274-181">Arguments defined in the hub method.</span></span>

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

<span data-ttu-id="1d274-182">Die `stream` -Methode `HubConnection` für gibt einen Observable-Wert des Datenstrom-Elementtyps zurück.</span><span class="sxs-lookup"><span data-stu-id="1d274-182">The `stream` method on `HubConnection` returns an Observable of the stream item type.</span></span> <span data-ttu-id="1d274-183">Die-Methode des Observable- `subscribe` Typs ist, `onCompleted` `onError` wo `onNext`und Handler definiert werden.</span><span class="sxs-lookup"><span data-stu-id="1d274-183">The Observable type's `subscribe` method is where `onNext`, `onError` and `onCompleted` handlers are defined.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="1d274-184">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="1d274-184">Additional resources</span></span>

* [<span data-ttu-id="1d274-185">Hubs</span><span class="sxs-lookup"><span data-stu-id="1d274-185">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="1d274-186">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="1d274-186">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="1d274-187">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="1d274-187">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="1d274-188">Veröffentlichen in Azure</span><span class="sxs-lookup"><span data-stu-id="1d274-188">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)

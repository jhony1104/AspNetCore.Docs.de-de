---
title: Verwenden Sie in ASP.NET Core SignalR-streaming
author: bradygaster
description: Erfahren Sie, wie Sie Daten zwischen dem Client und Server streamen.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2019
uid: signalr/streaming
ms.openlocfilehash: 83bbb231482d9c1606be3c5bbbeb1cc3b8efcf7d
ms.sourcegitcommit: eb784a68219b4829d8e50c8a334c38d4b94e0cfa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2019
ms.locfileid: "59982655"
---
# <a name="use-streaming-in-aspnet-core-signalr"></a><span data-ttu-id="29eee-103">Verwenden Sie in ASP.NET Core SignalR-streaming</span><span class="sxs-lookup"><span data-stu-id="29eee-103">Use streaming in ASP.NET Core SignalR</span></span>

<span data-ttu-id="29eee-104">Durch [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="29eee-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="29eee-105">ASP.NET Core SignalR unterstützt das streaming, vom Client zum Server und vom Server zum Client.</span><span class="sxs-lookup"><span data-stu-id="29eee-105">ASP.NET Core SignalR supports streaming from client to server and from server to client.</span></span> <span data-ttu-id="29eee-106">Dies ist nützlich für Szenarien, in denen Fragmente von Daten im Laufe der Zeit eintreffen.</span><span class="sxs-lookup"><span data-stu-id="29eee-106">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="29eee-107">Beim streaming wird jedes Fragment gesendet an den Client oder Server möglichst bald wird es verfügbaren, statt alle Daten auf das Freiwerden warten.</span><span class="sxs-lookup"><span data-stu-id="29eee-107">When streaming, each fragment is sent to the client or server as soon as it becomes available, rather than waiting for all of the data to become available.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="29eee-108">ASP.NET Core SignalR unterstützt streaming Rückgabewerte von Servermethoden.</span><span class="sxs-lookup"><span data-stu-id="29eee-108">ASP.NET Core SignalR supports streaming return values of server methods.</span></span> <span data-ttu-id="29eee-109">Dies ist nützlich für Szenarien, in denen Fragmente von Daten im Laufe der Zeit eintreffen.</span><span class="sxs-lookup"><span data-stu-id="29eee-109">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="29eee-110">Wenn ein Wert zurückgegeben, die an den Client gestreamt wird, wird jedes Fragment an den Client gesendet, sobald es wird zur Verfügung, statt alle Daten auf das Freiwerden warten.</span><span class="sxs-lookup"><span data-stu-id="29eee-110">When a return value is streamed to the client, each fragment is sent to the client as soon as it becomes available, rather than waiting for all the data to become available.</span></span>

::: moniker-end

<span data-ttu-id="29eee-111">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="29eee-111">[View or download sample code](https://github.com/aspnet/Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="set-up-a-hub-for-streaming"></a><span data-ttu-id="29eee-112">Richten Sie einen Hub für das streaming</span><span class="sxs-lookup"><span data-stu-id="29eee-112">Set up a hub for streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="29eee-113">Eine hubmethode wird automatisch eine streaming hubmethode, bei der Rückgabe eine <xref:System.Threading.Channels.ChannelReader`1>, `IAsyncEnumerable<T>`, `Task<ChannelReader<T>>`, oder `Task<IAsyncEnumerable<T>>`.</span><span class="sxs-lookup"><span data-stu-id="29eee-113">A hub method automatically becomes a streaming hub method when it returns a <xref:System.Threading.Channels.ChannelReader`1>, `IAsyncEnumerable<T>`, `Task<ChannelReader<T>>`, or `Task<IAsyncEnumerable<T>>`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="29eee-114">Eine hubmethode wird automatisch eine streaming hubmethode, bei der Rückgabe eine <xref:System.Threading.Channels.ChannelReader`1> oder `Task<ChannelReader<T>>`.</span><span class="sxs-lookup"><span data-stu-id="29eee-114">A hub method automatically becomes a streaming hub method when it returns a <xref:System.Threading.Channels.ChannelReader`1> or a `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

### <a name="server-to-client-streaming"></a><span data-ttu-id="29eee-115">Server-zu-Client-streaming</span><span class="sxs-lookup"><span data-stu-id="29eee-115">Server-to-client streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="29eee-116">Streaming von Hub-Methoden zurückgeben kann `IAsyncEnumerable<T>` zusätzlich zu `ChannelReader<T>`.</span><span class="sxs-lookup"><span data-stu-id="29eee-116">Streaming hub methods can return `IAsyncEnumerable<T>` in addition to `ChannelReader<T>`.</span></span> <span data-ttu-id="29eee-117">Die einfachste Möglichkeit zum Zurückgeben `IAsyncEnumerable<T>` ist, indem Sie der hubmethode eine Async-Iterator-Methode, wie das folgende Beispiel veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="29eee-117">The simplest way to return `IAsyncEnumerable<T>` is by making the hub method an async iterator method as the following sample demonstrates.</span></span> <span data-ttu-id="29eee-118">Hub-Async-Iterator-Methoden akzeptieren können eine `CancellationToken` Parameter, der ausgelöst wird, wenn der Client aus dem Stream kündigt das Abonnement.</span><span class="sxs-lookup"><span data-stu-id="29eee-118">Hub async iterator methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="29eee-119">Async-Iterator-Methoden Probleme, die mit Kanälen, häufig zu vermeiden, z. B. keine zurückgegeben, die `ChannelReader` noch genügend Zeit oder die Methode wird beendet, ohne abgeschlossen der <xref:System.Threading.Channels.ChannelWriter`1>.</span><span class="sxs-lookup"><span data-stu-id="29eee-119">Async iterator methods avoid problems common with Channels, such as not returning the `ChannelReader` early enough or exiting the method without completing the <xref:System.Threading.Channels.ChannelWriter`1>.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

<span data-ttu-id="29eee-120">Das folgende Beispiel zeigt die Grundlagen von streaming-Daten an den Client mithilfe von Kanälen.</span><span class="sxs-lookup"><span data-stu-id="29eee-120">The following sample shows the basics of streaming data to the client using Channels.</span></span> <span data-ttu-id="29eee-121">Jedes Mal, wenn ein Objekt richtet der <xref:System.Threading.Channels.ChannelWriter`1>, das Objekt sofort an den Client gesendet wird.</span><span class="sxs-lookup"><span data-stu-id="29eee-121">Whenever an object is written to the <xref:System.Threading.Channels.ChannelWriter`1>, the object is immediately sent to the client.</span></span> <span data-ttu-id="29eee-122">Am Ende der `ChannelWriter` abgeschlossen ist, um dem Client veranlassen der Stream ist geschlossen.</span><span class="sxs-lookup"><span data-stu-id="29eee-122">At the end, the `ChannelWriter` is completed to tell the client the stream is closed.</span></span>

> [!NOTE]
> <span data-ttu-id="29eee-123">Schreiben in die `ChannelWriter<T>` auf einem Hintergrundthread und Rückgabe der `ChannelReader` so bald wie möglich.</span><span class="sxs-lookup"><span data-stu-id="29eee-123">Write to the `ChannelWriter<T>` on a background thread and return the `ChannelReader` as soon as possible.</span></span> <span data-ttu-id="29eee-124">Andere Hubs Aufrufe werden blockiert, bis eine `ChannelReader` zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="29eee-124">Other hub invocations are blocked until a `ChannelReader` is returned.</span></span>
>
> <span data-ttu-id="29eee-125">Umschließen der Logik in einer `try ... catch`.</span><span class="sxs-lookup"><span data-stu-id="29eee-125">Wrap logic in a `try ... catch`.</span></span> <span data-ttu-id="29eee-126">Abschließen der `Channel` in die `catch` als auch außerhalb der `catch` sicherstellen, dass den Hub Methodenaufruf ordnungsgemäß abgeschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="29eee-126">Complete the `Channel` in the `catch` and outside the `catch` to make sure the hub method invocation is completed properly.</span></span>

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

<span data-ttu-id="29eee-127">Streaming Server-zu-Client-Hub-Methoden akzeptieren können eine `CancellationToken` Parameter, der ausgelöst wird, wenn der Client aus dem Stream kündigt das Abonnement.</span><span class="sxs-lookup"><span data-stu-id="29eee-127">Server-to-client streaming hub methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="29eee-128">Verwenden Sie dieses Token beenden Sie den Servervorgang und alle Ressourcen freigeben, wenn der Client vor dem Ende des Streams, der die Verbindung trennt.</span><span class="sxs-lookup"><span data-stu-id="29eee-128">Use this token to stop the server operation and release any resources if the client disconnects before the end of the stream.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="29eee-129">Client-zu-Server-streaming</span><span class="sxs-lookup"><span data-stu-id="29eee-129">Client-to-server streaming</span></span>

<span data-ttu-id="29eee-130">Eine hubmethode wird automatisch eine Client-zu-Server-streaming hubmethode, wenn sie eine oder mehrere akzeptiert <xref:System.Threading.Channels.ChannelReader`1>s.</span><span class="sxs-lookup"><span data-stu-id="29eee-130">A hub method automatically becomes a client-to-server streaming hub method when it accepts one or more <xref:System.Threading.Channels.ChannelReader`1>s.</span></span> <span data-ttu-id="29eee-131">Das folgende Beispiel zeigt die Grundlagen Lesen von Streamingdaten, die vom Client gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="29eee-131">The following sample shows the basics of reading streaming data sent from the client.</span></span> <span data-ttu-id="29eee-132">Jedes Mal, wenn der Client schreibt in die <xref:System.Threading.Channels.ChannelWriter`1>, in die Daten geschrieben werden die `ChannelReader` auf dem Server, die die hubmethode von der gelesen wird.</span><span class="sxs-lookup"><span data-stu-id="29eee-132">Whenever the client writes to the <xref:System.Threading.Channels.ChannelWriter`1>, the data is written into the `ChannelReader` on the server that the hub method is reading from.</span></span>

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

::: moniker-end

## <a name="net-client"></a><span data-ttu-id="29eee-133">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="29eee-133">.NET client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="29eee-134">Server-zu-Client-streaming</span><span class="sxs-lookup"><span data-stu-id="29eee-134">Server-to-client streaming</span></span>

<span data-ttu-id="29eee-135">Die `StreamAsChannelAsync` Methode `HubConnection` wird verwendet, um eine streaming-Server-zu-Client-Methode aufrufen.</span><span class="sxs-lookup"><span data-stu-id="29eee-135">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="29eee-136">Übergeben Sie die Namen der Hub-Methode und die Argumente, die in die hubmethode, die definiert `StreamAsChannelAsync`.</span><span class="sxs-lookup"><span data-stu-id="29eee-136">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="29eee-137">Der generische Parameter auf `StreamAsChannelAsync<T>` gibt den Typ der Objekte, die von der streaming-Methode zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="29eee-137">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="29eee-138">Ein `ChannelReader<T>` aus dem Stream-Aufruf zurückgegeben wird, und den Stream darstellt, auf dem Client.</span><span class="sxs-lookup"><span data-stu-id="29eee-138">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

::: moniker range=">= aspnetcore-2.2"

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

### <a name="client-to-server-streaming"></a><span data-ttu-id="29eee-139">Client-zu-Server-streaming</span><span class="sxs-lookup"><span data-stu-id="29eee-139">Client-to-server streaming</span></span>

<span data-ttu-id="29eee-140">Um eine Client-zu-Server-streaming hubmethode aus der .NET-Client aufrufen, erstellen Sie eine `Channel` und übergeben Sie die `ChannelReader` als Argument an `SendAsync`, `InvokeAsync`, oder `StreamAsChannelAsync`, je nachdem, für die hubmethode aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="29eee-140">To invoke a client-to-server streaming hub method from the .NET client, create a `Channel` and pass the `ChannelReader` as an argument to `SendAsync`, `InvokeAsync`, or `StreamAsChannelAsync`, depending on the hub method invoked.</span></span>

<span data-ttu-id="29eee-141">Jedes Mal, wenn die Daten geschrieben wird, auf die `ChannelWriter`, die hubmethode auf dem Server erhält ein neues Element mit den Daten vom Client.</span><span class="sxs-lookup"><span data-stu-id="29eee-141">Whenever data is written to the `ChannelWriter`, the hub method on the server receives a new item with the data from the client.</span></span>

<span data-ttu-id="29eee-142">Um den Datenstrom zu beenden, führen Sie den Kanal mit `channel.Writer.Complete()`.</span><span class="sxs-lookup"><span data-stu-id="29eee-142">To end the stream, complete the channel with `channel.Writer.Complete()`.</span></span>

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a><span data-ttu-id="29eee-143">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="29eee-143">JavaScript client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="29eee-144">Server-zu-Client-streaming</span><span class="sxs-lookup"><span data-stu-id="29eee-144">Server-to-client streaming</span></span>

<span data-ttu-id="29eee-145">Clients mit JavaScript-Hubs mit streaming Server-zu-Client-Methoden aufrufen `connection.stream`.</span><span class="sxs-lookup"><span data-stu-id="29eee-145">JavaScript clients call server-to-client streaming methods on hubs with `connection.stream`.</span></span> <span data-ttu-id="29eee-146">Die `stream` -Methode akzeptiert zwei Argumente:</span><span class="sxs-lookup"><span data-stu-id="29eee-146">The `stream` method accepts two arguments:</span></span>

* <span data-ttu-id="29eee-147">Der Name der hubmethode.</span><span class="sxs-lookup"><span data-stu-id="29eee-147">The name of the hub method.</span></span> <span data-ttu-id="29eee-148">Im folgenden Beispiel wird der Name der Hub-Methode `Counter`.</span><span class="sxs-lookup"><span data-stu-id="29eee-148">In the following example, the hub method name is `Counter`.</span></span>
* <span data-ttu-id="29eee-149">In der hubmethode definierten Argumente.</span><span class="sxs-lookup"><span data-stu-id="29eee-149">Arguments defined in the hub method.</span></span> <span data-ttu-id="29eee-150">Im folgenden Beispiel werden die Argumente für eine Anzahl für die Anzahl der zu erhalten und die Verzögerung zwischen Stream-Elemente.</span><span class="sxs-lookup"><span data-stu-id="29eee-150">In the following example, the arguments are a count for the number of stream items to receive and the delay between stream items.</span></span>

<span data-ttu-id="29eee-151">`connection.stream` Gibt eine `IStreamResult`, enthält eine `subscribe` Methode.</span><span class="sxs-lookup"><span data-stu-id="29eee-151">`connection.stream` returns an `IStreamResult`, which contains a `subscribe` method.</span></span> <span data-ttu-id="29eee-152">Übergeben einer `IStreamSubscriber` zu `subscribe` und legen Sie die `next`, `error`, und `complete` Rückrufe zum Empfangen von Benachrichtigungen von der `stream` Aufruf.</span><span class="sxs-lookup"><span data-stu-id="29eee-152">Pass an `IStreamSubscriber` to `subscribe` and set the `next`, `error`, and `complete` callbacks to receive notifications from the `stream` invocation.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="29eee-153">Um den Datenstrom vom Client zu beenden, rufen die `dispose` Methode für die `ISubscription` zurückgegeben, die von der `subscribe` Methode.</span><span class="sxs-lookup"><span data-stu-id="29eee-153">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span> <span data-ttu-id="29eee-154">Das Aufrufen dieser Methode bewirkt, dass Abbruch der `CancellationToken` Parameter der Hub-Methode, wenn Sie eine angegeben.</span><span class="sxs-lookup"><span data-stu-id="29eee-154">Calling this method causes cancellation of the `CancellationToken` parameter of the Hub method, if you provided one.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="29eee-155">Um den Datenstrom vom Client zu beenden, rufen die `dispose` Methode für die `ISubscription` zurückgegeben, die von der `subscribe` Methode.</span><span class="sxs-lookup"><span data-stu-id="29eee-155">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="29eee-156">Client-zu-Server-streaming</span><span class="sxs-lookup"><span data-stu-id="29eee-156">Client-to-server streaming</span></span>

<span data-ttu-id="29eee-157">JavaScript-Clients Client-zu-Server streaming Methoden aufrufen Hubs durch die Übergabe einer `Subject` als Argument an `send`, `invoke`, oder `stream`, je nachdem, für die hubmethode aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="29eee-157">JavaScript clients call client-to-server streaming methods on hubs by passing in a `Subject` as an argument to `send`, `invoke`, or `stream`, depending on the hub method invoked.</span></span> <span data-ttu-id="29eee-158">Die `Subject` ist eine Klasse, der aussieht wie ein `Subject`.</span><span class="sxs-lookup"><span data-stu-id="29eee-158">The `Subject` is a class that looks like a `Subject`.</span></span> <span data-ttu-id="29eee-159">Z. B. in RxJS können Sie die [Betreff](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) -Klasse aus dieser Bibliothek.</span><span class="sxs-lookup"><span data-stu-id="29eee-159">For example in RxJS, you can use the [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) class from that library.</span></span>

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

<span data-ttu-id="29eee-160">Aufrufen von `subject.next(item)` mit ein Element das Element in den Stream geschrieben werden soll, und die hubmethode empfängt das Element auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="29eee-160">Calling `subject.next(item)` with an item writes the item to the stream, and the hub method receives the item on the server.</span></span>

<span data-ttu-id="29eee-161">Um den Datenstrom zu beenden, rufen `subject.complete()`.</span><span class="sxs-lookup"><span data-stu-id="29eee-161">To end the stream, call `subject.complete()`.</span></span>

## <a name="java-client"></a><span data-ttu-id="29eee-162">Java-Client</span><span class="sxs-lookup"><span data-stu-id="29eee-162">Java client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="29eee-163">Server-zu-Client-streaming</span><span class="sxs-lookup"><span data-stu-id="29eee-163">Server-to-client streaming</span></span>

<span data-ttu-id="29eee-164">Der SignalR-Java-Client verwendet die `stream` Methode, um die streaming-Methoden aufrufen.</span><span class="sxs-lookup"><span data-stu-id="29eee-164">The SignalR Java client uses the `stream` method to invoke streaming methods.</span></span> <span data-ttu-id="29eee-165">`stream` akzeptiert mindestens drei Argumente:</span><span class="sxs-lookup"><span data-stu-id="29eee-165">`stream` accepts three or more arguments:</span></span>

* <span data-ttu-id="29eee-166">Der erwartete Typ der Elemente Stream.</span><span class="sxs-lookup"><span data-stu-id="29eee-166">The expected type of the stream items.</span></span>
* <span data-ttu-id="29eee-167">Der Name der hubmethode.</span><span class="sxs-lookup"><span data-stu-id="29eee-167">The name of the hub method.</span></span>
* <span data-ttu-id="29eee-168">In der hubmethode definierten Argumente.</span><span class="sxs-lookup"><span data-stu-id="29eee-168">Arguments defined in the hub method.</span></span>

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

<span data-ttu-id="29eee-169">Die `stream` Methode `HubConnection` gibt ein wahrnehmbares Element, für den Elementtyp des Streams.</span><span class="sxs-lookup"><span data-stu-id="29eee-169">The `stream` method on `HubConnection` returns an Observable of the stream item type.</span></span> <span data-ttu-id="29eee-170">Des Observable Typs `subscribe` Methode kommt `onNext`, `onError` und `onCompleted` Handler definiert sind.</span><span class="sxs-lookup"><span data-stu-id="29eee-170">The Observable type's `subscribe` method is where `onNext`, `onError` and `onCompleted` handlers are defined.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="29eee-171">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="29eee-171">Additional resources</span></span>

* [<span data-ttu-id="29eee-172">Hubs</span><span class="sxs-lookup"><span data-stu-id="29eee-172">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="29eee-173">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="29eee-173">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="29eee-174">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="29eee-174">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="29eee-175">Veröffentlichen in Azure</span><span class="sxs-lookup"><span data-stu-id="29eee-175">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)

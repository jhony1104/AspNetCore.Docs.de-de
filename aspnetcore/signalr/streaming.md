---
title: Verwenden Sie in ASP.NET Core SignalR-streaming
author: bradygaster
description: Erfahren Sie, wie Datenströme von Werten aus Server-Hub-Methoden zurückgeben, und nutzen die Datenströme mit den .NET- und JavaScript-Clients.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/14/2018
uid: signalr/streaming
ms.openlocfilehash: fb7183f7189d62c181f69ffdb170e3da25612919
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57345586"
---
# <a name="use-streaming-in-aspnet-core-signalr"></a><span data-ttu-id="bff35-103">Verwenden Sie in ASP.NET Core SignalR-streaming</span><span class="sxs-lookup"><span data-stu-id="bff35-103">Use streaming in ASP.NET Core SignalR</span></span>

<span data-ttu-id="bff35-104">Durch [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="bff35-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

<span data-ttu-id="bff35-105">ASP.NET Core SignalR unterstützt streaming Rückgabewerte von Servermethoden.</span><span class="sxs-lookup"><span data-stu-id="bff35-105">ASP.NET Core SignalR supports streaming return values of server methods.</span></span> <span data-ttu-id="bff35-106">Dies ist nützlich für Szenarien, in denen Fragmente von Daten im Laufe der Zeit kommen wird.</span><span class="sxs-lookup"><span data-stu-id="bff35-106">This is useful for scenarios where fragments of data will come in over time.</span></span> <span data-ttu-id="bff35-107">Wenn ein Wert zurückgegeben, die an den Client gestreamt wird, wird jedes Fragment an den Client gesendet, sobald es wird zur Verfügung, statt alle Daten auf das Freiwerden warten.</span><span class="sxs-lookup"><span data-stu-id="bff35-107">When a return value is streamed to the client, each fragment is sent to the client as soon as it becomes available, rather than waiting for all the data to become available.</span></span>

<span data-ttu-id="bff35-108">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/live/aspnetcore/signalr/streaming/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bff35-108">[View or download sample code](https://github.com/aspnet/Docs/tree/live/aspnetcore/signalr/streaming/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="set-up-the-hub"></a><span data-ttu-id="bff35-109">Richten Sie den hub</span><span class="sxs-lookup"><span data-stu-id="bff35-109">Set up the hub</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bff35-110">Eine hubmethode wird automatisch eine streaming hubmethode, bei der Rückgabe eine `ChannelReader<T>`, `IAsyncEnumerable<T>`, `Task<ChannelReader<T>>`, oder `Task<IAsyncEnumerable<T>>`.</span><span class="sxs-lookup"><span data-stu-id="bff35-110">A hub method automatically becomes a streaming hub method when it returns a `ChannelReader<T>`, `IAsyncEnumerable<T>`, `Task<ChannelReader<T>>`, or `Task<IAsyncEnumerable<T>>`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bff35-111">Eine hubmethode wird automatisch eine streaming hubmethode, bei der Rückgabe eine `ChannelReader<T>` oder `Task<ChannelReader<T>>`.</span><span class="sxs-lookup"><span data-stu-id="bff35-111">A hub method automatically becomes a streaming hub method when it returns a `ChannelReader<T>` or a `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bff35-112">In ASP.NET Core 3.0 oder höher, streaming-Hub-Methoden zurückgeben kann `IAsyncEnumerable<T>` zusätzlich zu `ChannelReader<T>`.</span><span class="sxs-lookup"><span data-stu-id="bff35-112">In ASP.NET Core 3.0 or later, streaming hub methods can return `IAsyncEnumerable<T>` in addition to `ChannelReader<T>`.</span></span> <span data-ttu-id="bff35-113">Die einfachste Möglichkeit zum Zurückgeben `IAsyncEnumerable<T>` ist, indem Sie der hubmethode eine Async-Iterator-Methode, wie das folgende Beispiel veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="bff35-113">The simplest way to return `IAsyncEnumerable<T>` is by making the hub method an async iterator method as the following sample demonstrates.</span></span> <span data-ttu-id="bff35-114">Hub-Async-Iterator-Methoden akzeptieren können eine `CancellationToken` Parameter, der ausgelöst wird, wenn der Client aus dem Stream kündigt das Abonnement.</span><span class="sxs-lookup"><span data-stu-id="bff35-114">Hub async iterator methods can accept a `CancellationToken` parameter that will be triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="bff35-115">Async-Iterator-Methoden leicht vermeiden häufige Probleme, mit der Kanäle wie z. B. das Zurückgeben von nicht die `ChannelReader` noch genügend Zeit oder die Methode wird beendet, ohne abgeschlossen der `ChannelWriter`.</span><span class="sxs-lookup"><span data-stu-id="bff35-115">Async iterator methods easily avoid problems common with Channels such as not returning the `ChannelReader` early enough or exiting the method without completing the `ChannelWriter`.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/sample/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

<span data-ttu-id="bff35-116">Das folgende Beispiel zeigt die Grundlagen von streaming-Daten an den Client mithilfe von Kanälen.</span><span class="sxs-lookup"><span data-stu-id="bff35-116">The following sample shows the basics of streaming data to the client using Channels.</span></span> <span data-ttu-id="bff35-117">Jedes Mal, wenn ein Objekt richtet der `ChannelWriter` dieses Objekt wird sofort an den Client gesendet.</span><span class="sxs-lookup"><span data-stu-id="bff35-117">Whenever an object is written to the `ChannelWriter` that object is immediately sent to the client.</span></span> <span data-ttu-id="bff35-118">Am Ende der `ChannelWriter` abgeschlossen ist, um dem Client veranlassen der Stream ist geschlossen.</span><span class="sxs-lookup"><span data-stu-id="bff35-118">At the end, the `ChannelWriter` is completed to tell the client the stream is closed.</span></span>

> [!NOTE]
> * <span data-ttu-id="bff35-119">Schreiben in die `ChannelWriter` auf einem Hintergrundthread und Rückgabe der `ChannelReader` so bald wie möglich.</span><span class="sxs-lookup"><span data-stu-id="bff35-119">Write to the `ChannelWriter` on a background thread and return the `ChannelReader` as soon as possible.</span></span> <span data-ttu-id="bff35-120">Andere Aufrufe Hub werden blockiert, bis eine `ChannelReader` zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="bff35-120">Other hub invocations will be blocked until a `ChannelReader` is returned.</span></span>
> * <span data-ttu-id="bff35-121">Umschließen Sie die Logik in einer `try ... catch` und führen Sie die `Channel` im Catch und außerhalb der Catch, um sicherzustellen, dass den Hub ist ordnungsgemäß Methodenaufruf abgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="bff35-121">Wrap your logic in a `try ... catch` and complete the `Channel` in the catch and outside the catch to make sure the hub method invocation is completed properly.</span></span>

::: moniker range="= aspnetcore-2.1"

[!code-csharp[Streaming hub method](streaming/sample/Hubs/StreamHub.aspnetcore21.cs?name=snippet1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[Streaming hub method](streaming/sample/Hubs/StreamHub.cs?name=snippet1)]

<span data-ttu-id="bff35-122">In ASP.NET Core 2.2 oder höher kann streaming-Hub-Methoden akzeptieren eine `CancellationToken` Parameter, der ausgelöst wird, wenn der Client aus dem Stream kündigt das Abonnement.</span><span class="sxs-lookup"><span data-stu-id="bff35-122">In ASP.NET Core 2.2 or later, streaming hub methods can accept a `CancellationToken` parameter that will be triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="bff35-123">Verwenden Sie dieses Token beenden Sie den Servervorgang und alle Ressourcen freigeben, wenn der Client vor dem Ende des Streams, der die Verbindung trennt.</span><span class="sxs-lookup"><span data-stu-id="bff35-123">Use this token to stop the server operation and release any resources if the client disconnects before the end of the stream.</span></span>

::: moniker-end

## <a name="net-client"></a><span data-ttu-id="bff35-124">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="bff35-124">.NET client</span></span>

<span data-ttu-id="bff35-125">Die `StreamAsChannelAsync` Methode `HubConnection` wird verwendet, um eine streaming-Methode aufrufen.</span><span class="sxs-lookup"><span data-stu-id="bff35-125">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a streaming method.</span></span> <span data-ttu-id="bff35-126">Übergeben Sie den Namen des Hub-Methode und Argumente, die in die hubmethode, die definiert `StreamAsChannelAsync`.</span><span class="sxs-lookup"><span data-stu-id="bff35-126">Pass the hub method name, and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="bff35-127">Der generische Parameter auf `StreamAsChannelAsync<T>` gibt den Typ der Objekte, die von der streaming-Methode zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="bff35-127">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="bff35-128">Ein `ChannelReader<T>` , die über den Stream-Aufruf zurückgegeben wird, und den Stream darstellt, auf dem Client.</span><span class="sxs-lookup"><span data-stu-id="bff35-128">A `ChannelReader<T>` is returned from the stream invocation, and represents the stream on the client.</span></span> <span data-ttu-id="bff35-129">Zum Lesen von Daten ist ein häufiges Muster in einer Schleife über `WaitToReadAsync` , und rufen Sie `TryRead` Wenn Daten verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="bff35-129">To read data, a common pattern is to loop over `WaitToReadAsync` and call `TryRead` when data is available.</span></span> <span data-ttu-id="bff35-130">Die Schleife endet, wenn der Stream vom Server geschlossen wurde wurde, oder das Abbruchtoken, um übergeben `StreamAsChannelAsync` abgebrochen wird.</span><span class="sxs-lookup"><span data-stu-id="bff35-130">The loop will end when the stream has been closed by the server, or the cancellation token passed to `StreamAsChannelAsync` is canceled.</span></span>

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

## <a name="javascript-client"></a><span data-ttu-id="bff35-131">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="bff35-131">JavaScript client</span></span>

<span data-ttu-id="bff35-132">JavaScript-Clients streamen Methoden aufrufen Hubs mithilfe von `connection.stream`.</span><span class="sxs-lookup"><span data-stu-id="bff35-132">JavaScript clients call streaming methods on hubs by using `connection.stream`.</span></span> <span data-ttu-id="bff35-133">Die `stream` -Methode akzeptiert zwei Argumente:</span><span class="sxs-lookup"><span data-stu-id="bff35-133">The `stream` method accepts two arguments:</span></span>

* <span data-ttu-id="bff35-134">Der Name der hubmethode.</span><span class="sxs-lookup"><span data-stu-id="bff35-134">The name of the hub method.</span></span> <span data-ttu-id="bff35-135">Im folgenden Beispiel wird der Name der Hub-Methode `Counter`.</span><span class="sxs-lookup"><span data-stu-id="bff35-135">In the following example, the hub method name is `Counter`.</span></span>
* <span data-ttu-id="bff35-136">In der hubmethode definierten Argumente.</span><span class="sxs-lookup"><span data-stu-id="bff35-136">Arguments defined in the hub method.</span></span> <span data-ttu-id="bff35-137">Im folgenden Beispiel werden die Argumente: eine Anzahl für die Anzahl der zu erhalten, und die Verzögerung zwischen Stream-Elemente.</span><span class="sxs-lookup"><span data-stu-id="bff35-137">In the following example, the arguments are: a count for the number of stream items to receive, and the delay between stream items.</span></span>

<span data-ttu-id="bff35-138">`connection.stream` Gibt eine `IStreamResult` enthält eine `subscribe` Methode.</span><span class="sxs-lookup"><span data-stu-id="bff35-138">`connection.stream` returns an `IStreamResult` which contains a `subscribe` method.</span></span> <span data-ttu-id="bff35-139">Übergeben einer `IStreamSubscriber` zu `subscribe` und legen Sie die `next`, `error`, und `complete` Rückrufe an die Benachrichtigungen erhalten, die `stream` Aufruf.</span><span class="sxs-lookup"><span data-stu-id="bff35-139">Pass an `IStreamSubscriber` to `subscribe` and set the `next`, `error`, and `complete` callbacks to get notifications from the `stream` invocation.</span></span>

[!code-javascript[Streaming javascript](streaming/sample/wwwroot/js/stream.js?range=19-36)]

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="bff35-140">Um den Datenstrom vom Client zu beenden, rufen die `dispose` Methode für die `ISubscription` zurückgegeben, die von der `subscribe` Methode.</span><span class="sxs-lookup"><span data-stu-id="bff35-140">To end the stream from the client, call the `dispose` method on the `ISubscription` that is returned from the `subscribe` method.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="bff35-141">Um den Datenstrom vom Client zu beenden, rufen die `dispose` Methode für die `ISubscription` zurückgegeben, die von der `subscribe` Methode.</span><span class="sxs-lookup"><span data-stu-id="bff35-141">To end the stream from the client, call the `dispose` method on the `ISubscription` that is returned from the `subscribe` method.</span></span> <span data-ttu-id="bff35-142">Aufrufen dieser Methode bewirkt, dass die `CancellationToken` Parameter der Hub-Methode (sofern Sie vorhanden) abgebrochen werden soll.</span><span class="sxs-lookup"><span data-stu-id="bff35-142">Calling this method will cause the `CancellationToken` parameter of the Hub method (if you provided one) to be canceled.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"
## <a name="java-client"></a><span data-ttu-id="bff35-143">Java-Client</span><span class="sxs-lookup"><span data-stu-id="bff35-143">Java client</span></span>
<span data-ttu-id="bff35-144">Der SignalR-Java-Client verwendet die `stream` Methode, um die streaming-Methoden aufrufen.</span><span class="sxs-lookup"><span data-stu-id="bff35-144">The SignalR Java client uses the `stream` method to invoke streaming methods.</span></span> <span data-ttu-id="bff35-145">Mindestens drei Argumente akzeptiert:</span><span class="sxs-lookup"><span data-stu-id="bff35-145">It accepts three or more arguments:</span></span>

* <span data-ttu-id="bff35-146">Der erwartete Typ der Elemente stream</span><span class="sxs-lookup"><span data-stu-id="bff35-146">The expected type of the stream items</span></span> 
* <span data-ttu-id="bff35-147">Der Name der hubmethode.</span><span class="sxs-lookup"><span data-stu-id="bff35-147">The name of the hub method.</span></span>
* <span data-ttu-id="bff35-148">In der hubmethode definierten Argumente.</span><span class="sxs-lookup"><span data-stu-id="bff35-148">Arguments defined in the hub method.</span></span> 

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```
<span data-ttu-id="bff35-149">Die `stream` Methode `HubConnection` gibt ein wahrnehmbares Element, für den Elementtyp des Streams.</span><span class="sxs-lookup"><span data-stu-id="bff35-149">The `stream` method on `HubConnection` returns an Observable of the stream item type.</span></span> <span data-ttu-id="bff35-150">Des Observable Typs `subscribe` Methode ist, definieren Sie Ihre `onNext`, `onError` und `onCompleted` Handler.</span><span class="sxs-lookup"><span data-stu-id="bff35-150">The Observable type's `subscribe` method is where you define your `onNext`,  `onError` and  `onCompleted` handlers.</span></span>

::: moniker-end

## <a name="related-resources"></a><span data-ttu-id="bff35-151">Weitere Informationen</span><span class="sxs-lookup"><span data-stu-id="bff35-151">Related resources</span></span>

* [<span data-ttu-id="bff35-152">Hubs</span><span class="sxs-lookup"><span data-stu-id="bff35-152">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="bff35-153">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="bff35-153">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="bff35-154">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="bff35-154">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="bff35-155">Veröffentlichen in Azure</span><span class="sxs-lookup"><span data-stu-id="bff35-155">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)

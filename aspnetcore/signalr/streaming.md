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
# <a name="use-streaming-in-aspnet-core-signalr"></a>Verwenden Sie in ASP.NET Core SignalR-streaming

Durch [Brennan Conroy](https://github.com/BrennanConroy)

ASP.NET Core SignalR unterstützt streaming Rückgabewerte von Servermethoden. Dies ist nützlich für Szenarien, in denen Fragmente von Daten im Laufe der Zeit kommen wird. Wenn ein Wert zurückgegeben, die an den Client gestreamt wird, wird jedes Fragment an den Client gesendet, sobald es wird zur Verfügung, statt alle Daten auf das Freiwerden warten.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/live/aspnetcore/signalr/streaming/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="set-up-the-hub"></a>Richten Sie den hub

::: moniker range=">= aspnetcore-3.0"

Eine hubmethode wird automatisch eine streaming hubmethode, bei der Rückgabe eine `ChannelReader<T>`, `IAsyncEnumerable<T>`, `Task<ChannelReader<T>>`, oder `Task<IAsyncEnumerable<T>>`.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Eine hubmethode wird automatisch eine streaming hubmethode, bei der Rückgabe eine `ChannelReader<T>` oder `Task<ChannelReader<T>>`.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

In ASP.NET Core 3.0 oder höher, streaming-Hub-Methoden zurückgeben kann `IAsyncEnumerable<T>` zusätzlich zu `ChannelReader<T>`. Die einfachste Möglichkeit zum Zurückgeben `IAsyncEnumerable<T>` ist, indem Sie der hubmethode eine Async-Iterator-Methode, wie das folgende Beispiel veranschaulicht. Hub-Async-Iterator-Methoden akzeptieren können eine `CancellationToken` Parameter, der ausgelöst wird, wenn der Client aus dem Stream kündigt das Abonnement. Async-Iterator-Methoden leicht vermeiden häufige Probleme, mit der Kanäle wie z. B. das Zurückgeben von nicht die `ChannelReader` noch genügend Zeit oder die Methode wird beendet, ohne abgeschlossen der `ChannelWriter`.

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/sample/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

Das folgende Beispiel zeigt die Grundlagen von streaming-Daten an den Client mithilfe von Kanälen. Jedes Mal, wenn ein Objekt richtet der `ChannelWriter` dieses Objekt wird sofort an den Client gesendet. Am Ende der `ChannelWriter` abgeschlossen ist, um dem Client veranlassen der Stream ist geschlossen.

> [!NOTE]
> * Schreiben in die `ChannelWriter` auf einem Hintergrundthread und Rückgabe der `ChannelReader` so bald wie möglich. Andere Aufrufe Hub werden blockiert, bis eine `ChannelReader` zurückgegeben wird.
> * Umschließen Sie die Logik in einer `try ... catch` und führen Sie die `Channel` im Catch und außerhalb der Catch, um sicherzustellen, dass den Hub ist ordnungsgemäß Methodenaufruf abgeschlossen.

::: moniker range="= aspnetcore-2.1"

[!code-csharp[Streaming hub method](streaming/sample/Hubs/StreamHub.aspnetcore21.cs?name=snippet1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[Streaming hub method](streaming/sample/Hubs/StreamHub.cs?name=snippet1)]

In ASP.NET Core 2.2 oder höher kann streaming-Hub-Methoden akzeptieren eine `CancellationToken` Parameter, der ausgelöst wird, wenn der Client aus dem Stream kündigt das Abonnement. Verwenden Sie dieses Token beenden Sie den Servervorgang und alle Ressourcen freigeben, wenn der Client vor dem Ende des Streams, der die Verbindung trennt.

::: moniker-end

## <a name="net-client"></a>.NET-Client

Die `StreamAsChannelAsync` Methode `HubConnection` wird verwendet, um eine streaming-Methode aufrufen. Übergeben Sie den Namen des Hub-Methode und Argumente, die in die hubmethode, die definiert `StreamAsChannelAsync`. Der generische Parameter auf `StreamAsChannelAsync<T>` gibt den Typ der Objekte, die von der streaming-Methode zurückgegeben. Ein `ChannelReader<T>` , die über den Stream-Aufruf zurückgegeben wird, und den Stream darstellt, auf dem Client. Zum Lesen von Daten ist ein häufiges Muster in einer Schleife über `WaitToReadAsync` , und rufen Sie `TryRead` Wenn Daten verfügbar ist. Die Schleife endet, wenn der Stream vom Server geschlossen wurde wurde, oder das Abbruchtoken, um übergeben `StreamAsChannelAsync` abgebrochen wird.

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

## <a name="javascript-client"></a>JavaScript-Client

JavaScript-Clients streamen Methoden aufrufen Hubs mithilfe von `connection.stream`. Die `stream` -Methode akzeptiert zwei Argumente:

* Der Name der hubmethode. Im folgenden Beispiel wird der Name der Hub-Methode `Counter`.
* In der hubmethode definierten Argumente. Im folgenden Beispiel werden die Argumente: eine Anzahl für die Anzahl der zu erhalten, und die Verzögerung zwischen Stream-Elemente.

`connection.stream` Gibt eine `IStreamResult` enthält eine `subscribe` Methode. Übergeben einer `IStreamSubscriber` zu `subscribe` und legen Sie die `next`, `error`, und `complete` Rückrufe an die Benachrichtigungen erhalten, die `stream` Aufruf.

[!code-javascript[Streaming javascript](streaming/sample/wwwroot/js/stream.js?range=19-36)]

::: moniker range="= aspnetcore-2.1"

Um den Datenstrom vom Client zu beenden, rufen die `dispose` Methode für die `ISubscription` zurückgegeben, die von der `subscribe` Methode.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

Um den Datenstrom vom Client zu beenden, rufen die `dispose` Methode für die `ISubscription` zurückgegeben, die von der `subscribe` Methode. Aufrufen dieser Methode bewirkt, dass die `CancellationToken` Parameter der Hub-Methode (sofern Sie vorhanden) abgebrochen werden soll.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"
## <a name="java-client"></a>Java-Client
Der SignalR-Java-Client verwendet die `stream` Methode, um die streaming-Methoden aufrufen. Mindestens drei Argumente akzeptiert:

* Der erwartete Typ der Elemente stream 
* Der Name der hubmethode.
* In der hubmethode definierten Argumente. 

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```
Die `stream` Methode `HubConnection` gibt ein wahrnehmbares Element, für den Elementtyp des Streams. Des Observable Typs `subscribe` Methode ist, definieren Sie Ihre `onNext`, `onError` und `onCompleted` Handler.

::: moniker-end

## <a name="related-resources"></a>Weitere Informationen

* [Hubs](xref:signalr/hubs)
* [.NET-Client](xref:signalr/dotnet-client)
* [JavaScript-Client](xref:signalr/javascript-client)
* [Veröffentlichen in Azure](xref:signalr/publish-to-azure-web-app)

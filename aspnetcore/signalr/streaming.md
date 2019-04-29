---
title: Verwenden Sie in ASP.NET Core SignalR-streaming
author: bradygaster
description: Erfahren Sie, wie Sie Daten zwischen dem Client und Server streamen.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2019
uid: signalr/streaming
ms.openlocfilehash: d185056d3bdda089eaa46ae9b8e13ab7a4354f93
ms.sourcegitcommit: 8a84ce880b4c40d6694ba6423038f18fc2eb5746
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60165075"
---
# <a name="use-streaming-in-aspnet-core-signalr"></a>Verwenden Sie in ASP.NET Core SignalR-streaming

Durch [Brennan Conroy](https://github.com/BrennanConroy)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core SignalR unterstützt das streaming, vom Client zum Server und vom Server zum Client. Dies ist nützlich für Szenarien, in denen Fragmente von Daten im Laufe der Zeit eintreffen. Beim streaming wird jedes Fragment gesendet an den Client oder Server möglichst bald wird es verfügbaren, statt alle Daten auf das Freiwerden warten.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core SignalR unterstützt streaming Rückgabewerte von Servermethoden. Dies ist nützlich für Szenarien, in denen Fragmente von Daten im Laufe der Zeit eintreffen. Wenn ein Wert zurückgegeben, die an den Client gestreamt wird, wird jedes Fragment an den Client gesendet, sobald es wird zur Verfügung, statt alle Daten auf das Freiwerden warten.

::: moniker-end

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="set-up-a-hub-for-streaming"></a>Richten Sie einen Hub für das streaming

::: moniker range=">= aspnetcore-3.0"

Eine hubmethode wird automatisch eine streaming hubmethode, bei der Rückgabe eine <xref:System.Threading.Channels.ChannelReader%601>, `IAsyncEnumerable<T>`, `Task<ChannelReader<T>>`, oder `Task<IAsyncEnumerable<T>>`.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Eine hubmethode wird automatisch eine streaming hubmethode, bei der Rückgabe eine <xref:System.Threading.Channels.ChannelReader%601> oder `Task<ChannelReader<T>>`.

::: moniker-end

### <a name="server-to-client-streaming"></a>Server-zu-Client-streaming

::: moniker range=">= aspnetcore-3.0"

Streaming von Hub-Methoden zurückgeben kann `IAsyncEnumerable<T>` zusätzlich zu `ChannelReader<T>`. Die einfachste Möglichkeit zum Zurückgeben `IAsyncEnumerable<T>` ist, indem Sie der hubmethode eine Async-Iterator-Methode, wie das folgende Beispiel veranschaulicht. Hub-Async-Iterator-Methoden akzeptieren können eine `CancellationToken` Parameter, der ausgelöst wird, wenn der Client aus dem Stream kündigt das Abonnement. Async-Iterator-Methoden Probleme, die mit Kanälen, häufig zu vermeiden, z. B. keine zurückgegeben, die `ChannelReader` noch genügend Zeit oder die Methode wird beendet, ohne abgeschlossen der <xref:System.Threading.Channels.ChannelWriter`1>.

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

Das folgende Beispiel zeigt die Grundlagen von streaming-Daten an den Client mithilfe von Kanälen. Jedes Mal, wenn ein Objekt richtet der <xref:System.Threading.Channels.ChannelWriter%601>, das Objekt sofort an den Client gesendet wird. Am Ende der `ChannelWriter` abgeschlossen ist, um dem Client veranlassen der Stream ist geschlossen.

> [!NOTE]
> Schreiben in die `ChannelWriter<T>` auf einem Hintergrundthread und Rückgabe der `ChannelReader` so bald wie möglich. Andere Hubs Aufrufe werden blockiert, bis eine `ChannelReader` zurückgegeben wird.
>
> Umschließen der Logik in einer `try ... catch`. Abschließen der `Channel` in die `catch` als auch außerhalb der `catch` sicherstellen, dass den Hub Methodenaufruf ordnungsgemäß abgeschlossen wird.

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

Streaming Server-zu-Client-Hub-Methoden akzeptieren können eine `CancellationToken` Parameter, der ausgelöst wird, wenn der Client aus dem Stream kündigt das Abonnement. Verwenden Sie dieses Token beenden Sie den Servervorgang und alle Ressourcen freigeben, wenn der Client vor dem Ende des Streams, der die Verbindung trennt.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>Client-zu-Server-streaming

Eine hubmethode wird automatisch eine Client-zu-Server-streaming hubmethode, wenn sie eine oder mehrere akzeptiert <xref:System.Threading.Channels.ChannelReader`1>s. Das folgende Beispiel zeigt die Grundlagen Lesen von Streamingdaten, die vom Client gesendet werden. Jedes Mal, wenn der Client schreibt in die <xref:System.Threading.Channels.ChannelWriter`1>, in die Daten geschrieben werden die `ChannelReader` auf dem Server, die die hubmethode von der gelesen wird.

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

::: moniker-end

## <a name="net-client"></a>.NET-Client

### <a name="server-to-client-streaming"></a>Server-zu-Client-streaming

Die `StreamAsChannelAsync` Methode `HubConnection` wird verwendet, um eine streaming-Server-zu-Client-Methode aufrufen. Übergeben Sie die Namen der Hub-Methode und die Argumente, die in die hubmethode, die definiert `StreamAsChannelAsync`. Der generische Parameter auf `StreamAsChannelAsync<T>` gibt den Typ der Objekte, die von der streaming-Methode zurückgegeben. Ein `ChannelReader<T>` aus dem Stream-Aufruf zurückgegeben wird, und den Stream darstellt, auf dem Client.

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

### <a name="client-to-server-streaming"></a>Client-zu-Server-streaming

Um eine Client-zu-Server-streaming hubmethode aus der .NET-Client aufrufen, erstellen Sie eine `Channel` und übergeben Sie die `ChannelReader` als Argument an `SendAsync`, `InvokeAsync`, oder `StreamAsChannelAsync`, je nachdem, für die hubmethode aufgerufen.

Jedes Mal, wenn die Daten geschrieben wird, auf die `ChannelWriter`, die hubmethode auf dem Server erhält ein neues Element mit den Daten vom Client.

Um den Datenstrom zu beenden, führen Sie den Kanal mit `channel.Writer.Complete()`.

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a>JavaScript-Client

### <a name="server-to-client-streaming"></a>Server-zu-Client-streaming

Clients mit JavaScript-Hubs mit streaming Server-zu-Client-Methoden aufrufen `connection.stream`. Die `stream` -Methode akzeptiert zwei Argumente:

* Der Name der hubmethode. Im folgenden Beispiel wird der Name der Hub-Methode `Counter`.
* In der hubmethode definierten Argumente. Im folgenden Beispiel werden die Argumente für eine Anzahl für die Anzahl der zu erhalten und die Verzögerung zwischen Stream-Elemente.

`connection.stream` Gibt eine `IStreamResult`, enthält eine `subscribe` Methode. Übergeben einer `IStreamSubscriber` zu `subscribe` und legen Sie die `next`, `error`, und `complete` Rückrufe zum Empfangen von Benachrichtigungen von der `stream` Aufruf.

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

Um den Datenstrom vom Client zu beenden, rufen die `dispose` Methode für die `ISubscription` zurückgegeben, die von der `subscribe` Methode. Das Aufrufen dieser Methode bewirkt, dass Abbruch der `CancellationToken` Parameter der Hub-Methode, wenn Sie eine angegeben.

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

Um den Datenstrom vom Client zu beenden, rufen die `dispose` Methode für die `ISubscription` zurückgegeben, die von der `subscribe` Methode.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>Client-zu-Server-streaming

JavaScript-Clients Client-zu-Server streaming Methoden aufrufen Hubs durch die Übergabe einer `Subject` als Argument an `send`, `invoke`, oder `stream`, je nachdem, für die hubmethode aufgerufen. Die `Subject` ist eine Klasse, der aussieht wie ein `Subject`. Z. B. in RxJS können Sie die [Betreff](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) -Klasse aus dieser Bibliothek.

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

Aufrufen von `subject.next(item)` mit ein Element das Element in den Stream geschrieben werden soll, und die hubmethode empfängt das Element auf dem Server.

Um den Datenstrom zu beenden, rufen `subject.complete()`.

## <a name="java-client"></a>Java-Client

### <a name="server-to-client-streaming"></a>Server-zu-Client-streaming

Der SignalR-Java-Client verwendet die `stream` Methode, um die streaming-Methoden aufrufen. `stream` akzeptiert mindestens drei Argumente:

* Der erwartete Typ der Elemente Stream.
* Der Name der hubmethode.
* In der hubmethode definierten Argumente.

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

Die `stream` Methode `HubConnection` gibt ein wahrnehmbares Element, für den Elementtyp des Streams. Des Observable Typs `subscribe` Methode kommt `onNext`, `onError` und `onCompleted` Handler definiert sind.

::: moniker-end

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Hubs](xref:signalr/hubs)
* [.NET-Client](xref:signalr/dotnet-client)
* [JavaScript-Client](xref:signalr/javascript-client)
* [Veröffentlichen in Azure](xref:signalr/publish-to-azure-web-app)

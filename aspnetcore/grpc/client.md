---
title: GrpC-Dienste mit dem .NET-Client abrufen
author: jamesnk
description: Erfahren Sie, wie Sie GrpC-Dienste mit dem .net-GrpC-Client abrufen.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/21/2019
uid: grpc/client
ms.openlocfilehash: 5518a221c4641ba0d1da051a14750e3165944455
ms.sourcegitcommit: 8b36f75b8931ae3f656e2a8e63572080adc78513
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/05/2019
ms.locfileid: "70310619"
---
# <a name="call-grpc-services-with-the-net-client"></a>GrpC-Dienste mit dem .NET-Client abrufen

Eine .net-GrpC-Client Bibliothek ist im nuget-Paket " [GrpC .net. Client](https://www.nuget.org/packages/Grpc.Net.Client) " verfügbar. In diesem Dokument wird Folgendes erläutert:

* Konfigurieren Sie einen GrpC-Client, um GrpC-Dienste aufzurufen.
* Erstellen von GrpC-Aufrufen von unären, Server Streaming-, Client Streaming-und bidirektionalen Streamingmethoden.

## <a name="configure-grpc-client"></a>Konfigurieren des GrpC-Clients

GrpC-Clients sind konkrete Client Typen, die [aus  *\*. proto* -Dateien generiert](xref:grpc/basics#generated-c-assets)werden. Der konkrete GrpC-Client verfügt über Methoden, die in der Datei "  *\*. proto* " in den GrpC-Dienst übersetzt werden.

Ein GrpC-Client wird über einen Kanal erstellt. Beginnen Sie mit `GrpcChannel.ForAddress` der Verwendung von, um einen Kanal zu erstellen, und verwenden Sie dann den Kanal zum Erstellen eines GrpC-Clients:

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

Ein Kanal stellt eine langlebige Verbindung mit einem GrpC-Dienst dar. Wenn ein Kanal erstellt wird, wird er mit Optionen konfiguriert, die mit dem Aufrufen eines Dienstanbieter verknüpft sind. Beispielsweise können die `HttpClient` , die zum Ausführen von aufrufen, die maximale Größe der Sende-und Empfangs Nachricht und die Protokollierung verwendet werden `GrpcChannel.ForAddress`, in `GrpcChannelOptions` angegeben und mit verwendet werden. Eine umfassende Liste der Optionen finden Sie unter [Client Konfigurationsoptionen](xref:grpc/configuration#configure-client-options).

Das Erstellen eines Kanals kann ein kostspieliger Vorgang sein, und die erneute Verwendung eines Kanals für GrpC-Anrufe bietet Leistungsvorteile. Mehrere konkrete GrpC-Clients können über einen Kanal erstellt werden, einschließlich unterschiedlicher Typen von Clients. Konkrete GrpC-Client Typen sind Lightweight-Objekte und können bei Bedarf erstellt werden.

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

`GrpcChannel.ForAddress`ist nicht die einzige Option zum Erstellen eines GrpC-Clients. Wenn Sie GrpC-Dienste aus einer ASP.net Core-App aufrufen, sollten Sie die [Integration der GrpC-clientfactory](xref:grpc/clientfactory)in Erwägung gezogen. die GrpC- `HttpClientFactory` Integration mit bietet eine zentralisierte Alternative zum Erstellen von GrpC-Clients.

## <a name="make-grpc-calls"></a>Erstellen von GrpC-aufrufen

Ein GrpC-Aufruf wird initiiert, indem eine Methode auf dem Client aufgerufen wird. Der GrpC-Client verarbeitet die Nachrichtenserialisierung und adressiert den GrpC-Rückruf an den richtigen Dienst.

GrpC verfügt über unterschiedliche Arten von Methoden. Wie Sie den Client verwenden, um einen GrpC-Aufruf durchführen zu können, hängt vom Typ der Methode ab, die Sie aufrufen. Die GrpC-Methoden Typen lauten wie folgt:

* Unär
* Server Streaming
* Client Streaming
* Bidirektionales Streaming

### <a name="unary-call"></a>Unärer Befehl

Ein unärer-Befehl beginnt mit dem Client, der eine Anforderungs Nachricht sendet. Eine Antwortmeldung wird zurückgegeben, wenn der Dienst beendet wird.

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

Jede unäre Dienst Methode in der  *\*. proto* -Datei führt zu zwei .NET-Methoden für den konkreten GrpC-Clienttyp zum Aufrufen der-Methode: eine asynchrone Methode und eine blockierende Methode. Beispielsweise können Sie `GreeterClient` auf zwei Arten aufrufen: `SayHello`

* `GreeterClient.SayHelloAsync`-Ruft `Greeter.SayHello` den Dienst asynchron auf. Kann erwartet werden.
* `GreeterClient.SayHello`: Ruft `Greeter.SayHello` den Dienst auf und blockiert bis zum Abschluss. Verwenden Sie in asynchronem Code nicht.

### <a name="server-streaming-call"></a>Serverstreamingaufrufe

Ein serverstreamingvorgang beginnt, wenn der Client eine Anforderungs Nachricht sendet. `ResponseStream.MoveNext()`liest Nachrichten, die vom Dienst gestreamt werden. Der Server Streaming-Rückruf ist beendet `ResponseStream.MoveNext()` , `false`wenn zurückgibt.

```csharp
var client = new Greet.GreeterClient(channel);
using (var call = client.SayHellos(new HelloRequest { Name = "World" }))
{
    while (await call.ResponseStream.MoveNext())
    {
        Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
        // Greeting: Hello World" is streamed multiple times
    }
}
```

Wenn Sie 8 oder C# höher verwenden, kann die `await foreach` Syntax zum Lesen von Nachrichten verwendet werden. Die `IAsyncStreamReader<T>.ReadAllAsync()` Erweiterungsmethode liest alle Nachrichten aus dem Antwortstream:

```csharp
var client = new Greet.GreeterClient(channel);
using (var call = client.SayHellos(new HelloRequest { Name = "World" }))
{
    await foreach (var response in call.ResponseStream.ReadAllAsync())
    {
        Console.WriteLine("Greeting: " + response.Message);
        // "Greeting: Hello World" is streamed multiple times
    }
}
```

### <a name="client-streaming-call"></a>Clientstreamingaufrufe

Ein clientstreaming-Rückruf wird gestartet, *ohne* dass der Client eine Nachricht sendet. Der Client kann das Senden von Nachrichten mit `RequestStream.WriteAsync`senden. Wenn der Client das Senden von nach `RequestStream.CompleteAsync` richten abgeschlossen hat, sollte aufgerufen werden, um den Dienst zu benachrichtigen. Der-Rückruf ist abgeschlossen, wenn der Dienst eine Antwortnachricht zurückgibt.

```csharp
var client = new Counter.CounterClient(channel);
using (var call = client.AccumulateCount())
{
    for (var i = 0; i < 3; i++)
    {
        await call.RequestStream.WriteAsync(new CounterRequest { Count = 1 });
    }
    await call.RequestStream.CompleteAsync();

    var response = await call;
    Console.WriteLine($"Count: {response.Count}");
    // Count: 3
}
```

### <a name="bi-directional-streaming-call"></a>Bidirektionaler Streaming-Befehl

Ein bidirektionaler Streaming-Befehl wird gestartet, *ohne* dass der Client eine Nachricht sendet. Der Client kann Nachrichten mit `RequestStream.WriteAsync`senden. Auf Nachrichten, die vom-Dienst `ResponseStream.MoveNext()` gestreamt werden, kann mit oder `ResponseStream.ReadAllAsync()` Der bidirektionale Streaming-Rückruf ist fertig `ResponseStream` , wenn keine weiteren Nachrichten mehr enthält.

```csharp
using (var call = client.Echo())
{
    Console.WriteLine("Starting background task to receive messages");
    var readTask = Task.Run(async () =>
    {
        await foreach (var response in call.ResponseStream.ReadAllAsync())
        {
            Console.WriteLine(response.Message);
            // Echo messages sent to the service
        }
    });

    Console.WriteLine("Starting to send messages");
    Console.WriteLine("Type a message to echo then press enter.");
    while (true)
    {
        var result = Console.ReadLine();
        if (string.IsNullOrEmpty(result))
        {
            break;
        }

        await call.RequestStream.WriteAsync(new EchoMessage { Message = result });
    }

    Console.WriteLine("Disconnecting");
    await call.RequestStream.CompleteAsync();
    await readTask;
}
```

Während eines bidirektionalen Streaming-Aufrufes können Client und Dienst Nachrichten jederzeit an einander senden. Die beste Client Logik für die Interaktion mit einem bidirektionalen-aufruten hängt von der Dienst Logik ab.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:grpc/clientfactory>
* <xref:grpc/basics>

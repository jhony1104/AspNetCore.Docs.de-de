---
title: GrpC-Dienste mit dem .NET-Client abrufen
author: jamesnk
description: Erfahren Sie, wie Sie GrpC-Dienste mit dem .net-GrpC-Client abrufen.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/21/2019
uid: grpc/client
ms.openlocfilehash: 1e7887388a752fb35d00e65db210c3924c6ab192
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75829100"
---
# <a name="call-grpc-services-with-the-net-client"></a>GrpC-Dienste mit dem .NET-Client abrufen

Eine .net-GrpC-Client Bibliothek ist im nuget-Paket " [GrpC .net. Client](https://www.nuget.org/packages/Grpc.Net.Client) " verfügbar. In diesem Dokument wird Folgendes erläutert:

* Konfigurieren Sie einen GrpC-Client, um GrpC-Dienste aufzurufen.
* Erstellen von GrpC-Aufrufen von unären, Server Streaming-, Client Streaming-und bidirektionalen Streamingmethoden.

## <a name="configure-grpc-client"></a>Konfigurieren des GrpC-Clients

gRPC-Clients sind konkrete Clienttypen, [die aus *\*.proto*Dateien generiert werden](xref:grpc/basics#generated-c-assets). Der konkrete gRPC-Client verfügt über Methoden, die in den gRPC-Dienst in der *\*.proto*-Datei übersetzt werden.

Ein GrpC-Client wird über einen Kanal erstellt. Beginnen Sie mit `GrpcChannel.ForAddress`, um einen Kanal zu erstellen, und verwenden Sie dann den Kanal, um einen GrpC-Client zu erstellen:

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

Ein Kanal stellt eine langlebige Verbindung mit einem GrpC-Dienst dar. Wenn ein Kanal erstellt wird, wird er mit Optionen konfiguriert, die mit dem Aufrufen eines Dienstanbieter verknüpft sind. Beispielsweise können die `HttpClient`, die für Aufrufe, die maximale Größe der Sende-und Empfangs Nachricht und die Protokollierung verwendet werden, auf `GrpcChannelOptions` angegeben und mit `GrpcChannel.ForAddress`verwendet werden. Eine umfassende Liste der Optionen finden Sie unter [Client Konfigurationsoptionen](xref:grpc/configuration#configure-client-options).

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

Leistung und Nutzung von Kanälen und Clients:

* Das Erstellen eines Kanals kann ein kostspieliger Vorgang sein. Die erneute Verwendung eines Kanals für GrpC-Anrufe bietet Leistungsvorteile.
* GrpC-Clients werden mit Kanälen erstellt. GrpC-Clients sind Lightweight-Objekte und müssen nicht zwischengespeichert oder wieder verwendet werden.
* Mehrere GrpC-Clients können über einen Kanal erstellt werden, einschließlich unterschiedlicher Typen von Clients.
* Ein Kanal und Clients, die über den Kanal erstellt wurden, können von mehreren Threads sicher verwendet werden.
* Clients, die über den Kanal erstellt werden, können mehrere gleichzeitige Aufrufe ausführen.

`GrpcChannel.ForAddress` ist nicht die einzige Option zum Erstellen eines GrpC-Clients. Wenn Sie GrpC-Dienste aus einer ASP.net Core-App aufrufen, sollten Sie die [Integration der GrpC-Client Factory](xref:grpc/clientfactory)in Erwägung gezogen. die GrpC-Integration mit `HttpClientFactory` bietet eine zentralisierte Alternative zum Erstellen von GrpC-Clients.

> [!NOTE]
> Zum [Abrufen unsicherer GrpC-Dienste mit dem .NET-Client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client)ist eine zusätzliche Konfiguration erforderlich.

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

Jede unäre Dienst Methode in der Datei " *\*. proto* " führt zum Aufrufen der-Methode zwei .NET-Methoden auf dem konkreten GrpC-Clienttyp: eine asynchrone Methode und eine blockierende Methode. Beispielsweise können Sie auf `GreeterClient` auf zwei Arten `SayHello`aufrufen:

* `GreeterClient.SayHelloAsync` ruft `Greeter.SayHello` Dienst asynchron auf. Kann erwartet werden.
* `GreeterClient.SayHello` ruft `Greeter.SayHello` Dienst auf und blockiert, bis der Vorgang beendet ist. Verwenden Sie in asynchronem Code nicht.

### <a name="server-streaming-call"></a>Serverstreamingaufrufe

Ein serverstreamingvorgang beginnt, wenn der Client eine Anforderungs Nachricht sendet. `ResponseStream.MoveNext()` liest Nachrichten, die vom Dienst gestreamt werden. Der Server Streaming-Rückruf ist fertiggestellt, wenn `ResponseStream.MoveNext()` `false`zurückgibt.

```csharp
var client = new Greet.GreeterClient(channel);
using (var call = client.SayHellos(new HelloRequest { Name = "World" }))
{
    while (await call.ResponseStream.MoveNext())
    {
        Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
        // "Greeting: Hello World" is written multiple times
    }
}
```

Wenn Sie 8 oder C# höher verwenden, kann die `await foreach` Syntax zum Lesen von Nachrichten verwendet werden. Mit der `IAsyncStreamReader<T>.ReadAllAsync()`-Erweiterungsmethode werden alle Nachrichten aus dem Antwortstream gelesen:

```csharp
var client = new Greet.GreeterClient(channel);
using (var call = client.SayHellos(new HelloRequest { Name = "World" }))
{
    await foreach (var response in call.ResponseStream.ReadAllAsync())
    {
        Console.WriteLine("Greeting: " + response.Message);
        // "Greeting: Hello World" is written multiple times
    }
}
```

### <a name="client-streaming-call"></a>Clientstreamingaufrufe

Ein clientstreaming-Rückruf wird gestartet, *ohne* dass der Client eine Nachricht sendet. Der Client kann auswählen, ob Nachrichten mit `RequestStream.WriteAsync`gesendet werden sollen. Wenn der Client das Senden von Nachrichten abgeschlossen hat `RequestStream.CompleteAsync` sollte aufgerufen werden, um den Dienst zu benachrichtigen. Der-Rückruf ist abgeschlossen, wenn der Dienst eine Antwortnachricht zurückgibt.

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

Ein bidirektionaler Streaming-Befehl wird gestartet, *ohne* dass der Client eine Nachricht sendet. Der Client kann auswählen, ob Nachrichten mit `RequestStream.WriteAsync`gesendet werden sollen. Der Zugriff auf Nachrichten, die vom Dienst gestreamt werden, kann mit `ResponseStream.MoveNext()` oder `ResponseStream.ReadAllAsync()` Der bidirektionale Streaming-Aufrufvorgang ist beendet, wenn der `ResponseStream` keine weiteren Nachrichten mehr enthält.

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

## <a name="additional-resources"></a>Weitere Ressourcen

* <xref:grpc/clientfactory>
* <xref:grpc/basics>

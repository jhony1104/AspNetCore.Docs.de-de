---
title: Aufrufen von gRPC-Diensten mithilfe eines .NET-Clients
author: jamesnk
description: Hier erfahren Sie, wie Sie gRPC-Dienste mit dem .NET-gRPC-Client aufrufen.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/21/2019
uid: grpc/client
ms.openlocfilehash: 6a6a649f7194354b16f3d67160be02428cc01170
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/06/2020
ms.locfileid: "78650803"
---
# <a name="call-grpc-services-with-the-net-client"></a>Aufrufen von gRPC-Diensten mithilfe eines .NET-Clients

Im NuGet-Paket [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) ist eine .NET-gRPC-Clientbibliothek verfügbar. Im vorliegenden Dokument wird Folgendes erläutert:

* Konfigurieren eines gRPC-Clients, um gRPC-Dienste aufzurufen
* Durchführen von gRPC-Aufrufe von unären, Serverstreaming- und Clientstreamingmethoden sowie von Methoden für bidirektionales Streaming.

## <a name="configure-grpc-client"></a>Konfigurieren eines gRPC-Clients

gRPC-Clients sind konkrete Clienttypen, [die aus *\*.proto*Dateien generiert werden](xref:grpc/basics#generated-c-assets). Der konkrete gRPC-Client verfügt über Methoden, die in den gRPC-Dienst in der *\*.proto*-Datei übersetzt werden.

Ein gRPC-Client wird aus einem Kanal erstellt. Starten Sie das Erstellen eines Kanals mithilfe von `GrpcChannel.ForAddress`, und verwenden Sie dann den Kanal, um einen gRPC-Client zu erstellen:

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

Ein Kanal steht für eine langlebige Verbindung zu einem gRPC-Dienst. Wenn ein Kanal erstellt wird, wird er mit Optionen zum Aufrufen eines Diensts konfiguriert. Die für Aufrufe verwendete Klasse `HttpClient`, die maximale Größe für gesendete und empfange Nachrichten sowie die Protokollierung können in der Klasse `GrpcChannelOptions` angegeben und mit `GrpcChannel.ForAddress` verwendet werden. Eine vollständige Liste der Optionen finden Sie unter [Konfigurieren von Clientoptionen](xref:grpc/configuration#configure-client-options).

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

Kanal- und Clientleistung und -nutzung:

* Das Erstellen eines Kanals kann ein kostspieliger Vorgang sein. Wenn ein Kanal für gRPC-Aufrufe wiederverwendet wird, stehen Leistungsvorteile zur Verfügung.
* gRPC-Clients werden mit Kanälen erstellt. Bei gRPC-Clients handelt es sich um Lightweightobjekte. Sie müssen nicht zwischengespeichert oder wiederverwendet werden.
* Aus einem Kanal können mehrere gRPC-Clients erstellt werden, einschließlich verschiedener Clienttypen.
* Ein Kanal und Clients, die aus dem Kanal erstellt wurden, können sicher von mehreren Threads verwendet werden.
* Clients, die aus dem Kanal erstellt wurden, können mehrere gleichzeitige Aufrufe durchführen.

`GrpcChannel.ForAddress` ist nicht die einzige Option für das Erstellen eines gRPC-Clients. Wenn Sie gRPC-Dienste in einer ASP.NET Core-App aufrufen, sollten Sie die [Integration der gRPC-Clientfactory](xref:grpc/clientfactory) in Erwägung ziehen. Die gRPC-Integration mit `HttpClientFactory` bietet eine zentralisierte Alternative zur Erstellung von gRPC-Clients.

> [!NOTE]
> Wenn Sie [unsichere gRPC-Dienste mit dem .NET-Client aufrufen](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client) möchten, sind weitere Konfigurationen erforderlich.

> [!NOTE]
> Das Aufrufen von gRPC über HTTP/2 mit `Grpc.Net.Client` wird in Xamarin aktuell nicht unterstützt. Die HTTP/2-Unterstützung soll jedoch in einem zukünftigen Release von Xamarin optimiert werden. [Grpc.Core](https://www.nuget.org/packages/Grpc.Core) und [gRPC-Web](xref:grpc/browser) sind funktionsfähige Alternativen, die aktuell verwendet werden können.

## <a name="make-grpc-calls"></a>Durchführen von gRPC-Aufrufen

Ein gRPC-Aufruf wird initiiert, indem eine Methode auf dem Client aufgerufen wird. Der gRPC-Client verarbeitet die Nachrichtenserialisierung und leitet den gRPC-Aufruf an den entsprechenden Dienst weiter.

Bei gRPC stehen verschiedene Methodentypen zur Verfügung. Wie der Client verwendet wird, um einen gRPC-Aufruf durchzuführen, hängt vom Typ der aufgerufenen Methode ab. Es gibt die folgenden gRPC-Methodentypen:

* Unär
* Serverstreaming
* Clientstreaming
* Bidirektionales Streaming

### <a name="unary-call"></a>Unärer Aufruf

Ein unärer Aufruf beginnt damit, dass der Client eine Anforderungsnachricht sendet. Sobald der Dienst abgeschlossen ist, wird eine Antwortnachricht zurückgegeben.

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

Alle unären Dienstmethoden in der Datei *\*.proto* führen zu zwei .NET-Methoden auf dem konkreten gRPC-Clienttyp, um die Methode aufzurufen: eine asynchrone Methode und eine blockierende Methode. Bei `GreeterClient` gibt es beispielsweise zwei Möglichkeiten, `SayHello` aufzurufen:

* `GreeterClient.SayHelloAsync` ruft den `Greeter.SayHello`-Dienst asynchron auf und kann erwartet werden.
* `GreeterClient.SayHello` ruft den `Greeter.SayHello`-Dienst auf und blockiert, bis der Vorgang abgeschlossen ist. Verwenden Sie diese Möglichkeit nicht für asynchronen Code.

### <a name="server-streaming-call"></a>Serverstreamingaufruf

Ein Serverstreamingaufruf beginnt damit, dass der Client eine Anforderungsnachricht sendet. `ResponseStream.MoveNext()` liest Nachrichten, die vom Dienst gestreamt werden. Der Serverstreamingaufruf ist abgeschlossen, wenn `ResponseStream.MoveNext()` `false` zurückgibt.

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

Wenn Sie C# 8 oder höher verwenden, kann die `await foreach`-Syntax verwendet werden, um Nachrichten zu lesen. Die `IAsyncStreamReader<T>.ReadAllAsync()`-Erweiterungsmethode liest alle Nachrichten aus dem Antwortstream:

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

### <a name="client-streaming-call"></a>Clientstreamingaufruf

Ein Clientstreamingaufruf beginnt, *ohne* dass der Client eine Nachricht sendet. Der Client kann Nachrichten mit `RequestStream.WriteAsync` senden. Wenn der Client das Senden von Nachrichten abgeschlossen hat, sollte `RequestStream.CompleteAsync` aufgerufen werden, damit der Dienst benachrichtigt wird. Der Aufruf ist abgeschlossen, wenn der Dienst eine Antwortnachricht zurückgibt.

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

### <a name="bi-directional-streaming-call"></a>Aufruf von bidirektionalem Streaming

Ein Aufruf für bidirektionales Streaming beginnt, *ohne* dass der Client eine Nachricht sendet. Der Client kann Nachrichten mit `RequestStream.WriteAsync` senden. Auf vom Dienst gestreamte Nachrichten kann mit `ResponseStream.MoveNext()` oder `ResponseStream.ReadAllAsync()` zugegriffen werden. Der Aufruf für bidirektionales Streaming ist abgeschlossen, wenn `ResponseStream` keine weiteren Nachrichten mehr hat.

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

Während des Aufrufs von bidirektionalem Streaming können sich Client und Dienst jederzeit gegenseitig Nachrichten senden. Die beste Clientlogik für die Interaktion mit einem bidirektionalem Aufruf variiert je nach Dienstlogik.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:grpc/clientfactory>
* <xref:grpc/basics>

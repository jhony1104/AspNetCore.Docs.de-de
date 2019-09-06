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
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="73776-103">GrpC-Dienste mit dem .NET-Client abrufen</span><span class="sxs-lookup"><span data-stu-id="73776-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="73776-104">Eine .net-GrpC-Client Bibliothek ist im nuget-Paket " [GrpC .net. Client](https://www.nuget.org/packages/Grpc.Net.Client) " verfügbar.</span><span class="sxs-lookup"><span data-stu-id="73776-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="73776-105">In diesem Dokument wird Folgendes erläutert:</span><span class="sxs-lookup"><span data-stu-id="73776-105">This document explains how to:</span></span>

* <span data-ttu-id="73776-106">Konfigurieren Sie einen GrpC-Client, um GrpC-Dienste aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="73776-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="73776-107">Erstellen von GrpC-Aufrufen von unären, Server Streaming-, Client Streaming-und bidirektionalen Streamingmethoden.</span><span class="sxs-lookup"><span data-stu-id="73776-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="73776-108">Konfigurieren des GrpC-Clients</span><span class="sxs-lookup"><span data-stu-id="73776-108">Configure gRPC client</span></span>

<span data-ttu-id="73776-109">GrpC-Clients sind konkrete Client Typen, die [aus  *\*. proto* -Dateien generiert](xref:grpc/basics#generated-c-assets)werden.</span><span class="sxs-lookup"><span data-stu-id="73776-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="73776-110">Der konkrete GrpC-Client verfügt über Methoden, die in der Datei "  *\*. proto* " in den GrpC-Dienst übersetzt werden.</span><span class="sxs-lookup"><span data-stu-id="73776-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

<span data-ttu-id="73776-111">Ein GrpC-Client wird über einen Kanal erstellt.</span><span class="sxs-lookup"><span data-stu-id="73776-111">A gRPC client is created from a channel.</span></span> <span data-ttu-id="73776-112">Beginnen Sie mit `GrpcChannel.ForAddress` der Verwendung von, um einen Kanal zu erstellen, und verwenden Sie dann den Kanal zum Erstellen eines GrpC-Clients:</span><span class="sxs-lookup"><span data-stu-id="73776-112">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="73776-113">Ein Kanal stellt eine langlebige Verbindung mit einem GrpC-Dienst dar.</span><span class="sxs-lookup"><span data-stu-id="73776-113">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="73776-114">Wenn ein Kanal erstellt wird, wird er mit Optionen konfiguriert, die mit dem Aufrufen eines Dienstanbieter verknüpft sind.</span><span class="sxs-lookup"><span data-stu-id="73776-114">When a channel is created it is configured with options related to calling a service.</span></span> <span data-ttu-id="73776-115">Beispielsweise können die `HttpClient` , die zum Ausführen von aufrufen, die maximale Größe der Sende-und Empfangs Nachricht und die Protokollierung verwendet werden `GrpcChannel.ForAddress`, in `GrpcChannelOptions` angegeben und mit verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="73776-115">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="73776-116">Eine umfassende Liste der Optionen finden Sie unter [Client Konfigurationsoptionen](xref:grpc/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="73776-116">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

<span data-ttu-id="73776-117">Das Erstellen eines Kanals kann ein kostspieliger Vorgang sein, und die erneute Verwendung eines Kanals für GrpC-Anrufe bietet Leistungsvorteile.</span><span class="sxs-lookup"><span data-stu-id="73776-117">Creating a channel can be an expensive operation and reusing a channel for gRPC calls offers performance benefits.</span></span> <span data-ttu-id="73776-118">Mehrere konkrete GrpC-Clients können über einen Kanal erstellt werden, einschließlich unterschiedlicher Typen von Clients.</span><span class="sxs-lookup"><span data-stu-id="73776-118">Multiple concrete gRPC clients can be created from a channel, including different types of clients.</span></span> <span data-ttu-id="73776-119">Konkrete GrpC-Client Typen sind Lightweight-Objekte und können bei Bedarf erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="73776-119">Concrete gRPC client types are lightweight objects and can be created when needed.</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

<span data-ttu-id="73776-120">`GrpcChannel.ForAddress`ist nicht die einzige Option zum Erstellen eines GrpC-Clients.</span><span class="sxs-lookup"><span data-stu-id="73776-120">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="73776-121">Wenn Sie GrpC-Dienste aus einer ASP.net Core-App aufrufen, sollten Sie die [Integration der GrpC-clientfactory](xref:grpc/clientfactory)in Erwägung gezogen.</span><span class="sxs-lookup"><span data-stu-id="73776-121">If you are calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="73776-122">die GrpC- `HttpClientFactory` Integration mit bietet eine zentralisierte Alternative zum Erstellen von GrpC-Clients.</span><span class="sxs-lookup"><span data-stu-id="73776-122">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="73776-123">Erstellen von GrpC-aufrufen</span><span class="sxs-lookup"><span data-stu-id="73776-123">Make gRPC calls</span></span>

<span data-ttu-id="73776-124">Ein GrpC-Aufruf wird initiiert, indem eine Methode auf dem Client aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="73776-124">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="73776-125">Der GrpC-Client verarbeitet die Nachrichtenserialisierung und adressiert den GrpC-Rückruf an den richtigen Dienst.</span><span class="sxs-lookup"><span data-stu-id="73776-125">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="73776-126">GrpC verfügt über unterschiedliche Arten von Methoden.</span><span class="sxs-lookup"><span data-stu-id="73776-126">gRPC has different types of methods.</span></span> <span data-ttu-id="73776-127">Wie Sie den Client verwenden, um einen GrpC-Aufruf durchführen zu können, hängt vom Typ der Methode ab, die Sie aufrufen.</span><span class="sxs-lookup"><span data-stu-id="73776-127">How you use the client to make a gRPC call depends on the type of method you are calling.</span></span> <span data-ttu-id="73776-128">Die GrpC-Methoden Typen lauten wie folgt:</span><span class="sxs-lookup"><span data-stu-id="73776-128">The gRPC method types are:</span></span>

* <span data-ttu-id="73776-129">Unär</span><span class="sxs-lookup"><span data-stu-id="73776-129">Unary</span></span>
* <span data-ttu-id="73776-130">Server Streaming</span><span class="sxs-lookup"><span data-stu-id="73776-130">Server streaming</span></span>
* <span data-ttu-id="73776-131">Client Streaming</span><span class="sxs-lookup"><span data-stu-id="73776-131">Client streaming</span></span>
* <span data-ttu-id="73776-132">Bidirektionales Streaming</span><span class="sxs-lookup"><span data-stu-id="73776-132">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="73776-133">Unärer Befehl</span><span class="sxs-lookup"><span data-stu-id="73776-133">Unary call</span></span>

<span data-ttu-id="73776-134">Ein unärer-Befehl beginnt mit dem Client, der eine Anforderungs Nachricht sendet.</span><span class="sxs-lookup"><span data-stu-id="73776-134">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="73776-135">Eine Antwortmeldung wird zurückgegeben, wenn der Dienst beendet wird.</span><span class="sxs-lookup"><span data-stu-id="73776-135">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="73776-136">Jede unäre Dienst Methode in der  *\*. proto* -Datei führt zu zwei .NET-Methoden für den konkreten GrpC-Clienttyp zum Aufrufen der-Methode: eine asynchrone Methode und eine blockierende Methode.</span><span class="sxs-lookup"><span data-stu-id="73776-136">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="73776-137">Beispielsweise können Sie `GreeterClient` auf zwei Arten aufrufen: `SayHello`</span><span class="sxs-lookup"><span data-stu-id="73776-137">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="73776-138">`GreeterClient.SayHelloAsync`-Ruft `Greeter.SayHello` den Dienst asynchron auf.</span><span class="sxs-lookup"><span data-stu-id="73776-138">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="73776-139">Kann erwartet werden.</span><span class="sxs-lookup"><span data-stu-id="73776-139">Can be awaited.</span></span>
* <span data-ttu-id="73776-140">`GreeterClient.SayHello`: Ruft `Greeter.SayHello` den Dienst auf und blockiert bis zum Abschluss.</span><span class="sxs-lookup"><span data-stu-id="73776-140">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="73776-141">Verwenden Sie in asynchronem Code nicht.</span><span class="sxs-lookup"><span data-stu-id="73776-141">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="73776-142">Serverstreamingaufrufe</span><span class="sxs-lookup"><span data-stu-id="73776-142">Server streaming call</span></span>

<span data-ttu-id="73776-143">Ein serverstreamingvorgang beginnt, wenn der Client eine Anforderungs Nachricht sendet.</span><span class="sxs-lookup"><span data-stu-id="73776-143">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="73776-144">`ResponseStream.MoveNext()`liest Nachrichten, die vom Dienst gestreamt werden.</span><span class="sxs-lookup"><span data-stu-id="73776-144">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="73776-145">Der Server Streaming-Rückruf ist beendet `ResponseStream.MoveNext()` , `false`wenn zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="73776-145">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

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

<span data-ttu-id="73776-146">Wenn Sie 8 oder C# höher verwenden, kann die `await foreach` Syntax zum Lesen von Nachrichten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="73776-146">If you are using C# 8 or later then the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="73776-147">Die `IAsyncStreamReader<T>.ReadAllAsync()` Erweiterungsmethode liest alle Nachrichten aus dem Antwortstream:</span><span class="sxs-lookup"><span data-stu-id="73776-147">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

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

### <a name="client-streaming-call"></a><span data-ttu-id="73776-148">Clientstreamingaufrufe</span><span class="sxs-lookup"><span data-stu-id="73776-148">Client streaming call</span></span>

<span data-ttu-id="73776-149">Ein clientstreaming-Rückruf wird gestartet, *ohne* dass der Client eine Nachricht sendet.</span><span class="sxs-lookup"><span data-stu-id="73776-149">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="73776-150">Der Client kann das Senden von Nachrichten mit `RequestStream.WriteAsync`senden.</span><span class="sxs-lookup"><span data-stu-id="73776-150">The client can choose to send sends messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="73776-151">Wenn der Client das Senden von nach `RequestStream.CompleteAsync` richten abgeschlossen hat, sollte aufgerufen werden, um den Dienst zu benachrichtigen.</span><span class="sxs-lookup"><span data-stu-id="73776-151">When the client has finished sending messages `RequestStream.CompleteAsync` should be called to notify the service.</span></span> <span data-ttu-id="73776-152">Der-Rückruf ist abgeschlossen, wenn der Dienst eine Antwortnachricht zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="73776-152">The call is finished when the service returns a response message.</span></span>

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

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="73776-153">Bidirektionaler Streaming-Befehl</span><span class="sxs-lookup"><span data-stu-id="73776-153">Bi-directional streaming call</span></span>

<span data-ttu-id="73776-154">Ein bidirektionaler Streaming-Befehl wird gestartet, *ohne* dass der Client eine Nachricht sendet.</span><span class="sxs-lookup"><span data-stu-id="73776-154">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="73776-155">Der Client kann Nachrichten mit `RequestStream.WriteAsync`senden.</span><span class="sxs-lookup"><span data-stu-id="73776-155">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="73776-156">Auf Nachrichten, die vom-Dienst `ResponseStream.MoveNext()` gestreamt werden, kann mit oder `ResponseStream.ReadAllAsync()`</span><span class="sxs-lookup"><span data-stu-id="73776-156">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="73776-157">Der bidirektionale Streaming-Rückruf ist fertig `ResponseStream` , wenn keine weiteren Nachrichten mehr enthält.</span><span class="sxs-lookup"><span data-stu-id="73776-157">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

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

<span data-ttu-id="73776-158">Während eines bidirektionalen Streaming-Aufrufes können Client und Dienst Nachrichten jederzeit an einander senden.</span><span class="sxs-lookup"><span data-stu-id="73776-158">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="73776-159">Die beste Client Logik für die Interaktion mit einem bidirektionalen-aufruten hängt von der Dienst Logik ab.</span><span class="sxs-lookup"><span data-stu-id="73776-159">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="73776-160">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="73776-160">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/basics>

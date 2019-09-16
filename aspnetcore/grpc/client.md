---
title: GrpC-Dienste mit dem .NET-Client abrufen
author: jamesnk
description: Erfahren Sie, wie Sie GrpC-Dienste mit dem .net-GrpC-Client abrufen.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/21/2019
uid: grpc/client
ms.openlocfilehash: 56f79b303a8d53699e8eb6156d328c0da1259416
ms.sourcegitcommit: dc5b293e08336dc236de66ed1834f7ef78359531
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/16/2019
ms.locfileid: "71011135"
---
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="a742f-103">GrpC-Dienste mit dem .NET-Client abrufen</span><span class="sxs-lookup"><span data-stu-id="a742f-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="a742f-104">Eine .net-GrpC-Client Bibliothek ist im nuget-Paket " [GrpC .net. Client](https://www.nuget.org/packages/Grpc.Net.Client) " verfügbar.</span><span class="sxs-lookup"><span data-stu-id="a742f-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="a742f-105">In diesem Dokument wird Folgendes erläutert:</span><span class="sxs-lookup"><span data-stu-id="a742f-105">This document explains how to:</span></span>

* <span data-ttu-id="a742f-106">Konfigurieren Sie einen GrpC-Client, um GrpC-Dienste aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="a742f-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="a742f-107">Erstellen von GrpC-Aufrufen von unären, Server Streaming-, Client Streaming-und bidirektionalen Streamingmethoden.</span><span class="sxs-lookup"><span data-stu-id="a742f-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="a742f-108">Konfigurieren des GrpC-Clients</span><span class="sxs-lookup"><span data-stu-id="a742f-108">Configure gRPC client</span></span>

<span data-ttu-id="a742f-109">GrpC-Clients sind konkrete Client Typen, die [aus  *\*. proto* -Dateien generiert](xref:grpc/basics#generated-c-assets)werden.</span><span class="sxs-lookup"><span data-stu-id="a742f-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="a742f-110">Der konkrete GrpC-Client verfügt über Methoden, die in der Datei "  *\*. proto* " in den GrpC-Dienst übersetzt werden.</span><span class="sxs-lookup"><span data-stu-id="a742f-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

<span data-ttu-id="a742f-111">Ein GrpC-Client wird über einen Kanal erstellt.</span><span class="sxs-lookup"><span data-stu-id="a742f-111">A gRPC client is created from a channel.</span></span> <span data-ttu-id="a742f-112">Beginnen Sie mit `GrpcChannel.ForAddress` der Verwendung von, um einen Kanal zu erstellen, und verwenden Sie dann den Kanal zum Erstellen eines GrpC-Clients:</span><span class="sxs-lookup"><span data-stu-id="a742f-112">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="a742f-113">Ein Kanal stellt eine langlebige Verbindung mit einem GrpC-Dienst dar.</span><span class="sxs-lookup"><span data-stu-id="a742f-113">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="a742f-114">Wenn ein Kanal erstellt wird, wird er mit Optionen konfiguriert, die mit dem Aufrufen eines Dienstanbieter verknüpft sind.</span><span class="sxs-lookup"><span data-stu-id="a742f-114">When a channel is created it is configured with options related to calling a service.</span></span> <span data-ttu-id="a742f-115">Beispielsweise können die `HttpClient` , die zum Ausführen von aufrufen, die maximale Größe der Sende-und Empfangs Nachricht und die Protokollierung verwendet werden `GrpcChannel.ForAddress`, in `GrpcChannelOptions` angegeben und mit verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="a742f-115">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="a742f-116">Eine umfassende Liste der Optionen finden Sie unter [Client Konfigurationsoptionen](xref:grpc/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="a742f-116">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

<span data-ttu-id="a742f-117">Das Erstellen eines Kanals kann ein kostspieliger Vorgang sein, und die erneute Verwendung eines Kanals für GrpC-Anrufe bietet Leistungsvorteile.</span><span class="sxs-lookup"><span data-stu-id="a742f-117">Creating a channel can be an expensive operation and reusing a channel for gRPC calls offers performance benefits.</span></span> <span data-ttu-id="a742f-118">Mehrere konkrete GrpC-Clients können über einen Kanal erstellt werden, einschließlich unterschiedlicher Typen von Clients.</span><span class="sxs-lookup"><span data-stu-id="a742f-118">Multiple concrete gRPC clients can be created from a channel, including different types of clients.</span></span> <span data-ttu-id="a742f-119">Konkrete GrpC-Client Typen sind Lightweight-Objekte und können bei Bedarf erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="a742f-119">Concrete gRPC client types are lightweight objects and can be created when needed.</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

<span data-ttu-id="a742f-120">`GrpcChannel.ForAddress`ist nicht die einzige Option zum Erstellen eines GrpC-Clients.</span><span class="sxs-lookup"><span data-stu-id="a742f-120">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="a742f-121">Wenn Sie GrpC-Dienste aus einer ASP.net Core-App aufrufen, sollten Sie die [Integration der GrpC-clientfactory](xref:grpc/clientfactory)in Erwägung gezogen.</span><span class="sxs-lookup"><span data-stu-id="a742f-121">If you are calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="a742f-122">die GrpC- `HttpClientFactory` Integration mit bietet eine zentralisierte Alternative zum Erstellen von GrpC-Clients.</span><span class="sxs-lookup"><span data-stu-id="a742f-122">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

> [!NOTE]
> <span data-ttu-id="a742f-123">Zum [Abrufen unsicherer GrpC-Dienste mit dem .NET-Client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client)ist eine zusätzliche Konfiguration erforderlich.</span><span class="sxs-lookup"><span data-stu-id="a742f-123">Additional configuration is required to [call insecure gRPC services with the .NET client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="a742f-124">Erstellen von GrpC-aufrufen</span><span class="sxs-lookup"><span data-stu-id="a742f-124">Make gRPC calls</span></span>

<span data-ttu-id="a742f-125">Ein GrpC-Aufruf wird initiiert, indem eine Methode auf dem Client aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="a742f-125">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="a742f-126">Der GrpC-Client verarbeitet die Nachrichtenserialisierung und adressiert den GrpC-Rückruf an den richtigen Dienst.</span><span class="sxs-lookup"><span data-stu-id="a742f-126">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="a742f-127">GrpC verfügt über unterschiedliche Arten von Methoden.</span><span class="sxs-lookup"><span data-stu-id="a742f-127">gRPC has different types of methods.</span></span> <span data-ttu-id="a742f-128">Wie Sie den Client verwenden, um einen GrpC-Aufruf durchführen zu können, hängt vom Typ der Methode ab, die Sie aufrufen.</span><span class="sxs-lookup"><span data-stu-id="a742f-128">How you use the client to make a gRPC call depends on the type of method you are calling.</span></span> <span data-ttu-id="a742f-129">Die GrpC-Methoden Typen lauten wie folgt:</span><span class="sxs-lookup"><span data-stu-id="a742f-129">The gRPC method types are:</span></span>

* <span data-ttu-id="a742f-130">Unär</span><span class="sxs-lookup"><span data-stu-id="a742f-130">Unary</span></span>
* <span data-ttu-id="a742f-131">Server Streaming</span><span class="sxs-lookup"><span data-stu-id="a742f-131">Server streaming</span></span>
* <span data-ttu-id="a742f-132">Client Streaming</span><span class="sxs-lookup"><span data-stu-id="a742f-132">Client streaming</span></span>
* <span data-ttu-id="a742f-133">Bidirektionales Streaming</span><span class="sxs-lookup"><span data-stu-id="a742f-133">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="a742f-134">Unärer Befehl</span><span class="sxs-lookup"><span data-stu-id="a742f-134">Unary call</span></span>

<span data-ttu-id="a742f-135">Ein unärer-Befehl beginnt mit dem Client, der eine Anforderungs Nachricht sendet.</span><span class="sxs-lookup"><span data-stu-id="a742f-135">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="a742f-136">Eine Antwortmeldung wird zurückgegeben, wenn der Dienst beendet wird.</span><span class="sxs-lookup"><span data-stu-id="a742f-136">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="a742f-137">Jede unäre Dienst Methode in der  *\*. proto* -Datei führt zu zwei .NET-Methoden für den konkreten GrpC-Clienttyp zum Aufrufen der-Methode: eine asynchrone Methode und eine blockierende Methode.</span><span class="sxs-lookup"><span data-stu-id="a742f-137">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="a742f-138">Beispielsweise können Sie `GreeterClient` auf zwei Arten aufrufen: `SayHello`</span><span class="sxs-lookup"><span data-stu-id="a742f-138">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="a742f-139">`GreeterClient.SayHelloAsync`-Ruft `Greeter.SayHello` den Dienst asynchron auf.</span><span class="sxs-lookup"><span data-stu-id="a742f-139">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="a742f-140">Kann erwartet werden.</span><span class="sxs-lookup"><span data-stu-id="a742f-140">Can be awaited.</span></span>
* <span data-ttu-id="a742f-141">`GreeterClient.SayHello`: Ruft `Greeter.SayHello` den Dienst auf und blockiert bis zum Abschluss.</span><span class="sxs-lookup"><span data-stu-id="a742f-141">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="a742f-142">Verwenden Sie in asynchronem Code nicht.</span><span class="sxs-lookup"><span data-stu-id="a742f-142">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="a742f-143">Serverstreamingaufrufe</span><span class="sxs-lookup"><span data-stu-id="a742f-143">Server streaming call</span></span>

<span data-ttu-id="a742f-144">Ein serverstreamingvorgang beginnt, wenn der Client eine Anforderungs Nachricht sendet.</span><span class="sxs-lookup"><span data-stu-id="a742f-144">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="a742f-145">`ResponseStream.MoveNext()`liest Nachrichten, die vom Dienst gestreamt werden.</span><span class="sxs-lookup"><span data-stu-id="a742f-145">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="a742f-146">Der Server Streaming-Rückruf ist beendet `ResponseStream.MoveNext()` , `false`wenn zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="a742f-146">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

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

<span data-ttu-id="a742f-147">Wenn Sie 8 oder C# höher verwenden, kann die `await foreach` Syntax zum Lesen von Nachrichten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="a742f-147">If you are using C# 8 or later then the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="a742f-148">Die `IAsyncStreamReader<T>.ReadAllAsync()` Erweiterungsmethode liest alle Nachrichten aus dem Antwortstream:</span><span class="sxs-lookup"><span data-stu-id="a742f-148">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

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

### <a name="client-streaming-call"></a><span data-ttu-id="a742f-149">Clientstreamingaufrufe</span><span class="sxs-lookup"><span data-stu-id="a742f-149">Client streaming call</span></span>

<span data-ttu-id="a742f-150">Ein clientstreaming-Rückruf wird gestartet, *ohne* dass der Client eine Nachricht sendet.</span><span class="sxs-lookup"><span data-stu-id="a742f-150">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="a742f-151">Der Client kann das Senden von Nachrichten mit `RequestStream.WriteAsync`senden.</span><span class="sxs-lookup"><span data-stu-id="a742f-151">The client can choose to send sends messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="a742f-152">Wenn der Client das Senden von nach `RequestStream.CompleteAsync` richten abgeschlossen hat, sollte aufgerufen werden, um den Dienst zu benachrichtigen.</span><span class="sxs-lookup"><span data-stu-id="a742f-152">When the client has finished sending messages `RequestStream.CompleteAsync` should be called to notify the service.</span></span> <span data-ttu-id="a742f-153">Der-Rückruf ist abgeschlossen, wenn der Dienst eine Antwortnachricht zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="a742f-153">The call is finished when the service returns a response message.</span></span>

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

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="a742f-154">Bidirektionaler Streaming-Befehl</span><span class="sxs-lookup"><span data-stu-id="a742f-154">Bi-directional streaming call</span></span>

<span data-ttu-id="a742f-155">Ein bidirektionaler Streaming-Befehl wird gestartet, *ohne* dass der Client eine Nachricht sendet.</span><span class="sxs-lookup"><span data-stu-id="a742f-155">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="a742f-156">Der Client kann Nachrichten mit `RequestStream.WriteAsync`senden.</span><span class="sxs-lookup"><span data-stu-id="a742f-156">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="a742f-157">Auf Nachrichten, die vom-Dienst `ResponseStream.MoveNext()` gestreamt werden, kann mit oder `ResponseStream.ReadAllAsync()`</span><span class="sxs-lookup"><span data-stu-id="a742f-157">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="a742f-158">Der bidirektionale Streaming-Rückruf ist fertig `ResponseStream` , wenn keine weiteren Nachrichten mehr enthält.</span><span class="sxs-lookup"><span data-stu-id="a742f-158">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

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

<span data-ttu-id="a742f-159">Während eines bidirektionalen Streaming-Aufrufes können Client und Dienst Nachrichten jederzeit an einander senden.</span><span class="sxs-lookup"><span data-stu-id="a742f-159">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="a742f-160">Die beste Client Logik für die Interaktion mit einem bidirektionalen-aufruten hängt von der Dienst Logik ab.</span><span class="sxs-lookup"><span data-stu-id="a742f-160">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a742f-161">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="a742f-161">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/basics>

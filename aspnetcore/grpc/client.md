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
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="0ea5c-103">GrpC-Dienste mit dem .NET-Client abrufen</span><span class="sxs-lookup"><span data-stu-id="0ea5c-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="0ea5c-104">Eine .net-GrpC-Client Bibliothek ist im nuget-Paket " [GrpC .net. Client](https://www.nuget.org/packages/Grpc.Net.Client) " verfügbar.</span><span class="sxs-lookup"><span data-stu-id="0ea5c-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="0ea5c-105">In diesem Dokument wird Folgendes erläutert:</span><span class="sxs-lookup"><span data-stu-id="0ea5c-105">This document explains how to:</span></span>

* <span data-ttu-id="0ea5c-106">Konfigurieren Sie einen GrpC-Client, um GrpC-Dienste aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="0ea5c-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="0ea5c-107">Erstellen von GrpC-Aufrufen von unären, Server Streaming-, Client Streaming-und bidirektionalen Streamingmethoden.</span><span class="sxs-lookup"><span data-stu-id="0ea5c-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="0ea5c-108">Konfigurieren des GrpC-Clients</span><span class="sxs-lookup"><span data-stu-id="0ea5c-108">Configure gRPC client</span></span>

<span data-ttu-id="0ea5c-109">gRPC-Clients sind konkrete Clienttypen, [die aus *\*.proto*Dateien generiert werden](xref:grpc/basics#generated-c-assets).</span><span class="sxs-lookup"><span data-stu-id="0ea5c-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="0ea5c-110">Der konkrete gRPC-Client verfügt über Methoden, die in den gRPC-Dienst in der *\*.proto*-Datei übersetzt werden.</span><span class="sxs-lookup"><span data-stu-id="0ea5c-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

<span data-ttu-id="0ea5c-111">Ein GrpC-Client wird über einen Kanal erstellt.</span><span class="sxs-lookup"><span data-stu-id="0ea5c-111">A gRPC client is created from a channel.</span></span> <span data-ttu-id="0ea5c-112">Beginnen Sie mit `GrpcChannel.ForAddress`, um einen Kanal zu erstellen, und verwenden Sie dann den Kanal, um einen GrpC-Client zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="0ea5c-112">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="0ea5c-113">Ein Kanal stellt eine langlebige Verbindung mit einem GrpC-Dienst dar.</span><span class="sxs-lookup"><span data-stu-id="0ea5c-113">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="0ea5c-114">Wenn ein Kanal erstellt wird, wird er mit Optionen konfiguriert, die mit dem Aufrufen eines Dienstanbieter verknüpft sind.</span><span class="sxs-lookup"><span data-stu-id="0ea5c-114">When a channel is created, it is configured with options related to calling a service.</span></span> <span data-ttu-id="0ea5c-115">Beispielsweise können die `HttpClient`, die für Aufrufe, die maximale Größe der Sende-und Empfangs Nachricht und die Protokollierung verwendet werden, auf `GrpcChannelOptions` angegeben und mit `GrpcChannel.ForAddress`verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="0ea5c-115">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="0ea5c-116">Eine umfassende Liste der Optionen finden Sie unter [Client Konfigurationsoptionen](xref:grpc/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="0ea5c-116">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

<span data-ttu-id="0ea5c-117">Leistung und Nutzung von Kanälen und Clients:</span><span class="sxs-lookup"><span data-stu-id="0ea5c-117">Channel and client performance and usage:</span></span>

* <span data-ttu-id="0ea5c-118">Das Erstellen eines Kanals kann ein kostspieliger Vorgang sein.</span><span class="sxs-lookup"><span data-stu-id="0ea5c-118">Creating a channel can be an expensive operation.</span></span> <span data-ttu-id="0ea5c-119">Die erneute Verwendung eines Kanals für GrpC-Anrufe bietet Leistungsvorteile.</span><span class="sxs-lookup"><span data-stu-id="0ea5c-119">Reusing a channel for gRPC calls provides performance benefits.</span></span>
* <span data-ttu-id="0ea5c-120">GrpC-Clients werden mit Kanälen erstellt.</span><span class="sxs-lookup"><span data-stu-id="0ea5c-120">gRPC clients are created with channels.</span></span> <span data-ttu-id="0ea5c-121">GrpC-Clients sind Lightweight-Objekte und müssen nicht zwischengespeichert oder wieder verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="0ea5c-121">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="0ea5c-122">Mehrere GrpC-Clients können über einen Kanal erstellt werden, einschließlich unterschiedlicher Typen von Clients.</span><span class="sxs-lookup"><span data-stu-id="0ea5c-122">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="0ea5c-123">Ein Kanal und Clients, die über den Kanal erstellt wurden, können von mehreren Threads sicher verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="0ea5c-123">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="0ea5c-124">Clients, die über den Kanal erstellt werden, können mehrere gleichzeitige Aufrufe ausführen.</span><span class="sxs-lookup"><span data-stu-id="0ea5c-124">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="0ea5c-125">`GrpcChannel.ForAddress` ist nicht die einzige Option zum Erstellen eines GrpC-Clients.</span><span class="sxs-lookup"><span data-stu-id="0ea5c-125">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="0ea5c-126">Wenn Sie GrpC-Dienste aus einer ASP.net Core-App aufrufen, sollten Sie die [Integration der GrpC-Client Factory](xref:grpc/clientfactory)in Erwägung gezogen.</span><span class="sxs-lookup"><span data-stu-id="0ea5c-126">If you're calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="0ea5c-127">die GrpC-Integration mit `HttpClientFactory` bietet eine zentralisierte Alternative zum Erstellen von GrpC-Clients.</span><span class="sxs-lookup"><span data-stu-id="0ea5c-127">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

> [!NOTE]
> <span data-ttu-id="0ea5c-128">Zum [Abrufen unsicherer GrpC-Dienste mit dem .NET-Client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client)ist eine zusätzliche Konfiguration erforderlich.</span><span class="sxs-lookup"><span data-stu-id="0ea5c-128">Additional configuration is required to [call insecure gRPC services with the .NET client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="0ea5c-129">Erstellen von GrpC-aufrufen</span><span class="sxs-lookup"><span data-stu-id="0ea5c-129">Make gRPC calls</span></span>

<span data-ttu-id="0ea5c-130">Ein GrpC-Aufruf wird initiiert, indem eine Methode auf dem Client aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="0ea5c-130">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="0ea5c-131">Der GrpC-Client verarbeitet die Nachrichtenserialisierung und adressiert den GrpC-Rückruf an den richtigen Dienst.</span><span class="sxs-lookup"><span data-stu-id="0ea5c-131">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="0ea5c-132">GrpC verfügt über unterschiedliche Arten von Methoden.</span><span class="sxs-lookup"><span data-stu-id="0ea5c-132">gRPC has different types of methods.</span></span> <span data-ttu-id="0ea5c-133">Wie Sie den Client verwenden, um einen GrpC-Aufruf durchführen zu können, hängt vom Typ der Methode ab, die Sie aufrufen.</span><span class="sxs-lookup"><span data-stu-id="0ea5c-133">How you use the client to make a gRPC call depends on the type of method you are calling.</span></span> <span data-ttu-id="0ea5c-134">Die GrpC-Methoden Typen lauten wie folgt:</span><span class="sxs-lookup"><span data-stu-id="0ea5c-134">The gRPC method types are:</span></span>

* <span data-ttu-id="0ea5c-135">Unär</span><span class="sxs-lookup"><span data-stu-id="0ea5c-135">Unary</span></span>
* <span data-ttu-id="0ea5c-136">Server Streaming</span><span class="sxs-lookup"><span data-stu-id="0ea5c-136">Server streaming</span></span>
* <span data-ttu-id="0ea5c-137">Client Streaming</span><span class="sxs-lookup"><span data-stu-id="0ea5c-137">Client streaming</span></span>
* <span data-ttu-id="0ea5c-138">Bidirektionales Streaming</span><span class="sxs-lookup"><span data-stu-id="0ea5c-138">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="0ea5c-139">Unärer Befehl</span><span class="sxs-lookup"><span data-stu-id="0ea5c-139">Unary call</span></span>

<span data-ttu-id="0ea5c-140">Ein unärer-Befehl beginnt mit dem Client, der eine Anforderungs Nachricht sendet.</span><span class="sxs-lookup"><span data-stu-id="0ea5c-140">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="0ea5c-141">Eine Antwortmeldung wird zurückgegeben, wenn der Dienst beendet wird.</span><span class="sxs-lookup"><span data-stu-id="0ea5c-141">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="0ea5c-142">Jede unäre Dienst Methode in der Datei " *\*. proto* " führt zum Aufrufen der-Methode zwei .NET-Methoden auf dem konkreten GrpC-Clienttyp: eine asynchrone Methode und eine blockierende Methode.</span><span class="sxs-lookup"><span data-stu-id="0ea5c-142">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="0ea5c-143">Beispielsweise können Sie auf `GreeterClient` auf zwei Arten `SayHello`aufrufen:</span><span class="sxs-lookup"><span data-stu-id="0ea5c-143">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="0ea5c-144">`GreeterClient.SayHelloAsync` ruft `Greeter.SayHello` Dienst asynchron auf.</span><span class="sxs-lookup"><span data-stu-id="0ea5c-144">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="0ea5c-145">Kann erwartet werden.</span><span class="sxs-lookup"><span data-stu-id="0ea5c-145">Can be awaited.</span></span>
* <span data-ttu-id="0ea5c-146">`GreeterClient.SayHello` ruft `Greeter.SayHello` Dienst auf und blockiert, bis der Vorgang beendet ist.</span><span class="sxs-lookup"><span data-stu-id="0ea5c-146">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="0ea5c-147">Verwenden Sie in asynchronem Code nicht.</span><span class="sxs-lookup"><span data-stu-id="0ea5c-147">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="0ea5c-148">Serverstreamingaufrufe</span><span class="sxs-lookup"><span data-stu-id="0ea5c-148">Server streaming call</span></span>

<span data-ttu-id="0ea5c-149">Ein serverstreamingvorgang beginnt, wenn der Client eine Anforderungs Nachricht sendet.</span><span class="sxs-lookup"><span data-stu-id="0ea5c-149">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="0ea5c-150">`ResponseStream.MoveNext()` liest Nachrichten, die vom Dienst gestreamt werden.</span><span class="sxs-lookup"><span data-stu-id="0ea5c-150">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="0ea5c-151">Der Server Streaming-Rückruf ist fertiggestellt, wenn `ResponseStream.MoveNext()` `false`zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="0ea5c-151">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

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

<span data-ttu-id="0ea5c-152">Wenn Sie 8 oder C# höher verwenden, kann die `await foreach` Syntax zum Lesen von Nachrichten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="0ea5c-152">If you are using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="0ea5c-153">Mit der `IAsyncStreamReader<T>.ReadAllAsync()`-Erweiterungsmethode werden alle Nachrichten aus dem Antwortstream gelesen:</span><span class="sxs-lookup"><span data-stu-id="0ea5c-153">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

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

### <a name="client-streaming-call"></a><span data-ttu-id="0ea5c-154">Clientstreamingaufrufe</span><span class="sxs-lookup"><span data-stu-id="0ea5c-154">Client streaming call</span></span>

<span data-ttu-id="0ea5c-155">Ein clientstreaming-Rückruf wird gestartet, *ohne* dass der Client eine Nachricht sendet.</span><span class="sxs-lookup"><span data-stu-id="0ea5c-155">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="0ea5c-156">Der Client kann auswählen, ob Nachrichten mit `RequestStream.WriteAsync`gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="0ea5c-156">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="0ea5c-157">Wenn der Client das Senden von Nachrichten abgeschlossen hat `RequestStream.CompleteAsync` sollte aufgerufen werden, um den Dienst zu benachrichtigen.</span><span class="sxs-lookup"><span data-stu-id="0ea5c-157">When the client has finished sending messages `RequestStream.CompleteAsync` should be called to notify the service.</span></span> <span data-ttu-id="0ea5c-158">Der-Rückruf ist abgeschlossen, wenn der Dienst eine Antwortnachricht zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="0ea5c-158">The call is finished when the service returns a response message.</span></span>

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

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="0ea5c-159">Bidirektionaler Streaming-Befehl</span><span class="sxs-lookup"><span data-stu-id="0ea5c-159">Bi-directional streaming call</span></span>

<span data-ttu-id="0ea5c-160">Ein bidirektionaler Streaming-Befehl wird gestartet, *ohne* dass der Client eine Nachricht sendet.</span><span class="sxs-lookup"><span data-stu-id="0ea5c-160">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="0ea5c-161">Der Client kann auswählen, ob Nachrichten mit `RequestStream.WriteAsync`gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="0ea5c-161">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="0ea5c-162">Der Zugriff auf Nachrichten, die vom Dienst gestreamt werden, kann mit `ResponseStream.MoveNext()` oder `ResponseStream.ReadAllAsync()`</span><span class="sxs-lookup"><span data-stu-id="0ea5c-162">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="0ea5c-163">Der bidirektionale Streaming-Aufrufvorgang ist beendet, wenn der `ResponseStream` keine weiteren Nachrichten mehr enthält.</span><span class="sxs-lookup"><span data-stu-id="0ea5c-163">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

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

<span data-ttu-id="0ea5c-164">Während eines bidirektionalen Streaming-Aufrufes können Client und Dienst Nachrichten jederzeit an einander senden.</span><span class="sxs-lookup"><span data-stu-id="0ea5c-164">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="0ea5c-165">Die beste Client Logik für die Interaktion mit einem bidirektionalen-aufruten hängt von der Dienst Logik ab.</span><span class="sxs-lookup"><span data-stu-id="0ea5c-165">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0ea5c-166">Weitere Ressourcen</span><span class="sxs-lookup"><span data-stu-id="0ea5c-166">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/basics>

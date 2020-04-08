---
title: gRPC-Integration von Clientfactory in .NET Core
author: jamesnk
description: Hier erfahren Sie, wie Sie gRPC-Clients mithilfe der Clientfactory erstellen.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 11/12/2019
no-loc:
- SignalR
uid: grpc/clientfactory
ms.openlocfilehash: 3042bb61367f8b9a9f3142217ad329270ab2cca5
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/06/2020
ms.locfileid: "78650797"
---
# <a name="grpc-client-factory-integration-in-net-core"></a><span data-ttu-id="9052c-103">gRPC-Integration von Clientfactory in .NET Core</span><span class="sxs-lookup"><span data-stu-id="9052c-103">gRPC client factory integration in .NET Core</span></span>

<span data-ttu-id="9052c-104">Die gRPC-Integration mit `HttpClientFactory` bietet eine zentralisierte Möglichkeit, gRPC-Clients zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="9052c-104">gRPC integration with `HttpClientFactory` offers a centralized way to create gRPC clients.</span></span> <span data-ttu-id="9052c-105">Diese Möglichkeit kann als Alternative zum [Konfigurieren eigenständiger gRPC-Clientinstanzen](xref:grpc/client) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="9052c-105">It can be used as an alternative to [configuring stand-alone gRPC client instances](xref:grpc/client).</span></span> <span data-ttu-id="9052c-106">Die Factoryintegration ist im NuGet-Paket [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) verfügbar.</span><span class="sxs-lookup"><span data-stu-id="9052c-106">Factory integration is available in the [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="9052c-107">Die Factory bietet die folgenden Vorteile:</span><span class="sxs-lookup"><span data-stu-id="9052c-107">The factory offers the following benefits:</span></span>

* <span data-ttu-id="9052c-108">Ein zentraler Ort für das Konfigurieren logischer gRPC-Clientinstanzen</span><span class="sxs-lookup"><span data-stu-id="9052c-108">Provides a central location for configuring logical gRPC client instances</span></span>
* <span data-ttu-id="9052c-109">Optionen für die Verwaltung der Lebensdauer des zugrunde liegenden `HttpClientMessageHandler`</span><span class="sxs-lookup"><span data-stu-id="9052c-109">Manages the lifetime of the underlying `HttpClientMessageHandler`</span></span>
* <span data-ttu-id="9052c-110">Automatische Weitergabe von Stichtagen und Absagen in einem ASP.NET Core-gRPC-Dienst</span><span class="sxs-lookup"><span data-stu-id="9052c-110">Automatic propagation of deadline and cancellation in an ASP.NET Core gRPC service</span></span>

## <a name="register-grpc-clients"></a><span data-ttu-id="9052c-111">Registrierung von gRPC-Clients</span><span class="sxs-lookup"><span data-stu-id="9052c-111">Register gRPC clients</span></span>

<span data-ttu-id="9052c-112">Zum Registrieren eines gRPC-Clients kann die generische Erweiterungsmethode `AddGrpcClient` innerhalb von `Startup.ConfigureServices` verwendet werden, die die typisierte Klasse des gRPC-Clients und die Dienstadresse angibt:</span><span class="sxs-lookup"><span data-stu-id="9052c-112">To register a gRPC client, the generic `AddGrpcClient` extension method can be used within `Startup.ConfigureServices`, specifying the gRPC typed client class and service address:</span></span>

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

<span data-ttu-id="9052c-113">Der gRPC-Clienttyp wird als vorübergehender Typ mit einer Abhängigkeitsinjektion (Dependency Injection, DI) registriert.</span><span class="sxs-lookup"><span data-stu-id="9052c-113">The gRPC client type is registered as transient with dependency injection (DI).</span></span> <span data-ttu-id="9052c-114">Der Client kann nun injiziert und direkt in von der DI erstellten Typen genutzt werden.</span><span class="sxs-lookup"><span data-stu-id="9052c-114">The client can now be injected and consumed directly in types created by DI.</span></span> <span data-ttu-id="9052c-115">Bei ASP.NET Core-MVC-Controllern, SignalR-Hubs und gRPC-Diensten handelt es sich um Orte, an denen gRPC-Clients direkt injiziert werden können:</span><span class="sxs-lookup"><span data-stu-id="9052c-115">ASP.NET Core MVC controllers, SignalR hubs and gRPC services are places where gRPC clients can automatically be injected:</span></span>

```csharp
public class AggregatorService : Aggregator.AggregatorBase
{
    private readonly Greeter.GreeterClient _client;

    public AggregatorService(Greeter.GreeterClient client)
    {
        _client = client;
    }

    public override async Task SayHellos(HelloRequest request,
        IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
    {
        // Forward the call on to the greeter service
        using (var call = _client.SayHellos(request))
        {
            await foreach (var response in call.ResponseStream.ReadAllAsync())
            {
                await responseStream.WriteAsync(response);
            }
        }
    }
}
```

## <a name="configure-httpclient"></a><span data-ttu-id="9052c-116">Konfigurieren von HttpClient</span><span class="sxs-lookup"><span data-stu-id="9052c-116">Configure HttpClient</span></span>

<span data-ttu-id="9052c-117">`HttpClientFactory` erstellt die `HttpClient`-Klasse, die vom gRPC-Client verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="9052c-117">`HttpClientFactory` creates the `HttpClient` used by the gRPC client.</span></span> <span data-ttu-id="9052c-118">`HttpClientFactory`-Standardmethoden können verwendet werden ,um Middleware für ausgehende Anforderungen hinzuzufügen oder den zugrunde liegenden `HttpClientHandler` der `HttpClient`-Klasse zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="9052c-118">Standard `HttpClientFactory` methods can be used to add outgoing request middleware or to configure the underlying `HttpClientHandler` of the `HttpClient`:</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(() =>
    {
        var handler = new HttpClientHandler();
        handler.ClientCertificates.Add(LoadCertificate());
        return handler;
    });
```

<span data-ttu-id="9052c-119">Weitere Informationen erhalten Sie unter [Stellen von HTTP-Anforderungen mithilfe von IHttpClientFactory in ASP.NET Core](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="9052c-119">For more information, see [Make HTTP requests using IHttpClientFactory](xref:fundamentals/http-requests).</span></span>

## <a name="configure-channel-and-interceptors"></a><span data-ttu-id="9052c-120">Konfigurieren von Kanal und Interceptoren</span><span class="sxs-lookup"><span data-stu-id="9052c-120">Configure Channel and Interceptors</span></span>

<span data-ttu-id="9052c-121">Für Folgendes sind gRPC-spezifische Methoden verfügbar:</span><span class="sxs-lookup"><span data-stu-id="9052c-121">gRPC-specific methods are available to:</span></span>

* <span data-ttu-id="9052c-122">Konfigurieren des einem gRPC-Client zugrunde liegenden Kanals</span><span class="sxs-lookup"><span data-stu-id="9052c-122">Configure a gRPC client's underlying channel.</span></span>
* <span data-ttu-id="9052c-123">Hinzufügen von `Interceptor`-Instanzen, die der Client für gRPC-Aufrufe verwendet</span><span class="sxs-lookup"><span data-stu-id="9052c-123">Add `Interceptor` instances that the client will use when making gRPC calls.</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .AddInterceptor(() => new LoggingInterceptor())
    .ConfigureChannel(o =>
    {
        o.Credentials = new CustomCredentials();
    });
```

## <a name="deadline-and-cancellation-propagation"></a><span data-ttu-id="9052c-124">Weitergabe von Stichtagen und Absagen</span><span class="sxs-lookup"><span data-stu-id="9052c-124">Deadline and cancellation propagation</span></span>

<span data-ttu-id="9052c-125">gRPC-Clients, die von der Factory in einem gRPC-Dienst erstellt wurden, können mit `EnableCallContextPropagation()` so konfiguriert werden, dass sie die Tokens für Stichtage und Absagen automatisch an untergeordnete Aufrufe weitergeben.</span><span class="sxs-lookup"><span data-stu-id="9052c-125">gRPC clients created by the factory in a gRPC service can be configured with `EnableCallContextPropagation()` to automatically propagate the deadline and cancellation token to child calls.</span></span> <span data-ttu-id="9052c-126">Die Erweiterungsmethode `EnableCallContextPropagation()` ist im NuGet-Paket [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) verfügbar.</span><span class="sxs-lookup"><span data-stu-id="9052c-126">The `EnableCallContextPropagation()` extension method is available in the [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="9052c-127">Die Weitergabe von Aufrufkontext funktioniert, in dem die Tokens für Stichtage und Absagen aus dem aktuellen gRPC-Anforderungskontext gelesen und automatisch an ausgehende Aufrufe weitergegeben werden, die vom gRPC-Client ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="9052c-127">Call context propagation works by reading the deadline and cancellation token from the current gRPC request context and automatically propagating them to outgoing calls made by the gRPC client.</span></span> <span data-ttu-id="9052c-128">Die Weitergabe von Aufrufkontext ist eine sehr gute Möglichkeit, sicherzustellen, dass komplexe und verschachtelte gRPC-Szenarios immer den Stichtag und die Absage weitergeben.</span><span class="sxs-lookup"><span data-stu-id="9052c-128">Call context propagation is an excellent way of ensuring that complex, nested gRPC scenarios always propagate the deadline and cancellation.</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

<span data-ttu-id="9052c-129">Weitere Informationen zu Stichtagen und der RPC-Absage finden Sie unter [RPC-Lebenszyklus](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).</span><span class="sxs-lookup"><span data-stu-id="9052c-129">For more information about deadlines and RPC cancellation, see [RPC life cycle](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9052c-130">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="9052c-130">Additional resources</span></span>

* <xref:grpc/client>
* <xref:fundamentals/http-requests>

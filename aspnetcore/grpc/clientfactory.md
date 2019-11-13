---
title: Integration von GrpC-Client-Factory in .net Core
author: jamesnk
description: Erfahren Sie, wie Sie mithilfe der clientfactory GrpC-Clients erstellen.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 11/12/2019
no-loc:
- SignalR
uid: grpc/clientfactory
ms.openlocfilehash: 3042bb61367f8b9a9f3142217ad329270ab2cca5
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963684"
---
# <a name="grpc-client-factory-integration-in-net-core"></a><span data-ttu-id="00845-103">Integration von GrpC-Client-Factory in .net Core</span><span class="sxs-lookup"><span data-stu-id="00845-103">gRPC client factory integration in .NET Core</span></span>

<span data-ttu-id="00845-104">die GrpC-Integration mit `HttpClientFactory` bietet eine zentralisierte Möglichkeit zum Erstellen von GrpC-Clients.</span><span class="sxs-lookup"><span data-stu-id="00845-104">gRPC integration with `HttpClientFactory` offers a centralized way to create gRPC clients.</span></span> <span data-ttu-id="00845-105">Sie kann als Alternative zum [Konfigurieren eigenständiger GrpC-Client Instanzen](xref:grpc/client)verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="00845-105">It can be used as an alternative to [configuring stand-alone gRPC client instances](xref:grpc/client).</span></span> <span data-ttu-id="00845-106">Die Factory-Integration steht im nuget-Paket von [GrpC .net. clientfactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="00845-106">Factory integration is available in the [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="00845-107">Die Factory bietet die folgenden Vorteile:</span><span class="sxs-lookup"><span data-stu-id="00845-107">The factory offers the following benefits:</span></span>

* <span data-ttu-id="00845-108">Stellt einen zentralen Ort zum Konfigurieren logischer GrpC-Client Instanzen bereit.</span><span class="sxs-lookup"><span data-stu-id="00845-108">Provides a central location for configuring logical gRPC client instances</span></span>
* <span data-ttu-id="00845-109">Verwaltet die Lebensdauer des zugrunde liegenden `HttpClientMessageHandler`</span><span class="sxs-lookup"><span data-stu-id="00845-109">Manages the lifetime of the underlying `HttpClientMessageHandler`</span></span>
* <span data-ttu-id="00845-110">Automatische Weitergabe von Stichtag und Abbruch in einem ASP.net Core GrpC-Dienst</span><span class="sxs-lookup"><span data-stu-id="00845-110">Automatic propagation of deadline and cancellation in an ASP.NET Core gRPC service</span></span>

## <a name="register-grpc-clients"></a><span data-ttu-id="00845-111">Registrieren von GrpC-Clients</span><span class="sxs-lookup"><span data-stu-id="00845-111">Register gRPC clients</span></span>

<span data-ttu-id="00845-112">Um einen GrpC-Client zu registrieren, kann die generische `AddGrpcClient`-Erweiterungsmethode in `Startup.ConfigureServices`verwendet werden, wobei die von GrpC typisierte Client Klasse und Dienst Adresse angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="00845-112">To register a gRPC client, the generic `AddGrpcClient` extension method can be used within `Startup.ConfigureServices`, specifying the gRPC typed client class and service address:</span></span>

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

<span data-ttu-id="00845-113">Der GrpC-Clienttyp wird als vorübergehender Wert mit Abhängigkeitsinjektion (di) registriert.</span><span class="sxs-lookup"><span data-stu-id="00845-113">The gRPC client type is registered as transient with dependency injection (DI).</span></span> <span data-ttu-id="00845-114">Der Client kann nun direkt in von di erstellten Typen eingefügt und genutzt werden.</span><span class="sxs-lookup"><span data-stu-id="00845-114">The client can now be injected and consumed directly in types created by DI.</span></span> <span data-ttu-id="00845-115">ASP.net Core MVC-Controller sind SignalR Hubs und GrpC-Dienste Orte, an denen GrpC-Clients automatisch eingefügt werden können:</span><span class="sxs-lookup"><span data-stu-id="00845-115">ASP.NET Core MVC controllers, SignalR hubs and gRPC services are places where gRPC clients can automatically be injected:</span></span>

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

## <a name="configure-httpclient"></a><span data-ttu-id="00845-116">HttpClient konfigurieren</span><span class="sxs-lookup"><span data-stu-id="00845-116">Configure HttpClient</span></span>

<span data-ttu-id="00845-117">`HttpClientFactory` erstellt die `HttpClient`, die vom GrpC-Client verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="00845-117">`HttpClientFactory` creates the `HttpClient` used by the gRPC client.</span></span> <span data-ttu-id="00845-118">Standard `HttpClientFactory` Methoden können verwendet werden, um eine ausgehende Anforderungs Middleware hinzuzufügen oder um die zugrunde liegende `HttpClientHandler` der `HttpClient`zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="00845-118">Standard `HttpClientFactory` methods can be used to add outgoing request middleware or to configure the underlying `HttpClientHandler` of the `HttpClient`:</span></span>

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

<span data-ttu-id="00845-119">Weitere Informationen finden Sie unter [make http Requests using ihttpclientfactory](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="00845-119">For more information, see [Make HTTP requests using IHttpClientFactory](xref:fundamentals/http-requests).</span></span>

## <a name="configure-channel-and-interceptors"></a><span data-ttu-id="00845-120">Konfigurieren von Kanälen und Interceptors</span><span class="sxs-lookup"><span data-stu-id="00845-120">Configure Channel and Interceptors</span></span>

<span data-ttu-id="00845-121">GrpC-spezifische Methoden stehen für Folgendes zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="00845-121">gRPC-specific methods are available to:</span></span>

* <span data-ttu-id="00845-122">Konfigurieren Sie den zugrunde liegenden Channel eines GrpC-Clients.</span><span class="sxs-lookup"><span data-stu-id="00845-122">Configure a gRPC client's underlying channel.</span></span>
* <span data-ttu-id="00845-123">Fügen Sie `Interceptor` Instanzen hinzu, die vom Client bei der Erstellung von GrpC-Aufrufen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="00845-123">Add `Interceptor` instances that the client will use when making gRPC calls.</span></span>

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

## <a name="deadline-and-cancellation-propagation"></a><span data-ttu-id="00845-124">Propagierung von Stichtag und Abbruch</span><span class="sxs-lookup"><span data-stu-id="00845-124">Deadline and cancellation propagation</span></span>

<span data-ttu-id="00845-125">GrpC-Clients, die von der Factory in einem GrpC-Dienst erstellt wurden, können mit `EnableCallContextPropagation()` konfiguriert werden, um den Stichtag und das Abbruch Token automatisch an untergeordnete Anrufe weiterzugeben.</span><span class="sxs-lookup"><span data-stu-id="00845-125">gRPC clients created by the factory in a gRPC service can be configured with `EnableCallContextPropagation()` to automatically propagate the deadline and cancellation token to child calls.</span></span> <span data-ttu-id="00845-126">Die `EnableCallContextPropagation()`-Erweiterungsmethode ist im nuget-Paket " [GrpC. aspnetcore. Server. clientfactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) " verfügbar.</span><span class="sxs-lookup"><span data-stu-id="00845-126">The `EnableCallContextPropagation()` extension method is available in the [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="00845-127">Die Aufruf Kontext Weitergabe funktioniert, indem der Stichtag und das Abbruch Token aus dem aktuellen GrpC-Anforderungs Kontext gelesen und automatisch an ausgehende Aufrufe durch den GrpC-Client weitergegeben werden.</span><span class="sxs-lookup"><span data-stu-id="00845-127">Call context propagation works by reading the deadline and cancellation token from the current gRPC request context and automatically propagating them to outgoing calls made by the gRPC client.</span></span> <span data-ttu-id="00845-128">Die weitergabekontexweitergabe ist eine hervorragende Möglichkeit, um sicherzustellen, dass komplexe, in einem Szenario eingefügte GrpC-Szenarios immer</span><span class="sxs-lookup"><span data-stu-id="00845-128">Call context propagation is an excellent way of ensuring that complex, nested gRPC scenarios always propagate the deadline and cancellation.</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

<span data-ttu-id="00845-129">Weitere Informationen zu Stichtage und RPC-Abbruch finden Sie unter [RPC-Lebenszyklus](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).</span><span class="sxs-lookup"><span data-stu-id="00845-129">For more information about deadlines and RPC cancellation, see [RPC life cycle](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="00845-130">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="00845-130">Additional resources</span></span>

* <xref:grpc/client>
* <xref:fundamentals/http-requests>

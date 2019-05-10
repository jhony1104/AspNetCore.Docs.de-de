---
title: Migrieren von gRPC-Dienste von C-Core in ASP.NET Core
author: juntaoluo
description: Informationen Sie zum Verschieben einer vorhandenen C-Core-Basis gRPC-app auf ASP.NET Core-Stapel ausgeführt.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 03/31/2019
uid: grpc/migration
ms.openlocfilehash: 47d74edd821124f0c8390d704ca7931b7eb6c4cd
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64895237"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a><span data-ttu-id="d90b9-103">Migrieren von gRPC-Dienste von C-Core in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d90b9-103">Migrating gRPC services from C-core to ASP.NET Core</span></span>

<span data-ttu-id="d90b9-104">Von [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="d90b9-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="d90b9-105">Aufgrund der Implementierung der zugrunde liegenden Stapel nicht alle Funktionen, die auf die gleiche Weise zwischen funktionieren [C-Core-basierte gRPC](https://grpc.io/blog/grpc-stacks) -apps und ASP.NET Core-basierten apps.</span><span class="sxs-lookup"><span data-stu-id="d90b9-105">Due to the implementation of the underlying stack, not all features work in the same way between [C-core-based gRPC](https://grpc.io/blog/grpc-stacks) apps and ASP.NET Core-based apps.</span></span> <span data-ttu-id="d90b9-106">In diesem Dokument werden die wichtigsten Unterschiede für die Migration zwischen beiden Stapeln hervorgehoben.</span><span class="sxs-lookup"><span data-stu-id="d90b9-106">This document highlights the key differences for migrating between the two stacks.</span></span>

## <a name="grpc-service-implementation-lifetime"></a><span data-ttu-id="d90b9-107">gRPC Implementierung Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="d90b9-107">gRPC service implementation lifetime</span></span>

<span data-ttu-id="d90b9-108">Im ASP.NET Core-Stapel gRPC-Services, in der Standardeinstellung werden mit erstellt eine [bereichslebensdauer](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="d90b9-108">In the ASP.NET Core stack, gRPC services, by default, are created with a [scoped lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="d90b9-109">Im Gegensatz dazu gRPC C-Core standardmäßig bindet an einen Dienst mit einem [Singleton-Lebensdauer](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="d90b9-109">In contrast, gRPC C-core by default binds to a service with a [singleton lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="d90b9-110">Eine Bereichsbezogene Lebensdauer ermöglicht die dienstimplementierung zum Auflösen von anderen Diensten mit Bereichsbezogene Lebensdauer.</span><span class="sxs-lookup"><span data-stu-id="d90b9-110">A scoped lifetime allows the service implementation to resolve other services with scoped lifetimes.</span></span> <span data-ttu-id="d90b9-111">Eine Bereichsbezogene Lebensdauer kann z. B. auch beheben `DBContext` vom DI-Container über Konstruktorinjektion.</span><span class="sxs-lookup"><span data-stu-id="d90b9-111">For example, a scoped lifetime can also resolve `DBContext` from the DI container through constructor injection.</span></span> <span data-ttu-id="d90b9-112">Verwenden die bereichsbezogenen Lebensdauer:</span><span class="sxs-lookup"><span data-stu-id="d90b9-112">Using scoped lifetime:</span></span>

* <span data-ttu-id="d90b9-113">Eine neue Instanz der dienstimplementierung, die für jede Anforderung erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="d90b9-113">A new instance of the service implementation is constructed for each request.</span></span>
* <span data-ttu-id="d90b9-114">Es ist nicht möglich, zwischen Anforderungen über Instanzmember auf den Implementierungstyp möglich.</span><span class="sxs-lookup"><span data-stu-id="d90b9-114">It isn't possible to share state between requests via instance members on the implementation type.</span></span>
* <span data-ttu-id="d90b9-115">Zum Speichern von Freigabestatus in ein Singleton-Dienst in der DI-Container wird erwartet.</span><span class="sxs-lookup"><span data-stu-id="d90b9-115">The expectation is to store shared states in a singleton service in the DI container.</span></span> <span data-ttu-id="d90b9-116">Die gespeicherten freigegebenen Status werden im Konstruktor der dienstimplementierung gRPC aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="d90b9-116">The stored shared states are resolved in the constructor of the gRPC service implementation.</span></span>

<span data-ttu-id="d90b9-117">Weitere Informationen zu dienstlebensdauer, finden Sie unter <xref:fundamentals/dependency-injection#service-lifetimes>.</span><span class="sxs-lookup"><span data-stu-id="d90b9-117">For more information on service lifetimes, see <xref:fundamentals/dependency-injection#service-lifetimes>.</span></span>

### <a name="add-a-singleton-service"></a><span data-ttu-id="d90b9-118">Fügen Sie einen Singleton-Dienst</span><span class="sxs-lookup"><span data-stu-id="d90b9-118">Add a singleton service</span></span>

<span data-ttu-id="d90b9-119">Um den Übergang von einer gRPC-C-Core-Implementierung zu ASP.NET Core zu erleichtern, ist es möglich, ändern die Lebensdauer des Diensts der dienstimplementierung von Singleton zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="d90b9-119">To facilitate the transition from a gRPC C-core implementation to ASP.NET Core, it's possible to change the service lifetime of the service implementation from scoped to singleton.</span></span> <span data-ttu-id="d90b9-120">Dies umfasst die DI-Container eine Instanz der dienstimplementierung hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="d90b9-120">This involves adding an instance of the service implementation to the DI container:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

<span data-ttu-id="d90b9-121">Eine dienstimplementierung mit einer Singleton-Lebensdauer ist jedoch nicht mehr Bereichsbezogene Dienste über Konstruktorinjektion auflösen.</span><span class="sxs-lookup"><span data-stu-id="d90b9-121">However, a service implementation with a singleton lifetime is no longer able to resolve scoped services through constructor injection.</span></span>

## <a name="configure-grpc-services-options"></a><span data-ttu-id="d90b9-122">Konfigurieren Sie Optionen für gRPC-Dienste</span><span class="sxs-lookup"><span data-stu-id="d90b9-122">Configure gRPC services options</span></span>

<span data-ttu-id="d90b9-123">In C-Core-basierte apps, Einstellungen wie z. B. `grpc.max_receive_message_length` und `grpc.max_send_message_length` konfiguriert sind, mit `ChannelOption` beim [erstellen die Server-Instanz](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).</span><span class="sxs-lookup"><span data-stu-id="d90b9-123">In C-core-based apps, settings such as `grpc.max_receive_message_length` and `grpc.max_send_message_length` are configured with `ChannelOption` when [constructing the Server instance](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).</span></span>

<span data-ttu-id="d90b9-124">In ASP.NET Core bietet gRPC-Konfiguration über die `GrpcServiceOptions` Typ.</span><span class="sxs-lookup"><span data-stu-id="d90b9-124">In ASP.NET Core, gRPC provides configuration through the `GrpcServiceOptions` type.</span></span> <span data-ttu-id="d90b9-125">Z. B. eine gRPC des Diensts die maximale Größe eingehender Nachrichten werden, über konfiguriert kann `AddGrpc`:</span><span class="sxs-lookup"><span data-stu-id="d90b9-125">For example, a gRPC service's the maximum incoming message size can be configured via `AddGrpc`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.ReceiveMaxMessageSize = 16384; // 16 MB
    });
}
```

<span data-ttu-id="d90b9-126">Weitere Informationen zur Konfiguration finden Sie unter <xref:grpc/configuration>.</span><span class="sxs-lookup"><span data-stu-id="d90b9-126">For more information on configuration, see <xref:grpc/configuration>.</span></span>

## <a name="logging"></a><span data-ttu-id="d90b9-127">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="d90b9-127">Logging</span></span>

<span data-ttu-id="d90b9-128">C-Core-basierte apps verwenden die `GrpcEnvironment` zu [konfigurieren Sie die Protokollierung](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) zu Debugzwecken.</span><span class="sxs-lookup"><span data-stu-id="d90b9-128">C-core-based apps rely on the `GrpcEnvironment` to [configure the logger](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) for debugging purposes.</span></span> <span data-ttu-id="d90b9-129">Die ASP.NET Core-Stapel bietet folgende Funktionen über die [Protokollierungs-API](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="d90b9-129">The ASP.NET Core stack provides this functionality through the [Logging API](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="d90b9-130">Beispielsweise kann der gRPC-Dienst per Konstruktorinjektion eine Protokollierung hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="d90b9-130">For example, a logger can be added to the gRPC service via constructor injection:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a><span data-ttu-id="d90b9-131">HTTPS</span><span class="sxs-lookup"><span data-stu-id="d90b9-131">HTTPS</span></span>

<span data-ttu-id="d90b9-132">C-Core-basierte apps Konfigurieren von HTTPS über den [Server.Ports Eigenschaft](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span><span class="sxs-lookup"><span data-stu-id="d90b9-132">C-core-based apps configure HTTPS through the [Server.Ports property](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span></span> <span data-ttu-id="d90b9-133">Ein ähnliches Konzept dient zum Konfigurieren von Servern in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d90b9-133">A similar concept is used to configure servers in ASP.NET Core.</span></span> <span data-ttu-id="d90b9-134">Kestrel verwendet z. B. [Endpunktkonfiguration](xref:fundamentals/servers/kestrel#endpoint-configuration) für diese Funktionalität.</span><span class="sxs-lookup"><span data-stu-id="d90b9-134">For example, Kestrel uses [endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) for this functionality.</span></span>

## <a name="interceptors-and-middleware"></a><span data-ttu-id="d90b9-135">Dienste und Middleware</span><span class="sxs-lookup"><span data-stu-id="d90b9-135">Interceptors and Middleware</span></span>

<span data-ttu-id="d90b9-136">ASP.NET Core [Middleware](xref:fundamentals/middleware/index) bietet ähnliche Funktionen, die im Vergleich zu Interceptors in C-Core-basierte gRPC-apps.</span><span class="sxs-lookup"><span data-stu-id="d90b9-136">ASP.NET Core [middleware](xref:fundamentals/middleware/index) offers similar functionalities compared to interceptors in C-core-based gRPC apps.</span></span> <span data-ttu-id="d90b9-137">Middleware und Dienste sind konzeptionell identisch, da beide verwendet werden, um eine Pipeline zu erstellen, die eine gRPC-Anforderung verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="d90b9-137">Middleware and interceptors are conceptually the same as both are used to construct a pipeline that handles a gRPC request.</span></span> <span data-ttu-id="d90b9-138">Beide können vor oder nach dem die nächste Komponente in der Pipeline ausgeführt werden müssen.</span><span class="sxs-lookup"><span data-stu-id="d90b9-138">They both allow work to be performed before or after the next component in the pipeline.</span></span> <span data-ttu-id="d90b9-139">ASP.NET Core-Middleware wirkt sich jedoch auf die zugrunde liegenden HTTP/2-Nachrichten, während Interceptors für die gRPC-Ebene der Abstraktion Verwendung verwendet werden die [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).</span><span class="sxs-lookup"><span data-stu-id="d90b9-139">However, ASP.NET Core middleware operates on the underlying HTTP/2 messages, while interceptors operate on the gRPC layer of abstraction using the [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d90b9-140">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="d90b9-140">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>

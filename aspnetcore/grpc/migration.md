---
title: Migrieren von GrpC-Diensten von C-Core zu ASP.net Core
author: juntaoluo
description: Erfahren Sie, wie Sie eine vorhandene, auf C-Core basierende GrpC-App verschieben, die auf ASP.net Core Stapel ausgeführt wird.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/25/2019
uid: grpc/migration
ms.openlocfilehash: 8f0d9dd980fa3281f30dc29d329d10ccd352ae72
ms.sourcegitcommit: 994da92edb0abf856b1655c18880028b15a28897
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71278702"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a><span data-ttu-id="9886b-103">Migrieren von GrpC-Diensten von C-Core zu ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="9886b-103">Migrating gRPC services from C-core to ASP.NET Core</span></span>

<span data-ttu-id="9886b-104">Von [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="9886b-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="9886b-105">Aufgrund der Implementierung des zugrunde liegenden Stapels funktionieren nicht alle Features auf die gleiche Weise wie zwischen [C-Core-basierten GrpC-](https://grpc.io/blog/grpc-stacks) apps und ASP.net Core basierten apps.</span><span class="sxs-lookup"><span data-stu-id="9886b-105">Due to the implementation of the underlying stack, not all features work in the same way between [C-core-based gRPC](https://grpc.io/blog/grpc-stacks) apps and ASP.NET Core-based apps.</span></span> <span data-ttu-id="9886b-106">In diesem Dokument werden die wichtigsten Unterschiede bei der Migration zwischen den beiden Stapeln hervorgehoben.</span><span class="sxs-lookup"><span data-stu-id="9886b-106">This document highlights the key differences for migrating between the two stacks.</span></span>

## <a name="grpc-service-implementation-lifetime"></a><span data-ttu-id="9886b-107">Lebensdauer der GrpC-Dienst Implementierung</span><span class="sxs-lookup"><span data-stu-id="9886b-107">gRPC service implementation lifetime</span></span>

<span data-ttu-id="9886b-108">Im ASP.net Core Stapel werden GrpC-Dienste standardmäßig mit einer Bereichs bezogenen Gültigkeits [Dauer](xref:fundamentals/dependency-injection#service-lifetimes)erstellt.</span><span class="sxs-lookup"><span data-stu-id="9886b-108">In the ASP.NET Core stack, gRPC services, by default, are created with a [scoped lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="9886b-109">Im Gegensatz dazu bindet GrpC C-Core standardmäßig an einen Dienst mit einer [Singleton Lebensdauer](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="9886b-109">In contrast, gRPC C-core by default binds to a service with a [singleton lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="9886b-110">Eine Bereichs bezogene Lebensdauer ermöglicht es der Dienst Implementierung, andere Dienste mit Bereichs bezogener Lebensdauer aufzulösen.</span><span class="sxs-lookup"><span data-stu-id="9886b-110">A scoped lifetime allows the service implementation to resolve other services with scoped lifetimes.</span></span> <span data-ttu-id="9886b-111">Beispielsweise kann eine Gültigkeitsdauer im Gültigkeits `DbContext` Bereich auch durch die Konstruktorinjektion aus dem di-Container aufgelöst werden.</span><span class="sxs-lookup"><span data-stu-id="9886b-111">For example, a scoped lifetime can also resolve `DbContext` from the DI container through constructor injection.</span></span> <span data-ttu-id="9886b-112">Verwenden der Gültigkeitsdauer:</span><span class="sxs-lookup"><span data-stu-id="9886b-112">Using scoped lifetime:</span></span>

* <span data-ttu-id="9886b-113">Für jede Anforderung wird eine neue Instanz der Dienst Implementierung erstellt.</span><span class="sxs-lookup"><span data-stu-id="9886b-113">A new instance of the service implementation is constructed for each request.</span></span>
* <span data-ttu-id="9886b-114">Es ist nicht möglich, den Status zwischen Anforderungen über Instanzmember für den Implementierungstyp freizugeben.</span><span class="sxs-lookup"><span data-stu-id="9886b-114">It isn't possible to share state between requests via instance members on the implementation type.</span></span>
* <span data-ttu-id="9886b-115">Die Annahme besteht darin, freigegebene Zustände in einem Singleton-Dienst im di-Container zu speichern.</span><span class="sxs-lookup"><span data-stu-id="9886b-115">The expectation is to store shared states in a singleton service in the DI container.</span></span> <span data-ttu-id="9886b-116">Die gespeicherten freigegebenen Zustände werden im Konstruktor der GrpC-Dienst Implementierung aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="9886b-116">The stored shared states are resolved in the constructor of the gRPC service implementation.</span></span>

<span data-ttu-id="9886b-117">Weitere Informationen zur Dienst Lebensdauer finden <xref:fundamentals/dependency-injection#service-lifetimes>Sie unter.</span><span class="sxs-lookup"><span data-stu-id="9886b-117">For more information on service lifetimes, see <xref:fundamentals/dependency-injection#service-lifetimes>.</span></span>

### <a name="add-a-singleton-service"></a><span data-ttu-id="9886b-118">Einen Singleton-Dienst hinzufügen</span><span class="sxs-lookup"><span data-stu-id="9886b-118">Add a singleton service</span></span>

<span data-ttu-id="9886b-119">Um den Übergang von einer GrpC-C-Core-Implementierung zu ASP.net Core zu vereinfachen, ist es möglich, die Dienst Lebensdauer der Dienst Implementierung von einem Bereich in einen Singleton zu ändern.</span><span class="sxs-lookup"><span data-stu-id="9886b-119">To facilitate the transition from a gRPC C-core implementation to ASP.NET Core, it's possible to change the service lifetime of the service implementation from scoped to singleton.</span></span> <span data-ttu-id="9886b-120">Dies umfasst das Hinzufügen einer Instanz der Dienst Implementierung zum di-Container:</span><span class="sxs-lookup"><span data-stu-id="9886b-120">This involves adding an instance of the service implementation to the DI container:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

<span data-ttu-id="9886b-121">Allerdings kann eine Dienst Implementierung mit einer Singleton-Lebensdauer keine Bereichs bezogenen Dienste mehr durch Konstruktorinjektion auflösen.</span><span class="sxs-lookup"><span data-stu-id="9886b-121">However, a service implementation with a singleton lifetime is no longer able to resolve scoped services through constructor injection.</span></span>

## <a name="configure-grpc-services-options"></a><span data-ttu-id="9886b-122">Konfigurieren von GrpC-Dienst Optionen</span><span class="sxs-lookup"><span data-stu-id="9886b-122">Configure gRPC services options</span></span>

<span data-ttu-id="9886b-123">In C-Core-basierten apps werden Einstellungen `grpc.max_receive_message_length` wie und `grpc.max_send_message_length` beim [Erstellen der Server Instanz](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__)mit `ChannelOption` konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="9886b-123">In C-core-based apps, settings such as `grpc.max_receive_message_length` and `grpc.max_send_message_length` are configured with `ChannelOption` when [constructing the Server instance](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).</span></span>

<span data-ttu-id="9886b-124">In ASP.net Core bietet GrpC eine Konfiguration über den `GrpcServiceOptions` -Typ.</span><span class="sxs-lookup"><span data-stu-id="9886b-124">In ASP.NET Core, gRPC provides configuration through the `GrpcServiceOptions` type.</span></span> <span data-ttu-id="9886b-125">So kann z. b. die maximale Größe eingehender Nachrichten für einen GrpC- `AddGrpc`Dienst über konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="9886b-125">For example, a gRPC service's the maximum incoming message size can be configured via `AddGrpc`.</span></span> <span data-ttu-id="9886b-126">Im folgenden Beispiel wird der Standard `ReceiveMaxMessageSize` Wert von 4 MB in 16 MB geändert:</span><span class="sxs-lookup"><span data-stu-id="9886b-126">The following example changes the default `ReceiveMaxMessageSize` of 4 MB to 16 MB:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.ReceiveMaxMessageSize = 16 * 1024 * 1024; // 16 MB
    });
}
```

<span data-ttu-id="9886b-127">Weitere Informationen zur Konfiguration finden <xref:grpc/configuration>Sie unter.</span><span class="sxs-lookup"><span data-stu-id="9886b-127">For more information on configuration, see <xref:grpc/configuration>.</span></span>

## <a name="logging"></a><span data-ttu-id="9886b-128">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="9886b-128">Logging</span></span>

<span data-ttu-id="9886b-129">Auf C-Core basierende apps basieren auf dem `GrpcEnvironment` , um die Protokollierung zu Debuggingzwecken zu [Konfigurieren](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) .</span><span class="sxs-lookup"><span data-stu-id="9886b-129">C-core-based apps rely on the `GrpcEnvironment` to [configure the logger](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) for debugging purposes.</span></span> <span data-ttu-id="9886b-130">Der ASP.net Core Stapel stellt diese Funktion über die [Protokollierungs-API](xref:fundamentals/logging/index)bereit.</span><span class="sxs-lookup"><span data-stu-id="9886b-130">The ASP.NET Core stack provides this functionality through the [Logging API](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="9886b-131">Beispielsweise kann eine Protokollierung mithilfe von konstruktorinjection zum GrpC-Dienst hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="9886b-131">For example, a logger can be added to the gRPC service via constructor injection:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a><span data-ttu-id="9886b-132">HTTPS</span><span class="sxs-lookup"><span data-stu-id="9886b-132">HTTPS</span></span>

<span data-ttu-id="9886b-133">Auf C-Core basierende apps konfigurieren HTTPS über die [Server. Ports-Eigenschaft](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span><span class="sxs-lookup"><span data-stu-id="9886b-133">C-core-based apps configure HTTPS through the [Server.Ports property](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span></span> <span data-ttu-id="9886b-134">Ein ähnliches Konzept wird verwendet, um Server in ASP.net Core zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="9886b-134">A similar concept is used to configure servers in ASP.NET Core.</span></span> <span data-ttu-id="9886b-135">Kestrel verwendet beispielsweise die [Endpunkt Konfiguration](xref:fundamentals/servers/kestrel#endpoint-configuration) für diese Funktion.</span><span class="sxs-lookup"><span data-stu-id="9886b-135">For example, Kestrel uses [endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) for this functionality.</span></span>

## <a name="interceptors-and-middleware"></a><span data-ttu-id="9886b-136">Interceptors und Middleware</span><span class="sxs-lookup"><span data-stu-id="9886b-136">Interceptors and Middleware</span></span>

<span data-ttu-id="9886b-137">ASP.net Core [Middleware](xref:fundamentals/middleware/index) bietet im Vergleich zu Interceptors in C-Core-basierten GrpC-apps ähnliche Funktionen.</span><span class="sxs-lookup"><span data-stu-id="9886b-137">ASP.NET Core [middleware](xref:fundamentals/middleware/index) offers similar functionalities compared to interceptors in C-core-based gRPC apps.</span></span> <span data-ttu-id="9886b-138">Middleware und Interceptors sind konzeptionell identisch mit beiden, um eine Pipeline zu erstellen, die eine GrpC-Anforderung verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="9886b-138">Middleware and interceptors are conceptually the same as both are used to construct a pipeline that handles a gRPC request.</span></span> <span data-ttu-id="9886b-139">Beide ermöglichen die Ausführung von Arbeiten vor oder nach der nächsten Komponente in der Pipeline.</span><span class="sxs-lookup"><span data-stu-id="9886b-139">They both allow work to be performed before or after the next component in the pipeline.</span></span> <span data-ttu-id="9886b-140">ASP.net Core Middleware arbeitet jedoch mit den zugrunde liegenden http/2-Nachrichten, während Interceptors mithilfe von [servercallcontext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html)auf der GrpC-Abstraktions Ebene arbeiten.</span><span class="sxs-lookup"><span data-stu-id="9886b-140">However, ASP.NET Core middleware operates on the underlying HTTP/2 messages, while interceptors operate on the gRPC layer of abstraction using the [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9886b-141">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="9886b-141">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>

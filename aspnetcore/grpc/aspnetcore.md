---
title: gRPC-Dienste mit ASP.NET Core
author: juntaoluo
description: Erfahren Sie, die grundlegenden Konzepten gRPC-Dienste mit ASP.NET Core zu schreiben.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 03/08/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 1f019fac23982a95fa37d43099522f4b3e9d107a
ms.sourcegitcommit: 5d384db2fa9373a93b5d15e985fb34430e49ad7a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66039284"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="11e66-103">gRPC-Dienste mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="11e66-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="11e66-104">Diesem Dokument wird erläutert, wie Sie die ersten Schritte mit gRPC-Services mithilfe von ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="11e66-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-all-3.0.md)]

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="11e66-105">Erste Schritte mit dem gRPC-Dienst in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="11e66-105">Get started with gRPC service in ASP.NET Core</span></span>

[!INCLUDE[View or download sample code](~/includes/grpc/download.md)]

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="11e66-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="11e66-106">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="11e66-107">Finden Sie unter [erste Schritte mit gRPC Services](xref:tutorials/grpc/grpc-start) ausführliche Anleitungen zum Erstellen eines Projekts gRPC.</span><span class="sxs-lookup"><span data-stu-id="11e66-107">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="11e66-108">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="11e66-108">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="11e66-109">Führen Sie `dotnet new grpc -o GrpcGreeter` über die Befehlszeile aus.</span><span class="sxs-lookup"><span data-stu-id="11e66-109">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="11e66-110">Hinzufügen von gRPC-Services zu einer ASP.NET Core-app</span><span class="sxs-lookup"><span data-stu-id="11e66-110">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="11e66-111">gRPC erfordert die folgenden Pakete:</span><span class="sxs-lookup"><span data-stu-id="11e66-111">gRPC requires the following packages:</span></span>

* [<span data-ttu-id="11e66-112">Grpc.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="11e66-112">Grpc.AspNetCore.Server</span></span>](https://www.nuget.org/packages/Grpc.AspNetCore.Server)
* <span data-ttu-id="11e66-113">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/) Protobuf-Nachricht APIs.</span><span class="sxs-lookup"><span data-stu-id="11e66-113">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/) for protobuf message APIs.</span></span>
* [<span data-ttu-id="11e66-114">Grpc.Tools</span><span class="sxs-lookup"><span data-stu-id="11e66-114">Grpc.Tools</span></span>](https://www.nuget.org/packages/Grpc.Tools/)

### <a name="configure-grpc"></a><span data-ttu-id="11e66-115">GRPC konfigurieren</span><span class="sxs-lookup"><span data-stu-id="11e66-115">Configure gRPC</span></span>

<span data-ttu-id="11e66-116">gRPC aktiviert ist, mit der `AddGrpc` Methode:</span><span class="sxs-lookup"><span data-stu-id="11e66-116">gRPC is enabled with the `AddGrpc` method:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeter/Startup.cs?name=snippet&highlight=5)]

<span data-ttu-id="11e66-117">Jeder gRPC-Dienst wird hinzugefügt, die routing-Pipeline über die `MapGrpcService` Methode:</span><span class="sxs-lookup"><span data-stu-id="11e66-117">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeter/Startup.cs?name=snippet&highlight=21)]

<span data-ttu-id="11e66-118">Freigeben von ASP.NET Core-Middleware und Funktionen der routing-Pipeline, die aus diesem Grund kann eine app konfiguriert werden, um zusätzliche Anforderungshandler zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="11e66-118">ASP.NET Core middlewares and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="11e66-119">Die zusätzliche Anforderung-Handler, z. B. MVC-Controller, arbeiten parallel mit den konfigurierten gRPC-Diensten.</span><span class="sxs-lookup"><span data-stu-id="11e66-119">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="11e66-120">Integration in ASP.NET Core-APIs</span><span class="sxs-lookup"><span data-stu-id="11e66-120">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="11e66-121">gRPC-Dienste verfügen über Vollzugriff auf die ASP.NET Core-Features wie z. B. [Dependency Injection](xref:fundamentals/dependency-injection) (DI) und [Protokollierung](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="11e66-121">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="11e66-122">Die Implementierung des Diensts kann beispielsweise einen Dienst Protokollierung von DI-Container über den Konstruktor beheben:</span><span class="sxs-lookup"><span data-stu-id="11e66-122">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="11e66-123">Standardmäßig kann die dienstimplementierung gRPC andere DI-Dienste mit jedem Lebensdauer (Singleton, Scoped oder vorübergehend) beheben.</span><span class="sxs-lookup"><span data-stu-id="11e66-123">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="11e66-124">Beheben von "HttpContext" gRPC-Methoden</span><span class="sxs-lookup"><span data-stu-id="11e66-124">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="11e66-125">Die gRPC-API ermöglicht den Zugriff auf einige Daten für das HTTP/2-Nachricht, z. B. die Methode, Host, Header und Nachspänne.</span><span class="sxs-lookup"><span data-stu-id="11e66-125">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="11e66-126">Der Zugriff erfolgt über die `ServerCallContext` Argument an jede gRPC-Methode übergeben:</span><span class="sxs-lookup"><span data-stu-id="11e66-126">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeter/Services/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="11e66-127">`ServerCallContext` bietet keine vollen Zugriff auf `HttpContext` in allen ASP.NET-APIs.</span><span class="sxs-lookup"><span data-stu-id="11e66-127">`ServerCallContext` does not provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="11e66-128">Die `GetHttpContext` Erweiterungsmethode bietet umfassenden Zugriff auf die `HttpContext` , die die zugrunde liegende HTTP/2-Nachricht in ASP.NET-APIs darstellt:</span><span class="sxs-lookup"><span data-stu-id="11e66-128">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeter/Services/GreeterService.cs?name=snippet1)]

## <a name="additional-resources"></a><span data-ttu-id="11e66-129">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="11e66-129">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>

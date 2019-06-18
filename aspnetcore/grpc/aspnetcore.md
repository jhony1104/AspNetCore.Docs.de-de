---
title: gRPC-Dienste mit ASP.NET Core
author: juntaoluo
description: Erfahren Sie, die grundlegenden Konzepten gRPC-Dienste mit ASP.NET Core zu schreiben.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 03/08/2019
uid: grpc/aspnetcore
ms.openlocfilehash: ca06478e6168c59d9abf43d99213fa8a7091e178
ms.sourcegitcommit: 756114cab5e24e99ec23de62b0c1c16b15197ac2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67169520"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="cde9c-103">gRPC-Dienste mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cde9c-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="cde9c-104">Diesem Dokument wird erläutert, wie Sie die ersten Schritte mit gRPC-Services mithilfe von ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cde9c-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-all-3.0.md)]

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="cde9c-105">Erste Schritte mit dem gRPC-Dienst in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cde9c-105">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="cde9c-106">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="cde9c-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="cde9c-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cde9c-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cde9c-108">Finden Sie unter [erste Schritte mit gRPC Services](xref:tutorials/grpc/grpc-start) ausführliche Anleitungen zum Erstellen eines Projekts gRPC.</span><span class="sxs-lookup"><span data-stu-id="cde9c-108">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="cde9c-109">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="cde9c-109">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="cde9c-110">Führen Sie `dotnet new grpc -o GrpcGreeter` über die Befehlszeile aus.</span><span class="sxs-lookup"><span data-stu-id="cde9c-110">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="cde9c-111">Hinzufügen von gRPC-Services zu einer ASP.NET Core-app</span><span class="sxs-lookup"><span data-stu-id="cde9c-111">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="cde9c-112">gRPC erfordert die folgenden Pakete:</span><span class="sxs-lookup"><span data-stu-id="cde9c-112">gRPC requires the following packages:</span></span>

* [<span data-ttu-id="cde9c-113">Grpc.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="cde9c-113">Grpc.AspNetCore.Server</span></span>](https://www.nuget.org/packages/Grpc.AspNetCore.Server)
* <span data-ttu-id="cde9c-114">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/) Protobuf-Nachricht APIs.</span><span class="sxs-lookup"><span data-stu-id="cde9c-114">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/) for protobuf message APIs.</span></span>
* [<span data-ttu-id="cde9c-115">Grpc.Tools</span><span class="sxs-lookup"><span data-stu-id="cde9c-115">Grpc.Tools</span></span>](https://www.nuget.org/packages/Grpc.Tools/)

### <a name="configure-grpc"></a><span data-ttu-id="cde9c-116">GRPC konfigurieren</span><span class="sxs-lookup"><span data-stu-id="cde9c-116">Configure gRPC</span></span>

<span data-ttu-id="cde9c-117">gRPC aktiviert ist, mit der `AddGrpc` Methode:</span><span class="sxs-lookup"><span data-stu-id="cde9c-117">gRPC is enabled with the `AddGrpc` method:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7)]

<span data-ttu-id="cde9c-118">Jeder gRPC-Dienst wird hinzugefügt, die routing-Pipeline über die `MapGrpcService` Methode:</span><span class="sxs-lookup"><span data-stu-id="cde9c-118">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=24)]

<span data-ttu-id="cde9c-119">Freigeben von ASP.NET Core-Middleware und Funktionen der routing-Pipeline, die aus diesem Grund kann eine app konfiguriert werden, um zusätzliche Anforderungshandler zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="cde9c-119">ASP.NET Core middlewares and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="cde9c-120">Die zusätzliche Anforderung-Handler, z. B. MVC-Controller, arbeiten parallel mit den konfigurierten gRPC-Diensten.</span><span class="sxs-lookup"><span data-stu-id="cde9c-120">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="cde9c-121">Integration in ASP.NET Core-APIs</span><span class="sxs-lookup"><span data-stu-id="cde9c-121">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="cde9c-122">gRPC-Dienste verfügen über Vollzugriff auf die ASP.NET Core-Features wie z. B. [Dependency Injection](xref:fundamentals/dependency-injection) (DI) und [Protokollierung](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="cde9c-122">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="cde9c-123">Die Implementierung des Diensts kann beispielsweise einen Dienst Protokollierung von DI-Container über den Konstruktor beheben:</span><span class="sxs-lookup"><span data-stu-id="cde9c-123">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="cde9c-124">Standardmäßig kann die dienstimplementierung gRPC andere DI-Dienste mit jedem Lebensdauer (Singleton, Scoped oder vorübergehend) beheben.</span><span class="sxs-lookup"><span data-stu-id="cde9c-124">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="cde9c-125">Beheben von "HttpContext" gRPC-Methoden</span><span class="sxs-lookup"><span data-stu-id="cde9c-125">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="cde9c-126">Die gRPC-API ermöglicht den Zugriff auf einige Daten für das HTTP/2-Nachricht, z. B. die Methode, Host, Header und Nachspänne.</span><span class="sxs-lookup"><span data-stu-id="cde9c-126">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="cde9c-127">Der Zugriff erfolgt über die `ServerCallContext` Argument an jede gRPC-Methode übergeben:</span><span class="sxs-lookup"><span data-stu-id="cde9c-127">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="cde9c-128">`ServerCallContext` bietet keine vollen Zugriff auf `HttpContext` in allen ASP.NET-APIs.</span><span class="sxs-lookup"><span data-stu-id="cde9c-128">`ServerCallContext` does not provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="cde9c-129">Die `GetHttpContext` Erweiterungsmethode bietet umfassenden Zugriff auf die `HttpContext` , die die zugrunde liegende HTTP/2-Nachricht in ASP.NET-APIs darstellt:</span><span class="sxs-lookup"><span data-stu-id="cde9c-129">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="cde9c-130">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="cde9c-130">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>

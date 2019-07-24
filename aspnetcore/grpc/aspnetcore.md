---
title: gRPC-Dienste mit ASP.NET Core
author: juntaoluo
description: Lernen Sie die grundlegenden Konzepte kennen, wenn Sie GrpC-Dienste mit ASP.net Core schreiben.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/03/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 02e443dfecf2f7464a8ecabfc0cac67854d63232
ms.sourcegitcommit: f30b18442ed12831c7e86b0db249183ccd749f59
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68412489"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="35063-103">gRPC-Dienste mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="35063-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="35063-104">In diesem Dokument werden die ersten Schritte mit den GrpC-Diensten mithilfe von ASP.net Core erläutert.</span><span class="sxs-lookup"><span data-stu-id="35063-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="35063-105">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="35063-105">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="35063-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="35063-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="35063-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="35063-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="35063-108">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="35063-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="35063-109">Erste Schritte mit dem gRPC-Dienst in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="35063-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="35063-110">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="35063-110">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="35063-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="35063-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="35063-112">Ausführliche Anweisungen zum Erstellen eines GrpC-Projekts finden [Sie unter Get Started with GrpC Services](xref:tutorials/grpc/grpc-start) .</span><span class="sxs-lookup"><span data-stu-id="35063-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="35063-113">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="35063-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="35063-114">Führen Sie `dotnet new grpc -o GrpcGreeter` über die Befehlszeile aus.</span><span class="sxs-lookup"><span data-stu-id="35063-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="35063-115">Hinzufügen von GrpC-Diensten zu einer ASP.net Core-App</span><span class="sxs-lookup"><span data-stu-id="35063-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="35063-116">GrpC erfordert das Paket " [GrpC. aspnetcore](https://www.nuget.org/packages/Grpc.AspNetCore) ".</span><span class="sxs-lookup"><span data-stu-id="35063-116">gRPC requires the [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) package.</span></span>

### <a name="configure-grpc"></a><span data-ttu-id="35063-117">Konfigurieren von GrpC</span><span class="sxs-lookup"><span data-stu-id="35063-117">Configure gRPC</span></span>

<span data-ttu-id="35063-118">GrpC ist mit der `AddGrpc` -Methode aktiviert:</span><span class="sxs-lookup"><span data-stu-id="35063-118">gRPC is enabled with the `AddGrpc` method:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7)]

<span data-ttu-id="35063-119">Jeder GrpC-Dienst wird mithilfe der `MapGrpcService` -Methode zur Routing Pipeline hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="35063-119">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=24)]

<span data-ttu-id="35063-120">ASP.net Core Middlewares und Features nutzen die Routing Pipeline gemeinsam, daher kann eine APP so konfiguriert werden, dass Sie zusätzliche Anforderungs Handler bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="35063-120">ASP.NET Core middlewares and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="35063-121">Die zusätzlichen Anforderungs Handler, z. b. MVC-Controller, arbeiten parallel mit den konfigurierten GrpC-Diensten.</span><span class="sxs-lookup"><span data-stu-id="35063-121">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="35063-122">Integration mit ASP.net Core-APIs</span><span class="sxs-lookup"><span data-stu-id="35063-122">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="35063-123">GrpC-Dienste verfügen über Vollzugriff auf die ASP.net Core Features wie z. b. [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (di) und [Protokollierung](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="35063-123">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="35063-124">Die Dienst Implementierung kann z. b. einen Protokollierungs Dienst aus dem di-Container über den-Konstruktor auflösen:</span><span class="sxs-lookup"><span data-stu-id="35063-124">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="35063-125">Standardmäßig kann die GrpC-Dienst Implementierung andere di-Dienste mit beliebiger Lebensdauer (Singleton, Bereich oder vorübergehend) auflösen.</span><span class="sxs-lookup"><span data-stu-id="35063-125">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="35063-126">Auflösen von HttpContext in den GrpC-Methoden</span><span class="sxs-lookup"><span data-stu-id="35063-126">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="35063-127">Die GrpC-API ermöglicht den Zugriff auf einige http/2-Nachrichten Daten, wie z. b. die Methode, den Host, den Header und die Nachspann.</span><span class="sxs-lookup"><span data-stu-id="35063-127">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="35063-128">Der Zugriff erfolgt über `ServerCallContext` das Argument, das an jede GrpC-Methode übermittelt wird:</span><span class="sxs-lookup"><span data-stu-id="35063-128">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="35063-129">`ServerCallContext`bietet keinen Vollzugriff auf `HttpContext` in allen ASP.NET-APIs.</span><span class="sxs-lookup"><span data-stu-id="35063-129">`ServerCallContext` does not provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="35063-130">Die `GetHttpContext` Erweiterungsmethode bietet vollen Zugriff auf das `HttpContext` , das die zugrunde liegende http/2-Nachricht in ASP.NET-APIs darstellt:</span><span class="sxs-lookup"><span data-stu-id="35063-130">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="35063-131">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="35063-131">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>

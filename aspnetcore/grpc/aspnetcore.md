---
title: gRPC-Dienste mit ASP.NET Core
author: juntaoluo
description: Lernen Sie die grundlegenden Konzepte kennen, wenn Sie GrpC-Dienste mit ASP.net Core schreiben.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 08/07/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 26f0d7610151460967b97665ed61deab1ef56d68
ms.sourcegitcommit: 2719c70cd15a430479ab4007ff3e197fbf5dfee0
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68862939"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="42f4f-103">gRPC-Dienste mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="42f4f-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="42f4f-104">In diesem Dokument werden die ersten Schritte mit den GrpC-Diensten mithilfe von ASP.net Core erläutert.</span><span class="sxs-lookup"><span data-stu-id="42f4f-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="42f4f-105">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="42f4f-105">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="42f4f-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="42f4f-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="42f4f-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="42f4f-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="42f4f-108">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="42f4f-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="42f4f-109">Erste Schritte mit dem gRPC-Dienst in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="42f4f-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="42f4f-110">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="42f4f-110">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="42f4f-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="42f4f-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="42f4f-112">Ausführliche Anweisungen zum Erstellen eines GrpC-Projekts finden [Sie unter Get Started with GrpC Services](xref:tutorials/grpc/grpc-start) .</span><span class="sxs-lookup"><span data-stu-id="42f4f-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="42f4f-113">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="42f4f-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="42f4f-114">Führen Sie `dotnet new grpc -o GrpcGreeter` über die Befehlszeile aus.</span><span class="sxs-lookup"><span data-stu-id="42f4f-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="42f4f-115">Hinzufügen von GrpC-Diensten zu einer ASP.net Core-App</span><span class="sxs-lookup"><span data-stu-id="42f4f-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="42f4f-116">GrpC erfordert das Paket " [GrpC. aspnetcore](https://www.nuget.org/packages/Grpc.AspNetCore) ".</span><span class="sxs-lookup"><span data-stu-id="42f4f-116">gRPC requires the [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) package.</span></span>

### <a name="configure-grpc"></a><span data-ttu-id="42f4f-117">Konfigurieren von GrpC</span><span class="sxs-lookup"><span data-stu-id="42f4f-117">Configure gRPC</span></span>

<span data-ttu-id="42f4f-118">GrpC ist mit der `AddGrpc` -Methode aktiviert:</span><span class="sxs-lookup"><span data-stu-id="42f4f-118">gRPC is enabled with the `AddGrpc` method:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7)]

<span data-ttu-id="42f4f-119">Jeder GrpC-Dienst wird mithilfe der `MapGrpcService` -Methode zur Routing Pipeline hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="42f4f-119">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=24)]

<span data-ttu-id="42f4f-120">ASP.net Core Middlewares und Features nutzen die Routing Pipeline gemeinsam, daher kann eine APP so konfiguriert werden, dass Sie zusätzliche Anforderungs Handler bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="42f4f-120">ASP.NET Core middlewares and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="42f4f-121">Die zusätzlichen Anforderungs Handler, z. b. MVC-Controller, arbeiten parallel mit den konfigurierten GrpC-Diensten.</span><span class="sxs-lookup"><span data-stu-id="42f4f-121">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="42f4f-122">Integration mit ASP.net Core-APIs</span><span class="sxs-lookup"><span data-stu-id="42f4f-122">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="42f4f-123">GrpC-Dienste verfügen über Vollzugriff auf die ASP.net Core Features wie z. b. [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (di) und [Protokollierung](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="42f4f-123">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="42f4f-124">Die Dienst Implementierung kann z. b. einen Protokollierungs Dienst aus dem di-Container über den-Konstruktor auflösen:</span><span class="sxs-lookup"><span data-stu-id="42f4f-124">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="42f4f-125">Standardmäßig kann die GrpC-Dienst Implementierung andere di-Dienste mit beliebiger Lebensdauer (Singleton, Bereich oder vorübergehend) auflösen.</span><span class="sxs-lookup"><span data-stu-id="42f4f-125">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="42f4f-126">Auflösen von HttpContext in den GrpC-Methoden</span><span class="sxs-lookup"><span data-stu-id="42f4f-126">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="42f4f-127">Die GrpC-API ermöglicht den Zugriff auf einige http/2-Nachrichten Daten, wie z. b. die Methode, den Host, den Header und die Nachspann.</span><span class="sxs-lookup"><span data-stu-id="42f4f-127">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="42f4f-128">Der Zugriff erfolgt über `ServerCallContext` das Argument, das an jede GrpC-Methode übermittelt wird:</span><span class="sxs-lookup"><span data-stu-id="42f4f-128">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="42f4f-129">`ServerCallContext`bietet keinen Vollzugriff auf `HttpContext` in allen ASP.NET-APIs.</span><span class="sxs-lookup"><span data-stu-id="42f4f-129">`ServerCallContext` does not provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="42f4f-130">Die `GetHttpContext` Erweiterungsmethode bietet vollen Zugriff auf das `HttpContext` , das die zugrunde liegende http/2-Nachricht in ASP.NET-APIs darstellt:</span><span class="sxs-lookup"><span data-stu-id="42f4f-130">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]

## <a name="grpc-and-aspnet-core-on-macos"></a><span data-ttu-id="42f4f-131">GrpC und ASP.net Core unter macOS</span><span class="sxs-lookup"><span data-stu-id="42f4f-131">gRPC and ASP.NET Core on macOS</span></span>

<span data-ttu-id="42f4f-132">Kestrel unterstützt HTTP/2 mit [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) unter macOS nicht.</span><span class="sxs-lookup"><span data-stu-id="42f4f-132">Kestrel doesn't support HTTP/2 with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) on macOS.</span></span> <span data-ttu-id="42f4f-133">Die ASP.net Core GrpC-Vorlage und-Beispiele verwenden standardmäßig TLS.</span><span class="sxs-lookup"><span data-stu-id="42f4f-133">The ASP.NET Core gRPC template and samples use TLS by default.</span></span> <span data-ttu-id="42f4f-134">Wenn Sie versuchen, den GrpC-Server zu starten, wird die folgende Fehlermeldung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="42f4f-134">You'll see the following error message when you attempt to start the gRPC server:</span></span>

> <span data-ttu-id="42f4f-135">Bindung https://localhost:5001 an an der IPv4-Loopback Schnittstelle nicht möglich: HTTP/2 über TLS wird unter macOS aufgrund fehlender alpn-Unterstützung nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="42f4f-135">Unable to bind to https://localhost:5001 on the IPv4 loopback interface: 'HTTP/2 over TLS is not supported on macOS due to missing ALPN support.'.</span></span>

<span data-ttu-id="42f4f-136">Um dieses Problem zu umgehen, konfigurieren Sie Kestrel und den GrpC-Client für die Verwendung von http/2 **ohne** TLS.</span><span class="sxs-lookup"><span data-stu-id="42f4f-136">To work around this issue, configure Kestrel and the gRPC client to use HTTP/2 **without** TLS.</span></span> <span data-ttu-id="42f4f-137">Dies sollte nur während der Entwicklung durchzuführen sein.</span><span class="sxs-lookup"><span data-stu-id="42f4f-137">You should only do this during development.</span></span> <span data-ttu-id="42f4f-138">Die Verwendung von TLS führt dazu, dass GrpC-Nachrichten ohne Verschlüsselung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="42f4f-138">Not using TLS will result in gRPC messages being sent without encryption.</span></span>

<span data-ttu-id="42f4f-139">Kestrel muss einen http/2-Endpunkt ohne TLS in `Program.cs`konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="42f4f-139">Kestrel must configure a HTTP/2 endpoint without TLS in `Program.cs`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                // Setup a HTTP/2 endpoint without TLS.
                options.ListenLocalhost(5000, o => o.Protocols = HttpProtocols.Http2);
            });
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="42f4f-140">Der GrpC-Client muss den `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` Switch auf `true` festlegen und `http` in der Server Adresse verwenden:</span><span class="sxs-lookup"><span data-stu-id="42f4f-140">The gRPC client must set the `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` switch to `true` and use `http` in the server address:</span></span>

```csharp
// This switch must be set before creating the HttpClient.
AppContext.SetSwitch("System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

var httpClient = new HttpClient();
// The port number(5000) must match the port of the gRPC server.
httpClient.BaseAddress = new Uri("http://localhost:5000");
var client = GrpcClient.Create<Greeter.GreeterClient>(httpClient);
```

> [!WARNING]
> <span data-ttu-id="42f4f-141">HTTP/2 ohne TLS sollte nur während der APP-Entwicklung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="42f4f-141">HTTP/2 without TLS should only be used during app development.</span></span> <span data-ttu-id="42f4f-142">Produktionsanwendungen sollten immer Transportsicherheit verwenden.</span><span class="sxs-lookup"><span data-stu-id="42f4f-142">Production applications should always use transport security.</span></span> <span data-ttu-id="42f4f-143">Weitere Informationen finden Sie unter [Sicherheitsüberlegungen in GrpC für ASP.net Core](xref:grpc/security#transport-security).</span><span class="sxs-lookup"><span data-stu-id="42f4f-143">For more information, see [Security considerations in gRPC for ASP.NET Core](xref:grpc/security#transport-security).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="42f4f-144">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="42f4f-144">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>

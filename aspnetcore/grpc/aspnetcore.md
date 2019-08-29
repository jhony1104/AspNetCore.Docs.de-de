---
title: gRPC-Dienste mit ASP.NET Core
author: juntaoluo
description: Lernen Sie die grundlegenden Konzepte kennen, wenn Sie GrpC-Dienste mit ASP.net Core schreiben.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 08/28/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 128f5b36eac9112460c33693db5537134a077476
ms.sourcegitcommit: 23f79bd71d49c4efddb56377c1f553cc993d781b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70130703"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="b4f24-103">gRPC-Dienste mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b4f24-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="b4f24-104">In diesem Dokument werden die ersten Schritte mit den GrpC-Diensten mithilfe von ASP.net Core erläutert.</span><span class="sxs-lookup"><span data-stu-id="b4f24-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b4f24-105">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="b4f24-105">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b4f24-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b4f24-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b4f24-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b4f24-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="b4f24-108">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="b4f24-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="b4f24-109">Erste Schritte mit dem gRPC-Dienst in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b4f24-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="b4f24-110">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="b4f24-110">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b4f24-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b4f24-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b4f24-112">Ausführliche Anweisungen zum Erstellen eines GrpC-Projekts finden [Sie unter Get Started with GrpC Services](xref:tutorials/grpc/grpc-start) .</span><span class="sxs-lookup"><span data-stu-id="b4f24-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="b4f24-113">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="b4f24-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="b4f24-114">Führen Sie `dotnet new grpc -o GrpcGreeter` über die Befehlszeile aus.</span><span class="sxs-lookup"><span data-stu-id="b4f24-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="b4f24-115">Hinzufügen von GrpC-Diensten zu einer ASP.net Core-App</span><span class="sxs-lookup"><span data-stu-id="b4f24-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="b4f24-116">GrpC erfordert das Paket " [GrpC. aspnetcore](https://www.nuget.org/packages/Grpc.AspNetCore) ".</span><span class="sxs-lookup"><span data-stu-id="b4f24-116">gRPC requires the [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) package.</span></span>

### <a name="configure-grpc"></a><span data-ttu-id="b4f24-117">Konfigurieren von GrpC</span><span class="sxs-lookup"><span data-stu-id="b4f24-117">Configure gRPC</span></span>

<span data-ttu-id="b4f24-118">In *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="b4f24-118">In *Startup.cs*:</span></span>

* <span data-ttu-id="b4f24-119">GrpC ist mit der `AddGrpc` -Methode aktiviert.</span><span class="sxs-lookup"><span data-stu-id="b4f24-119">gRPC is enabled with the `AddGrpc` method.</span></span>
* <span data-ttu-id="b4f24-120">Jeder GrpC-Dienst wird mithilfe der `MapGrpcService` -Methode zur Routing Pipeline hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="b4f24-120">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]

<span data-ttu-id="b4f24-121">ASP.net Core Middlewares und Features nutzen die Routing Pipeline gemeinsam, daher kann eine APP so konfiguriert werden, dass Sie zusätzliche Anforderungs Handler bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="b4f24-121">ASP.NET Core middlewares and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="b4f24-122">Die zusätzlichen Anforderungs Handler, z. b. MVC-Controller, arbeiten parallel mit den konfigurierten GrpC-Diensten.</span><span class="sxs-lookup"><span data-stu-id="b4f24-122">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

### <a name="configure-kestrel"></a><span data-ttu-id="b4f24-123">Konfigurieren von Kestrel</span><span class="sxs-lookup"><span data-stu-id="b4f24-123">Configure Kestrel</span></span>

<span data-ttu-id="b4f24-124">Kestrel-GrpC-Endpunkte:</span><span class="sxs-lookup"><span data-stu-id="b4f24-124">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="b4f24-125">Erfordert http/2.</span><span class="sxs-lookup"><span data-stu-id="b4f24-125">Require HTTP/2.</span></span>
* <span data-ttu-id="b4f24-126">Sollte mit HTTPS gesichert werden.</span><span class="sxs-lookup"><span data-stu-id="b4f24-126">Should be secured with HTTPS.</span></span>

#### <a name="http2"></a><span data-ttu-id="b4f24-127">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="b4f24-127">HTTP/2</span></span>

<span data-ttu-id="b4f24-128">Kestrel [unterstützt HTTP/2](xref:fundamentals/servers/kestrel#http2-support) auf den meisten modernen Betriebssystemen.</span><span class="sxs-lookup"><span data-stu-id="b4f24-128">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel#http2-support) on most modern operating systems.</span></span> <span data-ttu-id="b4f24-129">Kestrel-Endpunkte werden so konfiguriert, dass HTTP/1.1-und http/2-Verbindungen standardmäßig unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="b4f24-129">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

> [!NOTE]
> <span data-ttu-id="b4f24-130">ASP.net Core GrpC mit [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246)wird von macOS nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="b4f24-130">macOS doesn't support ASP.NET Core gRPC with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="b4f24-131">Zum erfolgreichen Ausführen von gRPC-Diensten unter macOS ist eine zusätzliche Konfiguration erforderlich.</span><span class="sxs-lookup"><span data-stu-id="b4f24-131">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="b4f24-132">Weitere Informationen finden Sie unter [ASP.NET Core gRPC-App kann unter macOS nicht gestartet werden](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="b4f24-132">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

#### <a name="https"></a><span data-ttu-id="b4f24-133">HTTPS</span><span class="sxs-lookup"><span data-stu-id="b4f24-133">HTTPS</span></span>

<span data-ttu-id="b4f24-134">Kestrel-Endpunkte, die für GrpC verwendet werden, sollten mit HTTPS gesichert werden.</span><span class="sxs-lookup"><span data-stu-id="b4f24-134">Kestrel endpoints used for gRPC should be secured with HTTPS.</span></span> <span data-ttu-id="b4f24-135">In der Entwicklung wird ein HTTPS-Endpunkt automatisch erstellt `https://localhost:5001` , wenn das ASP.net Core-Entwicklungs Zertifikat vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="b4f24-135">In development, an HTTPS endpoint is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="b4f24-136">Es ist keine Konfiguration erforderlich.</span><span class="sxs-lookup"><span data-stu-id="b4f24-136">No configuration is required.</span></span>

<span data-ttu-id="b4f24-137">In der Produktion muss HTTPS explizit konfiguriert sein.</span><span class="sxs-lookup"><span data-stu-id="b4f24-137">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="b4f24-138">Im folgenden *appSettings. JSON* -Beispiel wird ein http/2-Endpunkt bereitgestellt, der mit HTTPS gesichert ist:</span><span class="sxs-lookup"><span data-stu-id="b4f24-138">In the following *appsettings.json* example, an HTTP/2 endpoint secured with HTTPS is provided:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http2"
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="b4f24-139">Alternativ können Sie Kestrel-endspoint in *Program.cs*konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="b4f24-139">Alternatively, Kestrel endspoints can be configured in *Program.cs*:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                // This endpoint will use HTTP/2 and HTTPS on port 5001.
                options.Listen(IPAddress.Any, 5001, listenOptions =>
                {
                    listenOptions.Protocols = HttpProtocols.Http2;
                    listenOptions.UseHttps("<path to .pfx file>", 
                        "<certificate password>");
                });
            });
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="b4f24-140">Weitere Informationen zum Aktivieren von http/2 und HTTPS mit Kestrel finden Sie unter [Kestrel-Endpunkt Konfiguration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="b4f24-140">For more information on enabling HTTP/2 and HTTPS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="b4f24-141">Integration mit ASP.net Core-APIs</span><span class="sxs-lookup"><span data-stu-id="b4f24-141">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="b4f24-142">GrpC-Dienste verfügen über Vollzugriff auf die ASP.net Core Features wie z. b. [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (di) und [Protokollierung](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="b4f24-142">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="b4f24-143">Die Dienst Implementierung kann z. b. einen Protokollierungs Dienst aus dem di-Container über den-Konstruktor auflösen:</span><span class="sxs-lookup"><span data-stu-id="b4f24-143">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="b4f24-144">Standardmäßig kann die GrpC-Dienst Implementierung andere di-Dienste mit beliebiger Lebensdauer (Singleton, Bereich oder vorübergehend) auflösen.</span><span class="sxs-lookup"><span data-stu-id="b4f24-144">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="b4f24-145">Auflösen von HttpContext in den GrpC-Methoden</span><span class="sxs-lookup"><span data-stu-id="b4f24-145">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="b4f24-146">Die GrpC-API ermöglicht den Zugriff auf einige http/2-Nachrichten Daten, wie z. b. die Methode, den Host, den Header und die Nachspann.</span><span class="sxs-lookup"><span data-stu-id="b4f24-146">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="b4f24-147">Der Zugriff erfolgt über `ServerCallContext` das Argument, das an jede GrpC-Methode übermittelt wird:</span><span class="sxs-lookup"><span data-stu-id="b4f24-147">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="b4f24-148">`ServerCallContext`bietet keinen Vollzugriff auf `HttpContext` in allen ASP.NET-APIs.</span><span class="sxs-lookup"><span data-stu-id="b4f24-148">`ServerCallContext` does not provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="b4f24-149">Die `GetHttpContext` Erweiterungsmethode bietet vollen Zugriff auf das `HttpContext` , das die zugrunde liegende http/2-Nachricht in ASP.NET-APIs darstellt:</span><span class="sxs-lookup"><span data-stu-id="b4f24-149">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="b4f24-150">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="b4f24-150">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>

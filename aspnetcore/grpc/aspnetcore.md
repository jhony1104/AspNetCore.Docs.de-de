---
title: gRPC-Dienste mit ASP.NET Core
author: juntaoluo
description: Lernen Sie die grundlegenden Konzepte kennen, wenn Sie GrpC-Dienste mit ASP.net Core schreiben.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/03/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 18a6dd2ddd4f3c3c4466e3b96dd1748fd0972e39
ms.sourcegitcommit: fae6f0e253f9d62d8f39de5884d2ba2b4b2a6050
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71250802"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="1465f-103">gRPC-Dienste mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1465f-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="1465f-104">In diesem Dokument werden die ersten Schritte mit den GrpC-Diensten mithilfe von ASP.net Core erläutert.</span><span class="sxs-lookup"><span data-stu-id="1465f-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1465f-105">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="1465f-105">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1465f-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1465f-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1465f-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1465f-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1465f-108">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="1465f-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="1465f-109">Erste Schritte mit dem gRPC-Dienst in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1465f-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="1465f-110">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="1465f-110">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1465f-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1465f-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1465f-112">Ausführliche Anweisungen zum Erstellen eines GrpC-Projekts finden [Sie unter Get Started with GrpC Services](xref:tutorials/grpc/grpc-start) .</span><span class="sxs-lookup"><span data-stu-id="1465f-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="1465f-113">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="1465f-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="1465f-114">Führen Sie `dotnet new grpc -o GrpcGreeter` über die Befehlszeile aus.</span><span class="sxs-lookup"><span data-stu-id="1465f-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="1465f-115">Hinzufügen von GrpC-Diensten zu einer ASP.net Core-App</span><span class="sxs-lookup"><span data-stu-id="1465f-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="1465f-116">GrpC erfordert das Paket " [GrpC. aspnetcore](https://www.nuget.org/packages/Grpc.AspNetCore) ".</span><span class="sxs-lookup"><span data-stu-id="1465f-116">gRPC requires the [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) package.</span></span>

### <a name="configure-grpc"></a><span data-ttu-id="1465f-117">Konfigurieren von GrpC</span><span class="sxs-lookup"><span data-stu-id="1465f-117">Configure gRPC</span></span>

<span data-ttu-id="1465f-118">In *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="1465f-118">In *Startup.cs*:</span></span>

* <span data-ttu-id="1465f-119">GrpC ist mit der `AddGrpc` -Methode aktiviert.</span><span class="sxs-lookup"><span data-stu-id="1465f-119">gRPC is enabled with the `AddGrpc` method.</span></span>
* <span data-ttu-id="1465f-120">Jeder GrpC-Dienst wird mithilfe der `MapGrpcService` -Methode zur Routing Pipeline hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="1465f-120">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]

<span data-ttu-id="1465f-121">ASP.net Core Middlewares und Features nutzen die Routing Pipeline gemeinsam, daher kann eine APP so konfiguriert werden, dass Sie zusätzliche Anforderungs Handler bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="1465f-121">ASP.NET Core middlewares and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="1465f-122">Die zusätzlichen Anforderungs Handler, z. b. MVC-Controller, arbeiten parallel mit den konfigurierten GrpC-Diensten.</span><span class="sxs-lookup"><span data-stu-id="1465f-122">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

### <a name="configure-kestrel"></a><span data-ttu-id="1465f-123">Konfigurieren von Kestrel</span><span class="sxs-lookup"><span data-stu-id="1465f-123">Configure Kestrel</span></span>

<span data-ttu-id="1465f-124">Kestrel-GrpC-Endpunkte:</span><span class="sxs-lookup"><span data-stu-id="1465f-124">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="1465f-125">Erfordert http/2.</span><span class="sxs-lookup"><span data-stu-id="1465f-125">Require HTTP/2.</span></span>
* <span data-ttu-id="1465f-126">Muss mit [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246)gesichert werden.</span><span class="sxs-lookup"><span data-stu-id="1465f-126">Should be secured with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span>

#### <a name="http2"></a><span data-ttu-id="1465f-127">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="1465f-127">HTTP/2</span></span>

<span data-ttu-id="1465f-128">GrpC erfordert http/2.</span><span class="sxs-lookup"><span data-stu-id="1465f-128">gRPC requires HTTP/2.</span></span> <span data-ttu-id="1465f-129">GrpC für ASP.net Core überprüft, dass [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/2`den Wert hat.</span><span class="sxs-lookup"><span data-stu-id="1465f-129">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) is `HTTP/2`.</span></span>

<span data-ttu-id="1465f-130">Kestrel [unterstützt HTTP/2](xref:fundamentals/servers/kestrel#http2-support) auf den meisten modernen Betriebssystemen.</span><span class="sxs-lookup"><span data-stu-id="1465f-130">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel#http2-support) on most modern operating systems.</span></span> <span data-ttu-id="1465f-131">Kestrel-Endpunkte werden so konfiguriert, dass HTTP/1.1-und http/2-Verbindungen standardmäßig unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="1465f-131">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

#### <a name="tls"></a><span data-ttu-id="1465f-132">TLS</span><span class="sxs-lookup"><span data-stu-id="1465f-132">TLS</span></span>

<span data-ttu-id="1465f-133">Kestrel-Endpunkte, die für GrpC verwendet werden, sollten mit TLS gesichert werden.</span><span class="sxs-lookup"><span data-stu-id="1465f-133">Kestrel endpoints used for gRPC should be secured with TLS.</span></span> <span data-ttu-id="1465f-134">In der Entwicklung wird ein Endpunkt, der mit TLS gesichert ist `https://localhost:5001` , automatisch erstellt, wenn das ASP.net Core-Entwicklungs Zertifikat vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="1465f-134">In development, an endpoint secured with TLS is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="1465f-135">Es ist keine Konfiguration erforderlich.</span><span class="sxs-lookup"><span data-stu-id="1465f-135">No configuration is required.</span></span> <span data-ttu-id="1465f-136">Ein `https` Präfix überprüft, ob der Kestrel-Endpunkt TLS verwendet.</span><span class="sxs-lookup"><span data-stu-id="1465f-136">An `https` prefix verifies the Kestrel endpoint is using TLS.</span></span>

<span data-ttu-id="1465f-137">In der Produktionsumgebung muss TLS explizit konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="1465f-137">In production, TLS must be explicitly configured.</span></span> <span data-ttu-id="1465f-138">Im folgenden *appSettings. JSON* -Beispiel wird ein http/2-Endpunkt bereitgestellt, der mit TLS gesichert ist:</span><span class="sxs-lookup"><span data-stu-id="1465f-138">In the following *appsettings.json* example, an HTTP/2 endpoint secured with TLS is provided:</span></span>

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

<span data-ttu-id="1465f-139">Alternativ können Kestrel-Endpunkte in *Program.cs*konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="1465f-139">Alternatively, Kestrel endpoints can be configured in *Program.cs*:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

#### <a name="protocol-negotiation"></a><span data-ttu-id="1465f-140">Protokoll Aushandlung</span><span class="sxs-lookup"><span data-stu-id="1465f-140">Protocol negotiation</span></span>

<span data-ttu-id="1465f-141">TLS wird für mehr als das Sichern der Kommunikation verwendet.</span><span class="sxs-lookup"><span data-stu-id="1465f-141">TLS is used for more than securing communication.</span></span> <span data-ttu-id="1465f-142">Der Rahmen der TLS [-Protokoll Aushandlung (Application Layer Protocol Aushandlung, alpn)](https://tools.ietf.org/html/rfc7301#section-3) wird verwendet, um das Verbindungsprotokoll zwischen dem Client und dem Server auszuhandeln, wenn ein Endpunkt mehrere Protokolle unterstützt.</span><span class="sxs-lookup"><span data-stu-id="1465f-142">The TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake is used to negotiate the connection protocol between the client and the server when an endpoint supports multiple protocols.</span></span> <span data-ttu-id="1465f-143">Diese Aushandlung bestimmt, ob die Verbindung http/1.1 oder http/2 verwendet.</span><span class="sxs-lookup"><span data-stu-id="1465f-143">This negotiation determines whether the connection uses HTTP/1.1 or HTTP/2.</span></span>

<span data-ttu-id="1465f-144">Wenn ein http/2-Endpunkt ohne TLS konfiguriert ist, müssen die [listenoptions.-Protokolle](xref:fundamentals/servers/kestrel#listenoptionsprotocols) des Endpunkts `HttpProtocols.Http2`auf festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="1465f-144">If an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="1465f-145">Ein Endpunkt mit mehreren Protokollen (z `HttpProtocols.Http1AndHttp2`. b.) kann ohne TLS nicht verwendet werden, da keine Aushandlung vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="1465f-145">An endpoint with multiple protocols (for example, `HttpProtocols.Http1AndHttp2`) can't be used without TLS because there is no negotiation.</span></span> <span data-ttu-id="1465f-146">Alle Verbindungen mit dem ungesicherten Endpunkt werden standardmäßig auf HTTP/1.1 und GrpC-Aufrufe nicht bestanden.</span><span class="sxs-lookup"><span data-stu-id="1465f-146">All connections to the unsecured endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="1465f-147">Weitere Informationen zum Aktivieren von http/2 und TLS mit Kestrel finden Sie unter [Kestrel-Endpunkt Konfiguration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="1465f-147">For more information on enabling HTTP/2 and TLS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!NOTE]
> <span data-ttu-id="1465f-148">macOS unterstützt ASP.NET Core gRPC mit TLS nicht.</span><span class="sxs-lookup"><span data-stu-id="1465f-148">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="1465f-149">Zum erfolgreichen Ausführen von gRPC-Diensten unter macOS ist eine zusätzliche Konfiguration erforderlich.</span><span class="sxs-lookup"><span data-stu-id="1465f-149">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="1465f-150">Weitere Informationen finden Sie unter [ASP.NET Core gRPC-App kann unter macOS nicht gestartet werden](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="1465f-150">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="1465f-151">Integration mit ASP.net Core-APIs</span><span class="sxs-lookup"><span data-stu-id="1465f-151">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="1465f-152">GrpC-Dienste verfügen über Vollzugriff auf die ASP.net Core Features wie z. b. [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (di) und [Protokollierung](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="1465f-152">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="1465f-153">Die Dienst Implementierung kann z. b. einen Protokollierungs Dienst aus dem di-Container über den-Konstruktor auflösen:</span><span class="sxs-lookup"><span data-stu-id="1465f-153">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="1465f-154">Standardmäßig kann die GrpC-Dienst Implementierung andere di-Dienste mit beliebiger Lebensdauer (Singleton, Bereich oder vorübergehend) auflösen.</span><span class="sxs-lookup"><span data-stu-id="1465f-154">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="1465f-155">Auflösen von HttpContext in den GrpC-Methoden</span><span class="sxs-lookup"><span data-stu-id="1465f-155">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="1465f-156">Die GrpC-API ermöglicht den Zugriff auf einige http/2-Nachrichten Daten, wie z. b. die Methode, den Host, den Header und die Nachspann.</span><span class="sxs-lookup"><span data-stu-id="1465f-156">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="1465f-157">Der Zugriff erfolgt über `ServerCallContext` das Argument, das an jede GrpC-Methode übermittelt wird:</span><span class="sxs-lookup"><span data-stu-id="1465f-157">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="1465f-158">`ServerCallContext`bietet keinen Vollzugriff auf `HttpContext` in allen ASP.NET-APIs.</span><span class="sxs-lookup"><span data-stu-id="1465f-158">`ServerCallContext` does not provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="1465f-159">Die `GetHttpContext` Erweiterungsmethode bietet vollen Zugriff auf das `HttpContext` , das die zugrunde liegende http/2-Nachricht in ASP.NET-APIs darstellt:</span><span class="sxs-lookup"><span data-stu-id="1465f-159">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="1465f-160">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="1465f-160">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>

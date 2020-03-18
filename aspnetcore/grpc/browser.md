---
title: Verwenden von gRPC in Browser-Apps
author: jamesnk
description: Erfahren Sie, wie Sie gRPC-Dienste für ASP.NET Core so konfigurieren, dass sie mit gRPC-Web von Browser-Apps aus aufgerufen werden können.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 02/16/2020
uid: grpc/browser
ms.openlocfilehash: 3beeffc26ffd3c2dc85bfc22a46d97d5fd78d3d0
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78649417"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="634c3-103">Verwenden von gRPC in Browser-Apps</span><span class="sxs-lookup"><span data-stu-id="634c3-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="634c3-104">Von [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="634c3-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="634c3-105">**gRPC-Web-Unterstützung in .NET ist experimentell**</span><span class="sxs-lookup"><span data-stu-id="634c3-105">**gRPC-Web support in .NET is experimental**</span></span>
>
> <span data-ttu-id="634c3-106">gRPC-Web für .NET ist ein experimentelles Projekt, kein committetes Produkt.</span><span class="sxs-lookup"><span data-stu-id="634c3-106">gRPC-Web for .NET is an experimental project, not a committed product.</span></span> <span data-ttu-id="634c3-107">Wir möchten Folgendes:</span><span class="sxs-lookup"><span data-stu-id="634c3-107">We want to:</span></span>
>
> * <span data-ttu-id="634c3-108">Testen, ob unser Ansatz zur Implementierung von gRPC-Web funktioniert.</span><span class="sxs-lookup"><span data-stu-id="634c3-108">Test that our approach to implementing gRPC-Web works.</span></span>
> * <span data-ttu-id="634c3-109">Feedback erhalten, ob dieser Ansatz für .NET-Entwickler im Vergleich zu der traditionellen Art der Einrichtung von gRPC-Web über einen Proxy nützlich ist.</span><span class="sxs-lookup"><span data-stu-id="634c3-109">Get feedback on if this approach is useful to .NET developers compared to the traditional way of setting up gRPC-Web via a proxy.</span></span>
>
> <span data-ttu-id="634c3-110">Hinterlassen Sie Ihr Feedback unter [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet), um sicherzustellen, dass wir etwas entwickeln, das den Entwicklern gefällt und mit dem sie produktiv sind.</span><span class="sxs-lookup"><span data-stu-id="634c3-110">Please leave feedback at [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="634c3-111">Es ist nicht möglich, einen HTTP/2-gRPC-Dienst von einer browserbasierten App aus aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="634c3-111">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="634c3-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) ist ein Protokoll, das es JavaScript- und Blazor-Apps im Browser erlaubt, gRPC-Dienste aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="634c3-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="634c3-113">In diesem Artikel wird erläutert, wie gRPC-Web in .NET Core verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="634c3-113">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="634c3-114">Konfigurieren von gRPC-Web in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="634c3-114">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="634c3-115">gRPC-Dienste, die in ASP.NET Core gehostet werden, können so konfiguriert werden, dass sie neben HTTP/2 gRPC auch gRPC-Web unterstützen.</span><span class="sxs-lookup"><span data-stu-id="634c3-115">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="634c3-116">Für gRPC-Web sind keine Änderungen der Dienste erforderlich.</span><span class="sxs-lookup"><span data-stu-id="634c3-116">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="634c3-117">Die einzige Änderung ist die Startkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="634c3-117">The only modification is startup configuration.</span></span>

<span data-ttu-id="634c3-118">So aktivieren Sie gRPC-Web mit einem ASP.NET Core gRPC-Dienst</span><span class="sxs-lookup"><span data-stu-id="634c3-118">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="634c3-119">Fügen Sie einen Verweis auf das Paket [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) hinzu.</span><span class="sxs-lookup"><span data-stu-id="634c3-119">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="634c3-120">Konfigurieren Sie die App zur Verwendung von gRPC-Web, indem Sie `AddGrpcWeb` und `UseGrpcWeb` zu *Startup.cs* hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="634c3-120">Configure the app to use gRPC-Web by adding `AddGrpcWeb` and `UseGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="634c3-121">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="634c3-121">The preceding code:</span></span>

* <span data-ttu-id="634c3-122">Fügt die gRPC-Web-Middleware, `UseGrpcWeb`, nach dem Routing und vor den Endpunkten hinzu.</span><span class="sxs-lookup"><span data-stu-id="634c3-122">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="634c3-123">Gibt die Methode `endpoints.MapGrpcService<GreeterService>()` an, die gRPC-Web mit `EnableGrpcWeb` unterstützt.</span><span class="sxs-lookup"><span data-stu-id="634c3-123">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="634c3-124">Alternativ können Sie alle Dienste zur Unterstützung von gRPC-Web konfigurieren, indem Sie `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` zu ConfigureServices hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="634c3-124">Alternatively, configure all services to support gRPC-Web by adding `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` to ConfigureServices.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=6,13)]

### <a name="grpc-web-and-cors"></a><span data-ttu-id="634c3-125">gRPC-Web und CORS</span><span class="sxs-lookup"><span data-stu-id="634c3-125">gRPC-Web and CORS</span></span>

<span data-ttu-id="634c3-126">Die Browsersicherheit verhindert, dass eine Webseite Anforderungen an eine andere Domäne als diejenige stellt, die die Webseite versorgt hat.</span><span class="sxs-lookup"><span data-stu-id="634c3-126">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="634c3-127">Diese Einschränkung gilt für das Durchführen von gRPC-Webaufrufen mit Browser-Apps.</span><span class="sxs-lookup"><span data-stu-id="634c3-127">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="634c3-128">So wird z. B. eine Browser-App, die von `https://www.contoso.com` versorgt wird, daran gehindert, gRPC-Web-Dienste aufzurufen, die auf `https://services.contoso.com` gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="634c3-128">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="634c3-129">Die Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross Origin Resource Sharing, CORS) kann verwendet werden, um diese Einschränkung zu lockern.</span><span class="sxs-lookup"><span data-stu-id="634c3-129">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="634c3-130">Damit Ihre Browser-App die Möglichkeit hat, gRPC-Web-Aufrufe ursprungsübergreifend zu tätigen, richten Sie [CORS in ASP.NET Core](xref:security/cors) ein.</span><span class="sxs-lookup"><span data-stu-id="634c3-130">To allow your browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="634c3-131">Verwenden Sie die integrierte CORS-Unterstützung, und machen Sie gRPC-spezifische Header mit <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> verfügbar.</span><span class="sxs-lookup"><span data-stu-id="634c3-131">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="634c3-132">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="634c3-132">The preceding code:</span></span>

* <span data-ttu-id="634c3-133">Ruft `AddCors` zum Hinzufügen von CORS-Diensten auf und konfiguriert eine CORS-Richtlinie, die gRPC-spezifische Header verfügbar macht.</span><span class="sxs-lookup"><span data-stu-id="634c3-133">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="634c3-134">Ruft `UseCors` zum Hinzufügen der CORS-Middleware nach dem Routing und vor den Endpunkten auf.</span><span class="sxs-lookup"><span data-stu-id="634c3-134">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="634c3-135">Gibt die Methode `endpoints.MapGrpcService<GreeterService>()` an, die CORS mit `RequiresCors` unterstützt.</span><span class="sxs-lookup"><span data-stu-id="634c3-135">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="634c3-136">Aufrufen von gRPC-Web über den Browser</span><span class="sxs-lookup"><span data-stu-id="634c3-136">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="634c3-137">Browser-Apps können gRPC-Web verwenden, um gRPC-Dienste aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="634c3-137">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="634c3-138">Es gibt einige Anforderungen und Einschränkungen beim Aufrufen von gRPC-Diensten mit gRPC-Web aus dem Browser heraus:</span><span class="sxs-lookup"><span data-stu-id="634c3-138">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="634c3-139">Der Server muss für die Unterstützung von gRPC-Web konfiguriert worden sein.</span><span class="sxs-lookup"><span data-stu-id="634c3-139">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="634c3-140">Clientstreaming und bidirektionale Streamingaufrufe werden nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="634c3-140">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="634c3-141">Das Serverstreaming wird unterstützt.</span><span class="sxs-lookup"><span data-stu-id="634c3-141">Server streaming is supported.</span></span>
* <span data-ttu-id="634c3-142">Das Aufrufen von gRPC-Diensten in einer anderen Domäne erfordert die Konfiguration von [CORS](xref:security/cors) auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="634c3-142">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="634c3-143">JavaScript gRPC-Web-Client</span><span class="sxs-lookup"><span data-stu-id="634c3-143">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="634c3-144">Es gibt einen JavaScript gRPC-Web-Client.</span><span class="sxs-lookup"><span data-stu-id="634c3-144">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="634c3-145">Anweisungen zur Verwendung von gRPC-Web aus JavaScript heraus finden Sie unter [Schreiben von JavaScript-Clientcode mit gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span><span class="sxs-lookup"><span data-stu-id="634c3-145">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="634c3-146">Konfigurieren von gRPC-Web mit dem .NET gRPC-Client</span><span class="sxs-lookup"><span data-stu-id="634c3-146">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="634c3-147">Der .NET gRPC-Client kann für gRPC-Web-Aufrufe konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="634c3-147">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="634c3-148">Dies ist nützlich für [Blazor WebAssembly](xref:blazor/index#blazor-webassembly)-Apps, die im Browser gehostet werden und dieselben HTTP-Einschränkungen des JavaScript-Codes aufweisen.</span><span class="sxs-lookup"><span data-stu-id="634c3-148">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="634c3-149">Das Aufrufen von gRPC-Web mit einem .NET-Client ist [dasselbe wie das Aufrufen von HTTP/2 gRPC](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="634c3-149">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="634c3-150">Die einzige Änderung besteht darin, wie der Kanal erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="634c3-150">The only modification is how the channel is created.</span></span>

<span data-ttu-id="634c3-151">So verwenden Sie gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="634c3-151">To use gRPC-Web:</span></span>

* <span data-ttu-id="634c3-152">Fügen Sie einen Verweis auf das Paket [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) hinzu.</span><span class="sxs-lookup"><span data-stu-id="634c3-152">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="634c3-153">Stellen Sie sicher, dass der Verweis auf das Paket [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) 2.27.0 oder höher ist.</span><span class="sxs-lookup"><span data-stu-id="634c3-153">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.27.0 or greater.</span></span>
* <span data-ttu-id="634c3-154">Konfigurieren Sie den Kanal für die Verwendung von `GrpcWebHandler`:</span><span class="sxs-lookup"><span data-stu-id="634c3-154">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="634c3-155">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="634c3-155">The preceding code:</span></span>

* <span data-ttu-id="634c3-156">Konfiguriert einen Kanal zur Verwendung von gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="634c3-156">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="634c3-157">Erstellt einen Client und tätigt einen Aufruf über den Kanal.</span><span class="sxs-lookup"><span data-stu-id="634c3-157">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="634c3-158">Der `GrpcWebHandler` hat bei der Erstellung die folgenden Konfigurationsoptionen:</span><span class="sxs-lookup"><span data-stu-id="634c3-158">The `GrpcWebHandler` has the following configuration options when created:</span></span>

* <span data-ttu-id="634c3-159">**InnerHandler**: Der zugrundeliegende <xref:System.Net.Http.HttpMessageHandler>, der die gRPC HTTP-Anforderung stellt, z. B. `HttpClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="634c3-159">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="634c3-160">**Modus**: Ein Enumerationstyp, der angibt, ob die gRPC-HTTP-Anforderung `Content-Type` `application/grpc-web` oder `application/grpc-web-text` ist.</span><span class="sxs-lookup"><span data-stu-id="634c3-160">**Mode**: An enumeration type that specifies whether the gRPC HTTP request request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="634c3-161">`GrpcWebMode.GrpcWeb` konfiguriert Inhalte, die ohne Codierung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="634c3-161">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="634c3-162">Standardwert.</span><span class="sxs-lookup"><span data-stu-id="634c3-162">Default value.</span></span>
    * <span data-ttu-id="634c3-163">`GrpcWebMode.GrpcWebText` konfiguriert den Inhalt so, dass er Base64-codiert ist.</span><span class="sxs-lookup"><span data-stu-id="634c3-163">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="634c3-164">Erforderlich für Serverstreamingaufrufe in Browsern.</span><span class="sxs-lookup"><span data-stu-id="634c3-164">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="634c3-165">**HttpVersion**: Die `Version` des HTTP-Protokolls, die verwendet wird, um [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) für die zugrunde liegende gRPC-HTTP-Anforderung festzulegen.</span><span class="sxs-lookup"><span data-stu-id="634c3-165">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="634c3-166">gRPC-Web erfordert keine bestimmte Version und setzt die Standardeinstellung nicht außer Kraft, sofern nicht anders angegeben.</span><span class="sxs-lookup"><span data-stu-id="634c3-166">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="634c3-167">Generierte gRPC-Clients verfügen über synchrone und asynchrone Methoden für den Aufruf unärer Methoden.</span><span class="sxs-lookup"><span data-stu-id="634c3-167">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="634c3-168">Beispiel: `SayHello` ist synchron und `SayHelloAsync` ist asynchron.</span><span class="sxs-lookup"><span data-stu-id="634c3-168">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="634c3-169">Der Aufruf einer synchronen Methode in einer Blazor WebAssembly-App führt dazu, dass die App nicht mehr reagiert.</span><span class="sxs-lookup"><span data-stu-id="634c3-169">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="634c3-170">Asynchrone Methoden müssen in Blazor WebAssembly immer verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="634c3-170">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="634c3-171">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="634c3-171">Additional resources</span></span>

* [<span data-ttu-id="634c3-172">gRPC für GitHub-Projekt für Webclients</span><span class="sxs-lookup"><span data-stu-id="634c3-172">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>

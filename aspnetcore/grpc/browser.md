---
title: Verwenden von gRPC in Browser-Apps
author: jamesnk
description: Erfahren Sie, wie Sie GrpC-Dienste auf ASP.net Core konfigurieren können, um von Browser-Apps mithilfe von GrpC-Web aufgerufen zu werden.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 02/10/2020
uid: grpc/browser
ms.openlocfilehash: 333fc8c4277bbac47042d4904c276e963186914a
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172275"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="2d8d6-103">Verwenden von gRPC in Browser-Apps</span><span class="sxs-lookup"><span data-stu-id="2d8d6-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="2d8d6-104">Von [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="2d8d6-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="2d8d6-105">**GrpC-Webunterstützung in .net ist experimentell**</span><span class="sxs-lookup"><span data-stu-id="2d8d6-105">**gRPC-Web support in .NET is experimental**</span></span>
>
> <span data-ttu-id="2d8d6-106">GrpC-Web für .net ist ein experimentelles Projekt, nicht ein Commit-Produkt.</span><span class="sxs-lookup"><span data-stu-id="2d8d6-106">gRPC-Web for .NET is an experimental project, not a committed product.</span></span> <span data-ttu-id="2d8d6-107">Wir möchten:</span><span class="sxs-lookup"><span data-stu-id="2d8d6-107">We want to:</span></span>
>
> * <span data-ttu-id="2d8d6-108">Testen Sie, ob unser Ansatz für die Implementierung von GrpC-Web funktioniert.</span><span class="sxs-lookup"><span data-stu-id="2d8d6-108">Test that our approach to implementing gRPC-Web works.</span></span>
> * <span data-ttu-id="2d8d6-109">Informieren Sie sich darüber, ob diese Vorgehensweise für .NET-Entwickler nützlich ist, wenn Sie mit dem herkömmlichen Verfahren zum Einrichten von GrpC-Web über einen Proxy arbeiten.</span><span class="sxs-lookup"><span data-stu-id="2d8d6-109">Get feedback on if this approach is useful to .NET developers compared to the traditional way of setting up gRPC-Web via a proxy.</span></span>
>
> <span data-ttu-id="2d8d6-110">Bitte lassen Sie uns Feedback zu [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) , um sicherzustellen, dass wir eine solche erstellen, die Entwickler wie und produktiv sind.</span><span class="sxs-lookup"><span data-stu-id="2d8d6-110">Please leave feedback at [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="2d8d6-111">Es ist nicht möglich, einen http/2-GrpC-Dienst aus einer browserbasierten App aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="2d8d6-111">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="2d8d6-112">[GrpC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) ist ein Protokoll, mit dem Browser-JavaScript-und blazor-apps GrpC-Dienste anrufen können.</span><span class="sxs-lookup"><span data-stu-id="2d8d6-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="2d8d6-113">In diesem Artikel wird erläutert, wie Sie GrpC-Web in .net Core verwenden.</span><span class="sxs-lookup"><span data-stu-id="2d8d6-113">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="2d8d6-114">Konfigurieren von GrpC-Web in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="2d8d6-114">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="2d8d6-115">in ASP.net Core gehostete GrpC-Dienste können so konfiguriert werden, dass GrpC-Web neben http/2-GrpC unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="2d8d6-115">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="2d8d6-116">für GrpC-Web sind keine Änderungen an Diensten erforderlich.</span><span class="sxs-lookup"><span data-stu-id="2d8d6-116">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="2d8d6-117">Die einzige Änderung ist die Startkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="2d8d6-117">The only modification is startup configuration.</span></span>

<span data-ttu-id="2d8d6-118">So aktivieren Sie GrpC-Web mit einem ASP.net Core GrpC-Dienst:</span><span class="sxs-lookup"><span data-stu-id="2d8d6-118">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="2d8d6-119">Fügen Sie einen Verweis auf das Paket " [GrpC. aspnetcore. Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) " hinzu.</span><span class="sxs-lookup"><span data-stu-id="2d8d6-119">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="2d8d6-120">Konfigurieren Sie die APP für die Verwendung von GrpC-Web, indem Sie `AddGrpcWeb` und `UseGrpcWeb` zu *Startup.cs*hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="2d8d6-120">Configure the app to use gRPC-Web by adding `AddGrpcWeb` and `UseGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="2d8d6-121">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="2d8d6-121">The preceding code:</span></span>

* <span data-ttu-id="2d8d6-122">Fügt die GrpC-Web-Middleware, die `UseGrpcWeb`nach dem Routing und vor Endpunkten hinzu.</span><span class="sxs-lookup"><span data-stu-id="2d8d6-122">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="2d8d6-123">Gibt an, dass die `endpoints.MapGrpcService<GreeterService>()`-Methode GrpC-Web mit `EnableGrpcWeb`unterstützt.</span><span class="sxs-lookup"><span data-stu-id="2d8d6-123">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="2d8d6-124">Alternativ dazu können Sie alle Dienste so konfigurieren, dass GrpC-Web unterstützt wird, indem Sie `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` zu Configuration Services hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="2d8d6-124">Alternatively, configure all services to support gRPC-Web by adding `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` to ConfigureServices.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=6,13)]

<span data-ttu-id="2d8d6-125">Möglicherweise sind einige zusätzliche Konfigurationen erforderlich, um GrpC-Web über den Browser aufzurufen, z. b. das Konfigurieren von ASP.net Core zur Unterstützung von cors.</span><span class="sxs-lookup"><span data-stu-id="2d8d6-125">Some additional configuration may be required to call gRPC-Web from the browser, such as configuring ASP.NET Core to support CORS.</span></span> <span data-ttu-id="2d8d6-126">Weitere Informationen finden Sie [unter Unterstützung von cors](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="2d8d6-126">For more information, see [support CORS](xref:security/cors).</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="2d8d6-127">GrpC-Web aus dem Browser abrufen</span><span class="sxs-lookup"><span data-stu-id="2d8d6-127">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="2d8d6-128">Browser-Apps können GrpC-Web verwenden, um GrpC-Dienste aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="2d8d6-128">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="2d8d6-129">Beim Aufrufen von GrpC-Diensten mit GrpC-Web über den Browser gelten einige Anforderungen und Einschränkungen:</span><span class="sxs-lookup"><span data-stu-id="2d8d6-129">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="2d8d6-130">Der Server muss für die Unterstützung von GrpC-Web konfiguriert worden sein.</span><span class="sxs-lookup"><span data-stu-id="2d8d6-130">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="2d8d6-131">Client Streaming und bidirektionale Streaming-Aufrufe werden nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="2d8d6-131">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="2d8d6-132">Server Streaming wird unterstützt.</span><span class="sxs-lookup"><span data-stu-id="2d8d6-132">Server streaming is supported.</span></span>
* <span data-ttu-id="2d8d6-133">Zum Aufrufen von GrpC-Diensten in einer anderen Domäne müssen [cors](xref:security/cors) auf dem Server konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="2d8d6-133">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="2d8d6-134">JavaScript-GrpC-WebClient</span><span class="sxs-lookup"><span data-stu-id="2d8d6-134">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="2d8d6-135">Es ist ein JavaScript-GrpC-WebClient vorhanden.</span><span class="sxs-lookup"><span data-stu-id="2d8d6-135">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="2d8d6-136">Anweisungen zur Verwendung von GrpC-Web aus JavaScript finden Sie unter [Schreiben von JavaScript-Client Code mit GrpC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span><span class="sxs-lookup"><span data-stu-id="2d8d6-136">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="2d8d6-137">Konfigurieren von GrpC-Web mit dem .net-GrpC-Client</span><span class="sxs-lookup"><span data-stu-id="2d8d6-137">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="2d8d6-138">Der .net-GrpC-Client kann so konfiguriert werden, dass er GrpC-Web-Aufrufe aufruft.</span><span class="sxs-lookup"><span data-stu-id="2d8d6-138">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="2d8d6-139">Dies ist nützlich für [blazor Webassembly](xref:blazor/index#blazor-webassembly) -apps, die im Browser gehostet werden und dieselben http-Einschränkungen von JavaScript-Code aufweisen.</span><span class="sxs-lookup"><span data-stu-id="2d8d6-139">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="2d8d6-140">Das Aufrufen von GrpC-Web mit einem .NET-Client ist mit [dem http/2-GrpC identisch](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="2d8d6-140">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="2d8d6-141">Die einzige Änderung ist die Art und Weise, wie der Kanal erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="2d8d6-141">The only modification is how the channel is created.</span></span>

<span data-ttu-id="2d8d6-142">So verwenden Sie GrpC-Web:</span><span class="sxs-lookup"><span data-stu-id="2d8d6-142">To use gRPC-Web:</span></span>

* <span data-ttu-id="2d8d6-143">Fügen Sie einen Verweis auf das [GrpC .net. Client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) -Paket hinzu.</span><span class="sxs-lookup"><span data-stu-id="2d8d6-143">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="2d8d6-144">Stellen Sie sicher, dass der Verweis auf das [GrpC .net. Client](https://www.nuget.org/packages/Grpc.Net.Client) -Paket 2.27.0 oder höher ist.</span><span class="sxs-lookup"><span data-stu-id="2d8d6-144">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.27.0 or greater.</span></span>
* <span data-ttu-id="2d8d6-145">Konfigurieren Sie den Kanal für die Verwendung der `GrpcWebHandler`:</span><span class="sxs-lookup"><span data-stu-id="2d8d6-145">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="2d8d6-146">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="2d8d6-146">The preceding code:</span></span>

* <span data-ttu-id="2d8d6-147">Konfiguriert einen Kanal für die Verwendung von GrpC-Web.</span><span class="sxs-lookup"><span data-stu-id="2d8d6-147">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="2d8d6-148">Erstellt einen Client und führt einen-Befehl mithilfe des Kanals aus.</span><span class="sxs-lookup"><span data-stu-id="2d8d6-148">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="2d8d6-149">Die `GrpcWebHandler` verfügt bei der Erstellung über die folgenden Konfigurationsoptionen:</span><span class="sxs-lookup"><span data-stu-id="2d8d6-149">The `GrpcWebHandler` has the following configuration options when created:</span></span>

* <span data-ttu-id="2d8d6-150">**Innerhandler**: die zugrunde liegende <xref:System.Net.Http.HttpMessageHandler>, die die GrpC-http-Anforderung ausführt, z. b. `HttpClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="2d8d6-150">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="2d8d6-151">**Mode**: ein Enumerationstyp, der angibt, ob die GrpC-HTTP-Anforderungs Anforderung `Content-Type` `application/grpc-web` oder `application/grpc-web-text`ist.</span><span class="sxs-lookup"><span data-stu-id="2d8d6-151">**Mode**: An enumeration type that specifies whether the gRPC HTTP request request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="2d8d6-152">`GrpcWebMode.GrpcWeb` konfiguriert Inhalte so, dass Sie ohne Codierung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="2d8d6-152">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="2d8d6-153">Standardwert.</span><span class="sxs-lookup"><span data-stu-id="2d8d6-153">Default value.</span></span>
    * <span data-ttu-id="2d8d6-154">`GrpcWebMode.GrpcWebText` konfiguriert Inhalte so, dass Sie Base64-codiert sind.</span><span class="sxs-lookup"><span data-stu-id="2d8d6-154">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="2d8d6-155">Erforderlich für serverstreamingaufrufe in Browsern.</span><span class="sxs-lookup"><span data-stu-id="2d8d6-155">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="2d8d6-156">**HttpVersion**: http-Protokoll `Version` das zum Festlegen von [httprequestmessage. Version](xref:System.Net.Http.HttpRequestMessage.Version) für die zugrunde liegende GrpC-http-Anforderung verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="2d8d6-156">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="2d8d6-157">GrpC-Web erfordert keine bestimmte Version und überschreibt den Standardwert nur, wenn dies angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="2d8d6-157">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="2d8d6-158">Generierte GrpC-Clients verfügen über Synchronisierungs-und Async-Methoden zum Aufrufen unärer Methoden.</span><span class="sxs-lookup"><span data-stu-id="2d8d6-158">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="2d8d6-159">`SayHello` ist beispielsweise Sync, und `SayHelloAsync` ist Async.</span><span class="sxs-lookup"><span data-stu-id="2d8d6-159">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="2d8d6-160">Das Aufrufen einer Synchronisierungsmethode in einer blazor Webassembly-App führt dazu, dass die APP nicht mehr reagiert.</span><span class="sxs-lookup"><span data-stu-id="2d8d6-160">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="2d8d6-161">Async-Methoden müssen stets in der blazor-Webassembly verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="2d8d6-161">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2d8d6-162">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="2d8d6-162">Additional resources</span></span>

* [<span data-ttu-id="2d8d6-163">GrpC für Webclients GitHub-Projekt</span><span class="sxs-lookup"><span data-stu-id="2d8d6-163">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>

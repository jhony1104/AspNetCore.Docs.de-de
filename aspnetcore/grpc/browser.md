---
title: GrpC in Browser-apps
author: jamesnk
description: Erfahren Sie, wie Sie GrpC-Dienste auf ASP.net Core konfigurieren können, um von Browser-Apps mithilfe von GrpC-Web aufgerufen zu werden.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/24/2020
uid: grpc/browser
ms.openlocfilehash: 6359c3b76b3cb1ba2b6d9f9a989f64cbf4c4379d
ms.sourcegitcommit: b5ceb0a46d0254cc3425578116e2290142eec0f0
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2020
ms.locfileid: "76830633"
---
# <a name="grpc-in-browser-apps"></a><span data-ttu-id="9be55-103">GrpC in Browser-apps</span><span class="sxs-lookup"><span data-stu-id="9be55-103">gRPC in browser apps</span></span>

<span data-ttu-id="9be55-104">Von [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="9be55-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="9be55-105">**GrpC-Webunterstützung in .net ist experimentell**</span><span class="sxs-lookup"><span data-stu-id="9be55-105">**gRPC-Web support in .NET is experimental**</span></span>
>
> <span data-ttu-id="9be55-106">GrpC-Web für .net ist ein experimentelles Projekt, nicht ein Commit-Produkt.</span><span class="sxs-lookup"><span data-stu-id="9be55-106">gRPC-Web for .NET is an experimental project, not a committed product.</span></span> <span data-ttu-id="9be55-107">Wir möchten:</span><span class="sxs-lookup"><span data-stu-id="9be55-107">We want to:</span></span>
>
> * <span data-ttu-id="9be55-108">Testen Sie, ob unser Ansatz für die Implementierung von GrpC-Web funktioniert.</span><span class="sxs-lookup"><span data-stu-id="9be55-108">Test that our approach to implementing gRPC-Web works.</span></span>
> * <span data-ttu-id="9be55-109">Informieren Sie sich darüber, ob diese Vorgehensweise für .NET-Entwickler nützlich ist, wenn Sie mit dem herkömmlichen Verfahren zum Einrichten von GrpC-Web über einen Proxy arbeiten.</span><span class="sxs-lookup"><span data-stu-id="9be55-109">Get feedback on if this approach is useful to .NET developers compared to the traditional way of setting up gRPC-Web via a proxy.</span></span>
>
> <span data-ttu-id="9be55-110">Bitte lassen Sie uns Feedback zu [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) , um sicherzustellen, dass wir eine solche erstellen, die Entwickler wie und produktiv sind.</span><span class="sxs-lookup"><span data-stu-id="9be55-110">Please leave feedback at [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="9be55-111">Es ist nicht möglich, einen http/2-GrpC-Dienst aus einer browserbasierten App aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="9be55-111">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="9be55-112">[GrpC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) ist ein Protokoll, mit dem Browser-JavaScript-und blazor-apps GrpC-Dienste anrufen können.</span><span class="sxs-lookup"><span data-stu-id="9be55-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="9be55-113">In diesem Artikel wird erläutert, wie Sie GrpC-Web in .net Core verwenden.</span><span class="sxs-lookup"><span data-stu-id="9be55-113">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="9be55-114">Konfigurieren von GrpC-Web in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="9be55-114">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="9be55-115">in ASP.net Core gehostete GrpC-Dienste können so konfiguriert werden, dass GrpC-Web neben http/2-GrpC unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="9be55-115">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="9be55-116">für GrpC-Web sind keine Änderungen an Diensten erforderlich.</span><span class="sxs-lookup"><span data-stu-id="9be55-116">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="9be55-117">Die einzige Änderung ist die Startkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="9be55-117">The only modification is startup configuration.</span></span>

<span data-ttu-id="9be55-118">So aktivieren Sie GrpC-Web mit einem ASP.net Core GrpC-Dienst:</span><span class="sxs-lookup"><span data-stu-id="9be55-118">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="9be55-119">Fügen Sie einen Verweis auf das Paket " [GrpC. aspnetcore. Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) " hinzu.</span><span class="sxs-lookup"><span data-stu-id="9be55-119">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="9be55-120">Konfigurieren Sie die APP für die Verwendung von GrpC-Web, indem Sie `AddGrpcWeb` und `UseGrpcWeb` zu *Startup.cs*hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="9be55-120">Configure the app to use gRPC-Web by adding `AddGrpcWeb` and `UseGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=3,10,14)]

<span data-ttu-id="9be55-121">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="9be55-121">The preceding code:</span></span>

* <span data-ttu-id="9be55-122">Fügt die GrpC-Web-Middleware, die `UseGrpcWeb`nach dem Routing und vor Endpunkten hinzu.</span><span class="sxs-lookup"><span data-stu-id="9be55-122">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="9be55-123">Gibt an, dass die `endpoints.MapGrpcService<GreeterService>()`-Methode GrpC-Web mit `EnableGrpcWeb`unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9be55-123">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="9be55-124">Alternativ dazu können Sie alle Dienste so konfigurieren, dass GrpC-Web unterstützt wird, indem Sie `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` zu Configuration Services hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="9be55-124">Alternatively, configure all services to support gRPC-Web by adding `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` to ConfigureServices.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=5,12,16)]

<span data-ttu-id="9be55-125">Möglicherweise sind einige zusätzliche Konfigurationen erforderlich, um GrpC-Web über den Browser aufzurufen, z. b. das Konfigurieren von ASP.net Core zur Unterstützung von cors.</span><span class="sxs-lookup"><span data-stu-id="9be55-125">Some additional configuration may be required to call gRPC-Web from the browser, such as configuring ASP.NET Core to support CORS.</span></span> <span data-ttu-id="9be55-126">Weitere Informationen finden Sie [unter Unterstützung von cors](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="9be55-126">For more information, see [support CORS](xref:security/cors).</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="9be55-127">GrpC-Web aus dem Browser abrufen</span><span class="sxs-lookup"><span data-stu-id="9be55-127">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="9be55-128">Browser-Apps können GrpC-Web verwenden, um GrpC-Dienste aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="9be55-128">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="9be55-129">Beim Aufrufen von GrpC-Diensten mit GrpC-Web über den Browser gelten einige Anforderungen und Einschränkungen:</span><span class="sxs-lookup"><span data-stu-id="9be55-129">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="9be55-130">Der Server muss für die Unterstützung von GrpC-Web konfiguriert worden sein.</span><span class="sxs-lookup"><span data-stu-id="9be55-130">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="9be55-131">Client Streaming und bidirektionale Streaming-Aufrufe werden nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9be55-131">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="9be55-132">Server Streaming wird unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9be55-132">Server streaming is supported.</span></span>
* <span data-ttu-id="9be55-133">Zum Aufrufen von GrpC-Diensten in einer anderen Domäne müssen [cors](xref:security/cors) auf dem Server konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="9be55-133">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="9be55-134">JavaScript-GrpC-WebClient</span><span class="sxs-lookup"><span data-stu-id="9be55-134">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="9be55-135">Es ist ein JavaScript-GrpC-WebClient vorhanden.</span><span class="sxs-lookup"><span data-stu-id="9be55-135">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="9be55-136">Anweisungen zur Verwendung von GrpC-Web aus JavaScript finden Sie unter [Schreiben von JavaScript-Client Code mit GrpC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span><span class="sxs-lookup"><span data-stu-id="9be55-136">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="9be55-137">Konfigurieren von GrpC-Web mit dem .net-GrpC-Client</span><span class="sxs-lookup"><span data-stu-id="9be55-137">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="9be55-138">Der .net-GrpC-Client kann so konfiguriert werden, dass er GrpC-Web-Aufrufe aufruft.</span><span class="sxs-lookup"><span data-stu-id="9be55-138">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="9be55-139">Dies ist nützlich für [blazor Webassembly](xref:blazor/index#blazor-webassembly) -apps, die im Browser gehostet werden und dieselben http-Einschränkungen von JavaScript-Code aufweisen.</span><span class="sxs-lookup"><span data-stu-id="9be55-139">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="9be55-140">Das Aufrufen von GrpC-Web mit einem .NET-Client ist mit [dem http/2-GrpC identisch](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="9be55-140">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="9be55-141">Die einzige Änderung ist die Art und Weise, wie der Kanal erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="9be55-141">The only modification is how the channel is created.</span></span>

<span data-ttu-id="9be55-142">So verwenden Sie GrpC-Web:</span><span class="sxs-lookup"><span data-stu-id="9be55-142">To use gRPC-Web:</span></span>

* <span data-ttu-id="9be55-143">Fügen Sie einen Verweis auf das [GrpC .net. Client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) -Paket hinzu.</span><span class="sxs-lookup"><span data-stu-id="9be55-143">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="9be55-144">Konfigurieren Sie den Kanal für die Verwendung der `GrpcWebHandler`:</span><span class="sxs-lookup"><span data-stu-id="9be55-144">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="9be55-145">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="9be55-145">The preceding code:</span></span>

* <span data-ttu-id="9be55-146">Konfiguriert einen Kanal für die Verwendung von GrpC-Web.</span><span class="sxs-lookup"><span data-stu-id="9be55-146">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="9be55-147">Erstellt einen Client und führt einen-Befehl mithilfe des Kanals aus.</span><span class="sxs-lookup"><span data-stu-id="9be55-147">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="9be55-148">Die `GrpcWebHandler` verfügt bei der Erstellung über die folgenden Konfigurationsoptionen:</span><span class="sxs-lookup"><span data-stu-id="9be55-148">The `GrpcWebHandler` has the following configuration options when created:</span></span>

* <span data-ttu-id="9be55-149">**Innerhandler**: die zugrunde liegende <xref:System.Net.Http.HttpMessageHandler>, die den HTTP-Befehl ausführt, z. b. `HttpClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="9be55-149">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the HTTP call, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="9be55-150">**Mode**: `GrpcWebMode` Enum.</span><span class="sxs-lookup"><span data-stu-id="9be55-150">**Mode**: `GrpcWebMode` enum.</span></span> <span data-ttu-id="9be55-151">`GrpcWebMode.GrpcWebText` konfiguriert Inhalte für die Base64-Codierung, die zur Unterstützung von serverstreamingaufrufen erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="9be55-151">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded, which is required to support server streaming calls.</span></span>
* <span data-ttu-id="9be55-152">**HttpVersion**: http-Protokoll `Version`.</span><span class="sxs-lookup"><span data-stu-id="9be55-152">**HttpVersion**: HTTP protocol `Version`.</span></span> <span data-ttu-id="9be55-153">für "GrpC-Web" ist kein bestimmtes Protokoll erforderlich, und es wird kein bestimmtes Protokoll angegeben, wenn eine Anforderung nicht konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="9be55-153">gRPC-Web doesn't require a specific protocol and won't specify one when making a request unless configured.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9be55-154">Weitere Ressourcen</span><span class="sxs-lookup"><span data-stu-id="9be55-154">Additional resources</span></span>

* [<span data-ttu-id="9be55-155">GrpC für Webclients GitHub-Projekt</span><span class="sxs-lookup"><span data-stu-id="9be55-155">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>

---
title: Aktivieren von Cross-Origin-Anforderungen (cors) in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie cors als Standard zum Zulassen oder ablehnen von Ursprungs übergreifenden Anforderungen in einer ASP.net Core-app.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/cors
ms.openlocfilehash: 76e79c2d9762e242dc44857370b0ce1d13f1d1cb
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85403780"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="71f70-103">Aktivieren von Cross-Origin-Anforderungen (cors) in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="71f70-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="71f70-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="71f70-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="71f70-105">In diesem Artikel wird gezeigt, wie Sie cors in einer ASP.net Core-App aktivieren.</span><span class="sxs-lookup"><span data-stu-id="71f70-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="71f70-106">Die Browsersicherheit verhindert, dass eine Webseite Anforderungen an eine andere Domäne als diejenige stellt, die die Webseite versorgt hat.</span><span class="sxs-lookup"><span data-stu-id="71f70-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="71f70-107">Diese Einschränkung wird als *Richtlinie des gleichen Ursprungs* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="71f70-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="71f70-108">Die Richtlinie des gleichen Ursprungs verhindert, dass eine schädliche Website sensible Daten von einer anderen Website liest.</span><span class="sxs-lookup"><span data-stu-id="71f70-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="71f70-109">Manchmal möchten Sie, dass andere Sites Ursprungs übergreifende Anforderungen an Ihre APP senden können.</span><span class="sxs-lookup"><span data-stu-id="71f70-109">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="71f70-110">Weitere Informationen finden Sie im [Artikel zu Mozilla cors](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="71f70-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="71f70-111">[Cross-Origin Resource Sharing](https://www.w3.org/TR/cors/) (cors):</span><span class="sxs-lookup"><span data-stu-id="71f70-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="71f70-112">Ist ein W3C-Standard, der es einem Server ermöglicht, die Richtlinie für denselben Ursprung zu lockern.</span><span class="sxs-lookup"><span data-stu-id="71f70-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="71f70-113">Bei handelt es sich **nicht** um ein Sicherheits Feature, sondern um die Sicherheit von cors.</span><span class="sxs-lookup"><span data-stu-id="71f70-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="71f70-114">Eine API wird durch das Zulassen von cors nicht sicherer.</span><span class="sxs-lookup"><span data-stu-id="71f70-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="71f70-115">Weitere Informationen finden Sie unter [Funktionsweise von cors](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="71f70-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="71f70-116">Ermöglicht einem Server das explizite zulassen einiger Ursprungs übergreifender Anforderungen, während andere abgelehnt werden.</span><span class="sxs-lookup"><span data-stu-id="71f70-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="71f70-117">Ist sicherer und flexibler als frühere Techniken, z. b. [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="71f70-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="71f70-118">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="71f70-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="71f70-119">Gleicher Ursprung</span><span class="sxs-lookup"><span data-stu-id="71f70-119">Same origin</span></span>

<span data-ttu-id="71f70-120">Zwei URLs haben denselben Ursprung, wenn Sie identische Schemas, Hosts und Ports aufweisen ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="71f70-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="71f70-121">Diese beiden URLs haben denselben Ursprung:</span><span class="sxs-lookup"><span data-stu-id="71f70-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="71f70-122">Diese URLs haben andere Ursprünge als die beiden vorherigen URLs:</span><span class="sxs-lookup"><span data-stu-id="71f70-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="71f70-123">`https://example.net`: Andere Domäne</span><span class="sxs-lookup"><span data-stu-id="71f70-123">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="71f70-124">`https://www.example.com/foo.html`: Untergeordnete Domäne</span><span class="sxs-lookup"><span data-stu-id="71f70-124">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="71f70-125">`http://example.com/foo.html`: Anderes Schema</span><span class="sxs-lookup"><span data-stu-id="71f70-125">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="71f70-126">`https://example.com:9000/foo.html`: Anderer Port</span><span class="sxs-lookup"><span data-stu-id="71f70-126">`https://example.com:9000/foo.html`: Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="71f70-127">Aktivieren von CORS</span><span class="sxs-lookup"><span data-stu-id="71f70-127">Enable CORS</span></span>

<span data-ttu-id="71f70-128">Es gibt drei Möglichkeiten, cors zu aktivieren:</span><span class="sxs-lookup"><span data-stu-id="71f70-128">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="71f70-129">In Middleware, die eine [benannte Richtlinie](#np) oder eine [Standard Richtlinie](#dp)verwendet.</span><span class="sxs-lookup"><span data-stu-id="71f70-129">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="71f70-130">Verwenden des [Endpunkt Routings](#ecors).</span><span class="sxs-lookup"><span data-stu-id="71f70-130">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="71f70-131">Mit dem [[enablecors]](#attr) -Attribut.</span><span class="sxs-lookup"><span data-stu-id="71f70-131">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="71f70-132">Die Verwendung des [[enablecors]](#attr) -Attributs mit einer benannten Richtlinie ermöglicht das beste Steuerelement in einschränkenden Endpunkten, die cors unterstützen.</span><span class="sxs-lookup"><span data-stu-id="71f70-132">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

> [!WARNING]
> <span data-ttu-id="71f70-133"><xref:Owin.CorsExtensions.UseCors%2A>muss vor aufgerufen werden, <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> Wenn verwendet wird `UseResponseCaching` .</span><span class="sxs-lookup"><span data-stu-id="71f70-133"><xref:Owin.CorsExtensions.UseCors%2A> must be called before <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> when using `UseResponseCaching`.</span></span>

<span data-ttu-id="71f70-134">Jeder Ansatz wird in den folgenden Abschnitten ausführlich beschrieben.</span><span class="sxs-lookup"><span data-stu-id="71f70-134">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="71f70-135">Cors mit benannten Richtlinie und Middleware</span><span class="sxs-lookup"><span data-stu-id="71f70-135">CORS with named policy and middleware</span></span>

<span data-ttu-id="71f70-136">Cors-Middleware verarbeitet Ursprungs übergreifende Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="71f70-136">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="71f70-137">Mit dem folgenden Code wird eine cors-Richtlinie auf alle Endpunkte der APP mit den angegebenen Ursprüngen angewendet:</span><span class="sxs-lookup"><span data-stu-id="71f70-137">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,32)]

<span data-ttu-id="71f70-138">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="71f70-138">The preceding code:</span></span>

* <span data-ttu-id="71f70-139">Legt den Richtlinien Namen auf fest `_myAllowSpecificOrigins` .</span><span class="sxs-lookup"><span data-stu-id="71f70-139">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="71f70-140">Der Richtlinien Name ist willkürlich.</span><span class="sxs-lookup"><span data-stu-id="71f70-140">The policy name is arbitrary.</span></span>
* <span data-ttu-id="71f70-141">Ruft die <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> Erweiterungsmethode auf und gibt die `_myAllowSpecificOrigins` cors-Richtlinie an.</span><span class="sxs-lookup"><span data-stu-id="71f70-141">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="71f70-142">`UseCors`Fügt die cors-Middleware hinzu.</span><span class="sxs-lookup"><span data-stu-id="71f70-142">`UseCors` adds the CORS middleware.</span></span> <span data-ttu-id="71f70-143">Der-Anruf `UseCors` muss nach, jedoch `UseRouting` vor eingefügt werden `UseAuthorization` .</span><span class="sxs-lookup"><span data-stu-id="71f70-143">The call to `UseCors` must be placed after `UseRouting`, but before `UseAuthorization`.</span></span> <span data-ttu-id="71f70-144">Weitere Informationen finden Sie unter [Middleware Order](xref:fundamentals/middleware/index#middleware-order).</span><span class="sxs-lookup"><span data-stu-id="71f70-144">For more information, see [Middleware order](xref:fundamentals/middleware/index#middleware-order).</span></span>
* <span data-ttu-id="71f70-145">Ruft <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> mit einem [Lambda-Ausdruck](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)auf.</span><span class="sxs-lookup"><span data-stu-id="71f70-145">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="71f70-146">Der Lambda-Ausdruck nimmt ein- <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> Objekt an.</span><span class="sxs-lookup"><span data-stu-id="71f70-146">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="71f70-147">[Konfigurationsoptionen](#cors-policy-options), wie z `WithOrigins` . b., werden weiter unten in diesem Artikel beschrieben.</span><span class="sxs-lookup"><span data-stu-id="71f70-147">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="71f70-148">Aktiviert die `_myAllowSpecificOrigins` cors-Richtlinie für alle Controller Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="71f70-148">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="71f70-149">Weitere Informationen finden Sie unter [EndPoint Routing](#ecors) zum Anwenden einer cors-Richtlinie auf bestimmte Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="71f70-149">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>
* <span data-ttu-id="71f70-150">Wenn Sie die [Middleware zum Zwischenspeichern von Antworten](xref:performance/caching/middleware)verwenden, sollten Sie <xref:Owin.CorsExtensions.UseCors%2A> vor <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A></span><span class="sxs-lookup"><span data-stu-id="71f70-150">When using [Response Caching Middleware](xref:performance/caching/middleware), call <xref:Owin.CorsExtensions.UseCors%2A> before <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A>.</span></span>

<span data-ttu-id="71f70-151">Beim Endpunkt Routing **muss** die cors-Middleware für die Ausführung zwischen den Aufrufen von und konfiguriert werden `UseRouting` `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="71f70-151">With endpoint routing, the CORS middleware **must** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="71f70-152">Anweisungen zum Testen von Code, der dem vorangehenden Code ähnelt, finden Sie unter [Test-cors](#testc) .</span><span class="sxs-lookup"><span data-stu-id="71f70-152">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="71f70-153">Der <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> -Methodenaufrufe fügt cors-Dienste zum Dienst Container der APP hinzu:</span><span class="sxs-lookup"><span data-stu-id="71f70-153">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="71f70-154">Weitere Informationen finden Sie unter [cors-Richtlinien Optionen](#cpo) in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="71f70-154">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="71f70-155">Die <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> Methoden können verkettet werden, wie im folgenden Code gezeigt:</span><span class="sxs-lookup"><span data-stu-id="71f70-155">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="71f70-156">Hinweis: die angegebene URL darf **keinen** nachgestellten Schrägstrich ( `/` ) enthalten.</span><span class="sxs-lookup"><span data-stu-id="71f70-156">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="71f70-157">Wenn die URL mit beendet `/` wird, gibt der Vergleich zurück, `false` und es wird kein-Header zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="71f70-157">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="71f70-158">Cors mit Standard Richtlinie und Middleware</span><span class="sxs-lookup"><span data-stu-id="71f70-158">CORS with default policy and middleware</span></span>

<span data-ttu-id="71f70-159">Der folgende markierte Code aktiviert die cors-Standard Richtlinie:</span><span class="sxs-lookup"><span data-stu-id="71f70-159">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="71f70-160">Im vorangehenden Code wird die cors-Standard Richtlinie auf alle Controller Endpunkte angewendet.</span><span class="sxs-lookup"><span data-stu-id="71f70-160">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="71f70-161">Aktivieren von Cors mit Endpunktrouting</span><span class="sxs-lookup"><span data-stu-id="71f70-161">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="71f70-162">Die Aktivierung von cors auf Basis von Endpunkten `RequireCors` unterstützt **not** derzeit keine [automatischen Preflight-Anforderungen](#apf).</span><span class="sxs-lookup"><span data-stu-id="71f70-162">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="71f70-163">Weitere Informationen finden Sie in [diesem GitHub-Problem](https://github.com/dotnet/aspnetcore/issues/20709) und [Testen von cors mit dem Endpunkt Routing und [httpoptions]](#tcer).</span><span class="sxs-lookup"><span data-stu-id="71f70-163">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="71f70-164">Mit dem Endpunkt Routing kann cors pro Endpunkt mit dem <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> Satz von Erweiterungs Methoden aktiviert werden:</span><span class="sxs-lookup"><span data-stu-id="71f70-164">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="71f70-165">Für den Code oben gilt:</span><span class="sxs-lookup"><span data-stu-id="71f70-165">In the preceding code:</span></span>

* <span data-ttu-id="71f70-166">`app.UseCors`aktiviert die cors-Middleware.</span><span class="sxs-lookup"><span data-stu-id="71f70-166">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="71f70-167">Da eine Standard Richtlinie nicht konfiguriert wurde, werden `app.UseCors()` cors von alleine nicht aktiviert.</span><span class="sxs-lookup"><span data-stu-id="71f70-167">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="71f70-168">Die `/echo` -und-Controller Endpunkte lassen Ursprungs übergreifende Anforderungen zu, die die angegebene Richtlinie verwenden.</span><span class="sxs-lookup"><span data-stu-id="71f70-168">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="71f70-169">Die `/echo2` -und Razor -Seiten Endpunkte lassen **keine** Ursprungs übergreifenden Anforderungen zu, da keine Standard Richtlinie angegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="71f70-169">The `/echo2` and Razor Pages endpoints do **not** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="71f70-170">Das [[disablecors]](#dc) -Attribut deaktiviert **keine** cors, die durch das Endpunkt Routing mit aktiviert wurden `RequireCors` .</span><span class="sxs-lookup"><span data-stu-id="71f70-170">The [[DisableCors]](#dc) attribute does **not**  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="71f70-171">Anweisungen zum Testen von Code, der dem vorangehenden ähnelt, finden Sie unter [Testen von cors mit dem Endpunkt Routing und [httpoptions]](#tcer) .</span><span class="sxs-lookup"><span data-stu-id="71f70-171">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="71f70-172">Aktivieren von cors mit Attributen</span><span class="sxs-lookup"><span data-stu-id="71f70-172">Enable CORS with attributes</span></span>

<span data-ttu-id="71f70-173">Das Aktivieren von cors mit dem [[enablecors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) -Attribut und das Anwenden einer benannten Richtlinie auf die Endpunkte, die cors erfordern, stellt das beste Steuerelement bereit.</span><span class="sxs-lookup"><span data-stu-id="71f70-173">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="71f70-174">Das [[enablecors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) -Attribut bietet eine Alternative zum globalen Anwenden von cors.</span><span class="sxs-lookup"><span data-stu-id="71f70-174">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="71f70-175">Das- `[EnableCors]` Attribut aktiviert cors für ausgewählte Endpunkte anstelle aller Endpunkte:</span><span class="sxs-lookup"><span data-stu-id="71f70-175">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="71f70-176">`[EnableCors]`Gibt die Standard Richtlinie an.</span><span class="sxs-lookup"><span data-stu-id="71f70-176">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="71f70-177">`[EnableCors("{Policy String}")]`gibt eine benannte Richtlinie an.</span><span class="sxs-lookup"><span data-stu-id="71f70-177">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="71f70-178">Das- `[EnableCors]` Attribut kann auf Folgendes angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="71f70-178">The `[EnableCors]` attribute can be applied to:</span></span>

* Razor<span data-ttu-id="71f70-179">S`PageModel`</span><span class="sxs-lookup"><span data-stu-id="71f70-179"> Page `PageModel`</span></span>
* <span data-ttu-id="71f70-180">Controller</span><span class="sxs-lookup"><span data-stu-id="71f70-180">Controller</span></span>
* <span data-ttu-id="71f70-181">Controller-Aktionsmethode</span><span class="sxs-lookup"><span data-stu-id="71f70-181">Controller action method</span></span>

<span data-ttu-id="71f70-182">Mithilfe des-Attributs können unterschiedliche Richtlinien auf Controller, Seiten Modelle oder Aktionsmethoden angewendet werden `[EnableCors]` .</span><span class="sxs-lookup"><span data-stu-id="71f70-182">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="71f70-183">Wenn das `[EnableCors]` -Attribut auf einen Controller, ein Seiten Modell oder eine Aktionsmethode angewendet wird und cors in der Middleware aktiviert ist, werden **beide** Richtlinien angewendet.</span><span class="sxs-lookup"><span data-stu-id="71f70-183">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="71f70-184">**Es wird empfohlen, Richtlinien zu kombinieren. Verwenden Sie das** `[EnableCors]` **Attribut oder die Middleware, nicht beide in der gleichen app.**</span><span class="sxs-lookup"><span data-stu-id="71f70-184">**We recommend against combining policies. Use the** `[EnableCors]` **attribute or middleware, not both in the same app.**</span></span>

<span data-ttu-id="71f70-185">Der folgende Code wendet eine andere Richtlinie auf jede Methode an:</span><span class="sxs-lookup"><span data-stu-id="71f70-185">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="71f70-186">Der folgende Code erstellt zwei cors-Richtlinien:</span><span class="sxs-lookup"><span data-stu-id="71f70-186">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="71f70-187">Für die beste Kontrolle über das Einschränken von cors-Anforderungen:</span><span class="sxs-lookup"><span data-stu-id="71f70-187">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="71f70-188">Verwenden Sie `[EnableCors("MyPolicy")]` mit einer benannten Richtlinie.</span><span class="sxs-lookup"><span data-stu-id="71f70-188">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="71f70-189">Definieren Sie keine Standard Richtlinie.</span><span class="sxs-lookup"><span data-stu-id="71f70-189">Don't define a default policy.</span></span>
* <span data-ttu-id="71f70-190">Verwenden Sie das [Endpunkt Routing](#ecors)nicht.</span><span class="sxs-lookup"><span data-stu-id="71f70-190">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="71f70-191">Der Code im nächsten Abschnitt entspricht der vorangehenden Liste.</span><span class="sxs-lookup"><span data-stu-id="71f70-191">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="71f70-192">Anweisungen zum Testen von Code, der dem vorangehenden Code ähnelt, finden Sie unter [Test-cors](#testc) .</span><span class="sxs-lookup"><span data-stu-id="71f70-192">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="71f70-193">Cors deaktivieren</span><span class="sxs-lookup"><span data-stu-id="71f70-193">Disable CORS</span></span>

<span data-ttu-id="71f70-194">Das [[disablecors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) -Attribut deaktiviert **keine** cors, die durch das [Endpunkt Routing](#ecors)aktiviert wurden.</span><span class="sxs-lookup"><span data-stu-id="71f70-194">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does **not**  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="71f70-195">Der folgende Code definiert die cors-Richtlinie `"MyPolicy"` :</span><span class="sxs-lookup"><span data-stu-id="71f70-195">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="71f70-196">Der folgende Code deaktiviert cors für die `GetValues2` Aktion:</span><span class="sxs-lookup"><span data-stu-id="71f70-196">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="71f70-197">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="71f70-197">The preceding code:</span></span>

* <span data-ttu-id="71f70-198">Aktivieren von cors mit dem [Endpunkt Routing](#ecors)nicht.</span><span class="sxs-lookup"><span data-stu-id="71f70-198">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="71f70-199">Definiert keine [cors-Standard Richtlinie](#dp).</span><span class="sxs-lookup"><span data-stu-id="71f70-199">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="71f70-200">Verwendet [[enablecors ("MyPolicy")]](#attr) , um die `"MyPolicy"` cors-Richtlinie für den Controller zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="71f70-200">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="71f70-201">Deaktiviert cors für die- `GetValues2` Methode.</span><span class="sxs-lookup"><span data-stu-id="71f70-201">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="71f70-202">Anweisungen zum Testen des vorangehenden Codes finden Sie unter [Test-cors](#testc) .</span><span class="sxs-lookup"><span data-stu-id="71f70-202">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="71f70-203">Cors-Richtlinien Optionen</span><span class="sxs-lookup"><span data-stu-id="71f70-203">CORS policy options</span></span>

<span data-ttu-id="71f70-204">In diesem Abschnitt werden die verschiedenen Optionen beschrieben, die in einer cors-Richtlinie festgelegt werden können:</span><span class="sxs-lookup"><span data-stu-id="71f70-204">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="71f70-205">Festlegen der zulässigen Ursprünge</span><span class="sxs-lookup"><span data-stu-id="71f70-205">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="71f70-206">Festlegen der zulässigen HTTP-Methoden</span><span class="sxs-lookup"><span data-stu-id="71f70-206">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="71f70-207">Festlegen der zulässigen Anforderungs Header</span><span class="sxs-lookup"><span data-stu-id="71f70-207">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="71f70-208">Festlegen der verfügbar gemachten Antwortheader</span><span class="sxs-lookup"><span data-stu-id="71f70-208">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="71f70-209">Anmelde Informationen in Ursprungs übergreifenden Anforderungen</span><span class="sxs-lookup"><span data-stu-id="71f70-209">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="71f70-210">Festlegen der Preflight-Ablaufzeit</span><span class="sxs-lookup"><span data-stu-id="71f70-210">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="71f70-211"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>wird in aufgerufen `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="71f70-211"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="71f70-212">Für einige Optionen kann es hilfreich sein, zuerst den Abschnitt [wie cors Works](#how-cors) zu lesen.</span><span class="sxs-lookup"><span data-stu-id="71f70-212">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="71f70-213">Festlegen der zulässigen Ursprünge</span><span class="sxs-lookup"><span data-stu-id="71f70-213">Set the allowed origins</span></span>

<span data-ttu-id="71f70-214"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Ermöglicht cors-Anforderungen von allen Ursprüngen mit einem beliebigen Schema ( `http` oder `https` ).</span><span class="sxs-lookup"><span data-stu-id="71f70-214"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="71f70-215">`AllowAnyOrigin`ist unsicher, weil *jede Website* Ursprungs übergreifende Anforderungen an die APP stellen kann.</span><span class="sxs-lookup"><span data-stu-id="71f70-215">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="71f70-216">Die Angabe von `AllowAnyOrigin` und `AllowCredentials` ist eine unsichere Konfiguration und kann zu einer Website übergreifenden Anforderungs Fälschung führen.</span><span class="sxs-lookup"><span data-stu-id="71f70-216">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="71f70-217">Der cors-Dienst gibt eine ungültige cors-Antwort zurück, wenn eine APP mit beiden Methoden konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="71f70-217">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="71f70-218">`AllowAnyOrigin`wirkt sich auf Preflight-Anforderungen und den `Access-Control-Allow-Origin` Header aus.</span><span class="sxs-lookup"><span data-stu-id="71f70-218">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="71f70-219">Weitere Informationen finden Sie im Abschnitt [Preflight Requests (Preflight-Anforderungen](#preflight-requests) ).</span><span class="sxs-lookup"><span data-stu-id="71f70-219">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="71f70-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Legt fest, dass die- <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> Eigenschaft der Richtlinie eine Funktion ist, die es den Ursprüngen ermöglicht, eine konfigurierte Platzhalter Domäne zuzuordnen, wenn ausgewertet wird, ob der Ursprung zulässig ist</span><span class="sxs-lookup"><span data-stu-id="71f70-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="71f70-221">Festlegen der zulässigen HTTP-Methoden</span><span class="sxs-lookup"><span data-stu-id="71f70-221">Set the allowed HTTP methods</span></span>

<span data-ttu-id="71f70-222"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="71f70-222"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="71f70-223">Lässt jede HTTP-Methode zu:</span><span class="sxs-lookup"><span data-stu-id="71f70-223">Allows any HTTP method:</span></span>
* <span data-ttu-id="71f70-224">Wirkt sich auf Preflight-Anforderungen und den `Access-Control-Allow-Methods` Header aus.</span><span class="sxs-lookup"><span data-stu-id="71f70-224">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="71f70-225">Weitere Informationen finden Sie im Abschnitt [Preflight Requests (Preflight-Anforderungen](#preflight-requests) ).</span><span class="sxs-lookup"><span data-stu-id="71f70-225">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="71f70-226">Festlegen der zulässigen Anforderungs Header</span><span class="sxs-lookup"><span data-stu-id="71f70-226">Set the allowed request headers</span></span>

<span data-ttu-id="71f70-227">Um zu ermöglichen, dass bestimmte Header in einer cors-Anforderung gesendet werden, die als [Autor Request Headers](https://xhr.spec.whatwg.org/#request)bezeichnet wird, müssen Sie aufrufen <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> und die zulässigen Header angeben:</span><span class="sxs-lookup"><span data-stu-id="71f70-227">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="71f70-228">Um alle [Autoren Anforderungs Header](https://www.w3.org/TR/cors/#author-request-headers)zuzulassen, geben Sie Folgendes an <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="71f70-228">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="71f70-229">`AllowAnyHeader`wirkt sich auf Preflight-Anforderungen und den [Access-Control-Request-Headers-](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) Header aus.</span><span class="sxs-lookup"><span data-stu-id="71f70-229">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="71f70-230">Weitere Informationen finden Sie im Abschnitt [Preflight Requests (Preflight-Anforderungen](#preflight-requests) ).</span><span class="sxs-lookup"><span data-stu-id="71f70-230">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="71f70-231">Eine cors-Middleware-Richtlinie ist mit bestimmten Headern übereinstimmen, die durch angegeben werden `WithHeaders` `Access-Control-Request-Headers` `WithHeaders`</span><span class="sxs-lookup"><span data-stu-id="71f70-231">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="71f70-232">Nehmen Sie beispielsweise an, dass eine APP wie folgt konfiguriert ist:</span><span class="sxs-lookup"><span data-stu-id="71f70-232">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="71f70-233">Cors-Middleware lehnt eine Preflight-Anforderung mit dem folgenden Anforderungs Header ab, da `Content-Language` ([headernames. contentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) nicht in aufgeführt ist `WithHeaders` :</span><span class="sxs-lookup"><span data-stu-id="71f70-233">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="71f70-234">Die APP gibt eine *200 OK* -Antwort zurück, sendet jedoch keine cors-Header zurück.</span><span class="sxs-lookup"><span data-stu-id="71f70-234">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="71f70-235">Der Browser versucht daher nicht, die Ursprungs übergreifende Anforderung zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="71f70-235">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="71f70-236">Festlegen der verfügbar gemachten Antwortheader</span><span class="sxs-lookup"><span data-stu-id="71f70-236">Set the exposed response headers</span></span>

<span data-ttu-id="71f70-237">Standardmäßig macht der Browser nicht alle Antwortheader für die app verfügbar.</span><span class="sxs-lookup"><span data-stu-id="71f70-237">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="71f70-238">Weitere Informationen finden Sie unter [W3C Cross-Origin Resource Sharing (Terminologie): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="71f70-238">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="71f70-239">Die standardmäßig verfügbaren Antwortheader lauten wie folgt:</span><span class="sxs-lookup"><span data-stu-id="71f70-239">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="71f70-240">Mit der cors-Spezifikation werden die Header für *einfache Antworten*aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="71f70-240">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="71f70-241">Um andere Header für die app verfügbar zu machen, wenden Sie Folgendes an <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="71f70-241">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="71f70-242">Anmelde Informationen in Ursprungs übergreifenden Anforderungen</span><span class="sxs-lookup"><span data-stu-id="71f70-242">Credentials in cross-origin requests</span></span>

<span data-ttu-id="71f70-243">Anmelde Informationen erfordern eine spezielle Behandlung in einer cors-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="71f70-243">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="71f70-244">Standardmäßig sendet der Browser keine Anmelde Informationen mit einer Ursprungs übergreifenden Anforderung.</span><span class="sxs-lookup"><span data-stu-id="71f70-244">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="71f70-245">Anmelde Informationen umfassen Cookies und http-Authentifizierungs Schemas.</span><span class="sxs-lookup"><span data-stu-id="71f70-245">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="71f70-246">Um Anmelde Informationen mit einer Ursprungs übergreifenden Anforderung zu senden, muss der Client `XMLHttpRequest.withCredentials` auf festlegen `true` .</span><span class="sxs-lookup"><span data-stu-id="71f70-246">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="71f70-247">`XMLHttpRequest`Direkt verwenden:</span><span class="sxs-lookup"><span data-stu-id="71f70-247">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="71f70-248">Verwenden von jQuery:</span><span class="sxs-lookup"><span data-stu-id="71f70-248">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="71f70-249">Verwenden der [Fetch-API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="71f70-249">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="71f70-250">Der Server muss die Anmelde Informationen zulassen.</span><span class="sxs-lookup"><span data-stu-id="71f70-250">The server must allow the credentials.</span></span> <span data-ttu-id="71f70-251">Um Ursprungs übergreifende Anmelde Informationen zuzulassen, geben Sie Folgendes an <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :</span><span class="sxs-lookup"><span data-stu-id="71f70-251">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="71f70-252">Die HTTP-Antwort enthält einen- `Access-Control-Allow-Credentials` Header, der dem Browser mitteilt, dass der Server Anmelde Informationen für eine Ursprungs übergreifende Anforderung zulässt.</span><span class="sxs-lookup"><span data-stu-id="71f70-252">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="71f70-253">Wenn der Browser Anmelde Informationen sendet, die Antwort jedoch keinen gültigen `Access-Control-Allow-Credentials` Header enthält, macht der Browser die Antwort nicht an die app verfügbar, und die Ursprungs übergreifende Anforderung schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="71f70-253">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="71f70-254">Das Zulassen von Ursprungs übergreifenden Anmelde Informationen stellt ein Sicherheitsrisiko dar.</span><span class="sxs-lookup"><span data-stu-id="71f70-254">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="71f70-255">Eine Website in einer anderen Domäne kann die Anmelde Informationen des angemeldeten Benutzers ohne das Wissen des Benutzers an die APP im Auftrag des Benutzers senden.</span><span class="sxs-lookup"><span data-stu-id="71f70-255">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="71f70-256">In der cors-Spezifikation wird auch festgelegt, dass das Festlegen von Ursprüngen auf `"*"` (alle Ursprünge) ungültig ist, wenn der `Access-Control-Allow-Credentials` Header vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="71f70-256">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="71f70-257">Preflight-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="71f70-257">Preflight requests</span></span>

<span data-ttu-id="71f70-258">Bei einigen cors-Anforderungen sendet der Browser eine zusätzliche [options](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) Anforderung, bevor die tatsächliche Anforderung gesendet wird.</span><span class="sxs-lookup"><span data-stu-id="71f70-258">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="71f70-259">Diese Anforderung wird als [Preflight-Anforderung](https://developer.mozilla.org/docs/Glossary/Preflight_request)bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="71f70-259">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="71f70-260">Der Browser kann die Preflight-Anforderung überspringen, wenn **alle** der folgenden Bedingungen zutreffen:</span><span class="sxs-lookup"><span data-stu-id="71f70-260">The browser can skip the preflight request if **all** the following conditions are true:</span></span>

* <span data-ttu-id="71f70-261">Die Anforderungs Methode ist Get, Head oder Post.</span><span class="sxs-lookup"><span data-stu-id="71f70-261">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="71f70-262">Von der App werden keine anderen Anforderungs Header als `Accept` , `Accept-Language` , `Content-Language` , `Content-Type` oder `Last-Event-ID` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="71f70-262">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="71f70-263">`Content-Type`Wenn festgelegt, hat der-Header einen der folgenden Werte:</span><span class="sxs-lookup"><span data-stu-id="71f70-263">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="71f70-264">Die Regel für Anforderungs Header, die für die Client Anforderung festgelegt wurde, gilt für Header, die die APP festlegt, indem `setRequestHeader` für das-Objekt aufgerufen wird `XMLHttpRequest` .</span><span class="sxs-lookup"><span data-stu-id="71f70-264">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="71f70-265">In der cors-Spezifikation werden diese Header [Anforderungs Header für Autoren](https://www.w3.org/TR/cors/#author-request-headers)aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="71f70-265">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="71f70-266">Die Regel gilt nicht für Header, die vom Browser festgelegt werden können, z `User-Agent` `Host` . b., oder `Content-Length` .</span><span class="sxs-lookup"><span data-stu-id="71f70-266">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="71f70-267">Im folgenden finden Sie eine Beispiel Antwort, die der Preflight-Anforderung ähnelt, die im Abschnitt " [Test cors](#testc) " dieses Dokuments von der Schaltfläche **[Put Test]** erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="71f70-267">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

```
General:
Request URL: https://cors3.azurewebsites.net/api/values/5
Request Method: OPTIONS
Status Code: 204 No Content

Response Headers:
Access-Control-Allow-Methods: PUT,DELETE,GET
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f8...8;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Vary: Origin

Request Headers:
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Method: PUT
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

<span data-ttu-id="71f70-268">Die Preflight-Anforderung verwendet die [http Options](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) -Methode.</span><span class="sxs-lookup"><span data-stu-id="71f70-268">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="71f70-269">Die folgenden Header sind möglicherweise enthalten:</span><span class="sxs-lookup"><span data-stu-id="71f70-269">It may include the following headers:</span></span>

* <span data-ttu-id="71f70-270">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): die HTTP-Methode, die für die tatsächliche Anforderung verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="71f70-270">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="71f70-271">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): eine Liste mit Anforderungs Headern, die von der APP für die tatsächliche Anforderung festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="71f70-271">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="71f70-272">Wie bereits erwähnt, enthält dies keine Header, die vom Browser festgelegt werden, z `User-Agent` . b..</span><span class="sxs-lookup"><span data-stu-id="71f70-272">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="71f70-273">Access-Control-Allow-Methods</span><span class="sxs-lookup"><span data-stu-id="71f70-273">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="71f70-274">Wenn die Preflight-Anforderung verweigert wird, gibt die APP eine Antwort zurück, `200 OK` legt jedoch keine cors-Header fest.</span><span class="sxs-lookup"><span data-stu-id="71f70-274">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="71f70-275">Der Browser versucht daher nicht, die Ursprungs übergreifende Anforderung zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="71f70-275">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="71f70-276">Ein Beispiel für eine verweigerte Preflight-Anforderung finden Sie im Abschnitt zum [Testen von cors](#testc) in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="71f70-276">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="71f70-277">Mit den F12-Tools zeigt die Konsolen-App je nach Browser einen Fehler an, der einem der folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="71f70-277">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="71f70-278">Firefox: Ursprungs übergreifende Anforderung blockiert: die gleiche Ursprungs Richtlinie lässt das Lesen der Remote Ressource unter nicht zu `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5` .</span><span class="sxs-lookup"><span data-stu-id="71f70-278">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="71f70-279">(Ursache: die cors-Anforderung war nicht erfolgreich).</span><span class="sxs-lookup"><span data-stu-id="71f70-279">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="71f70-280">Weitere Informationen</span><span class="sxs-lookup"><span data-stu-id="71f70-280">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="71f70-281">Chrom basiert: der Zugriff auf " https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5 " vom Ursprung "" wurde https://cors3.azurewebsites.net durch eine cors-Richtlinie blockiert: die Antwort auf eine Preflight-Anforderung übergibt die Zugriffs Steuerungs Prüfung nicht: Es ist kein "Access-Control-Allow-Origin"-Header für die angeforderte Ressource vorhanden.</span><span class="sxs-lookup"><span data-stu-id="71f70-281">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="71f70-282">Falls eine opake Antwort Ihre Anforderungen erfüllt, legen Sie den Modus der Anforderung auf 'no-cors' fest, um CORS für den Ressourcenabruf zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="71f70-282">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="71f70-283">Um bestimmte Header zuzulassen, geben Sie Folgendes an <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="71f70-283">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="71f70-284">Um alle [Autoren Anforderungs Header](https://www.w3.org/TR/cors/#author-request-headers)zuzulassen, geben Sie Folgendes an <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="71f70-284">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="71f70-285">Browser sind nicht konsistent, wie Sie festgelegt werden `Access-Control-Request-Headers` .</span><span class="sxs-lookup"><span data-stu-id="71f70-285">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="71f70-286">Wenn beides:</span><span class="sxs-lookup"><span data-stu-id="71f70-286">If either:</span></span>

* <span data-ttu-id="71f70-287">Header werden auf einen anderen Wert als festgelegt.`"*"`</span><span class="sxs-lookup"><span data-stu-id="71f70-287">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="71f70-288"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>wird aufgerufen: Schließen Sie mindestens `Accept` , `Content-Type` , und `Origin` sowie alle benutzerdefinierten Header ein, die Sie unterstützen möchten.</span><span class="sxs-lookup"><span data-stu-id="71f70-288"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="71f70-289">Automatischer Preflight-Anforderungs Code</span><span class="sxs-lookup"><span data-stu-id="71f70-289">Automatic preflight request code</span></span>

<span data-ttu-id="71f70-290">Wenn die cors-Richtlinie angewendet wird, gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="71f70-290">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="71f70-291">Global durch Aufrufen von `app.UseCors` in `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="71f70-291">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="71f70-292">Verwenden des- `[EnableCors]` Attributs.</span><span class="sxs-lookup"><span data-stu-id="71f70-292">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="71f70-293">ASP.net Core antwortet auf die Preflight-Options Anforderung.</span><span class="sxs-lookup"><span data-stu-id="71f70-293">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="71f70-294">Die Aktivierung von cors auf Basis von Endpunkten `RequireCors` unterstützt derzeit **keine** automatischen Preflight-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="71f70-294">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support automatic preflight requests.</span></span>

<span data-ttu-id="71f70-295">Dieses Verhalten wird im Abschnitt " [Test-cors](#testc) " dieses Dokuments veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="71f70-295">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="71f70-296">[Httpoptions]-Attribut für Preflight-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="71f70-296">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="71f70-297">Wenn cors mit der entsprechenden Richtlinie aktiviert ist, reagieren ASP.net Core in der Regel automatisch auf cors-Preflight-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="71f70-297">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="71f70-298">In einigen Szenarios ist dies möglicherweise nicht der Fall.</span><span class="sxs-lookup"><span data-stu-id="71f70-298">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="71f70-299">Beispielsweise bei Verwendung von [cors mit dem Endpunkt Routing](#ecors).</span><span class="sxs-lookup"><span data-stu-id="71f70-299">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="71f70-300">Der folgende Code verwendet das [[httpoptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) -Attribut, um Endpunkte für Options Anforderungen zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="71f70-300">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="71f70-301">Anweisungen zum Testen des vorangehenden Codes finden Sie unter [Testen von cors mit dem Endpunkt Routing und [httpoptions]](#tcer) .</span><span class="sxs-lookup"><span data-stu-id="71f70-301">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="71f70-302">Festlegen der Preflight-Ablaufzeit</span><span class="sxs-lookup"><span data-stu-id="71f70-302">Set the preflight expiration time</span></span>

<span data-ttu-id="71f70-303">Der- `Access-Control-Max-Age` Header gibt an, wie lange die Antwort auf die Preflight-Anforderung zwischengespeichert werden kann.</span><span class="sxs-lookup"><span data-stu-id="71f70-303">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="71f70-304">Um diesen Header festzulegen, wenden Sie Folgendes an <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :</span><span class="sxs-lookup"><span data-stu-id="71f70-304">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="71f70-305">Funktionsweise von cors</span><span class="sxs-lookup"><span data-stu-id="71f70-305">How CORS works</span></span>

<span data-ttu-id="71f70-306">In diesem Abschnitt wird beschrieben, was in einer [cors](https://developer.mozilla.org/docs/Web/HTTP/CORS) -Anforderung auf der Ebene der HTTP-Nachrichten geschieht.</span><span class="sxs-lookup"><span data-stu-id="71f70-306">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="71f70-307">Cors ist **kein** Sicherheits Feature.</span><span class="sxs-lookup"><span data-stu-id="71f70-307">CORS is **not** a security feature.</span></span> <span data-ttu-id="71f70-308">Cors ist ein W3C-Standard, der es einem Server ermöglicht, die Richtlinie für denselben Ursprung zu lockern.</span><span class="sxs-lookup"><span data-stu-id="71f70-308">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="71f70-309">Ein böswilliger Akteur könnte z. b. Site übergreifende Skripts [(Cross-Site Scripting, XSS)](xref:security/cross-site-scripting) für Ihre Website verwenden und eine Website übergreifende Anforderung an Ihre cors-aktivierte Website ausführen, um Informationen zu stehlen.</span><span class="sxs-lookup"><span data-stu-id="71f70-309">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="71f70-310">Eine API ist nicht sicherer, da Sie cors zulässt.</span><span class="sxs-lookup"><span data-stu-id="71f70-310">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="71f70-311">Es liegt an dem Client (Browser), cors zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="71f70-311">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="71f70-312">Der Server führt die Anforderung aus und gibt die Antwort zurück. dabei handelt es sich um den Client, der einen Fehler zurückgibt und die Antwort blockiert.</span><span class="sxs-lookup"><span data-stu-id="71f70-312">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="71f70-313">Eines der folgenden Tools zeigt z. b. die Serverantwort an:</span><span class="sxs-lookup"><span data-stu-id="71f70-313">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="71f70-314">Fiddler</span><span class="sxs-lookup"><span data-stu-id="71f70-314">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="71f70-315">Postman</span><span class="sxs-lookup"><span data-stu-id="71f70-315">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="71f70-316">.Net httpclient</span><span class="sxs-lookup"><span data-stu-id="71f70-316">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="71f70-317">Einen Webbrowser, indem Sie die URL in die Adressleiste eingeben.</span><span class="sxs-lookup"><span data-stu-id="71f70-317">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="71f70-318">Es ist eine Möglichkeit für einen Server, Browser das Ausführen einer Ursprungs übergreifenden [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) -oder [Fetch-API](https://developer.mozilla.org/docs/Web/API/Fetch_API) -Anforderung zu gestatten, die andernfalls unzulässig wäre.</span><span class="sxs-lookup"><span data-stu-id="71f70-318">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="71f70-319">Browser ohne cors können keine Ursprungs übergreifenden Anforderungen ausführen.</span><span class="sxs-lookup"><span data-stu-id="71f70-319">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="71f70-320">Vor cors wurde [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) verwendet, um diese Einschränkung zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="71f70-320">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="71f70-321">JSONP verwendet nicht XHR, sondern verwendet das- `<script>` Tag, um die Antwort zu empfangen.</span><span class="sxs-lookup"><span data-stu-id="71f70-321">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="71f70-322">Skripts können Ursprungs übergreifend geladen werden.</span><span class="sxs-lookup"><span data-stu-id="71f70-322">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="71f70-323">In der [cors-Spezifikation](https://www.w3.org/TR/cors/) wurden mehrere neue HTTP-Header eingeführt, die Ursprungs übergreifende Anforderungen ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="71f70-323">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="71f70-324">Wenn ein Browser cors unterstützt, werden diese Header automatisch für Ursprungs übergreifende Anforderungen festgelegt.</span><span class="sxs-lookup"><span data-stu-id="71f70-324">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="71f70-325">Benutzerdefinierter JavaScript-Code ist nicht erforderlich, um cors zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="71f70-325">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="71f70-326">[Schaltfläche "Test platzieren](https://cors3.azurewebsites.net/test) " im bereitgestellten [Beispiel](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span><span class="sxs-lookup"><span data-stu-id="71f70-326">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="71f70-327">Im folgenden finden Sie ein Beispiel für eine Ursprungs übergreifende Anforderung von der Test Schaltfläche " [Werte](https://cors3.azurewebsites.net/) " zu `https://cors1.azurewebsites.net/api/values` .</span><span class="sxs-lookup"><span data-stu-id="71f70-327">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="71f70-328">Der `Origin` Header:</span><span class="sxs-lookup"><span data-stu-id="71f70-328">The `Origin` header:</span></span>

* <span data-ttu-id="71f70-329">Stellt die Domäne der Site bereit, von der die Anforderung stammt.</span><span class="sxs-lookup"><span data-stu-id="71f70-329">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="71f70-330">Ist erforderlich und muss sich vom Host unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="71f70-330">Is required and must be different from the host.</span></span>

<span data-ttu-id="71f70-331">**Allgemeine Header**</span><span class="sxs-lookup"><span data-stu-id="71f70-331">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="71f70-332">**Antwortheader**</span><span class="sxs-lookup"><span data-stu-id="71f70-332">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="71f70-333">**Anforderungsheader**</span><span class="sxs-lookup"><span data-stu-id="71f70-333">**Request headers**</span></span>

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Connection: keep-alive
Host: cors1.azurewebsites.net
Origin: https://cors3.azurewebsites.net
Referer: https://cors3.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0 ...
```

<span data-ttu-id="71f70-334">In `OPTIONS` Anforderungen legt der Server den Header der **Antwortheader** `Access-Control-Allow-Origin: {allowed origin}` in der Antwort fest.</span><span class="sxs-lookup"><span data-stu-id="71f70-334">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="71f70-335">Beispielsweise enthält die Schaltfläche für das bereitgestellte [Beispiel](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) [delete [enablecors]](https://cors1.azurewebsites.net/test?number=2) `OPTIONS` die folgenden Header:</span><span class="sxs-lookup"><span data-stu-id="71f70-335">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="71f70-336">**Allgemeine Header**</span><span class="sxs-lookup"><span data-stu-id="71f70-336">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="71f70-337">**Antwortheader**</span><span class="sxs-lookup"><span data-stu-id="71f70-337">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="71f70-338">**Anforderungsheader**</span><span class="sxs-lookup"><span data-stu-id="71f70-338">**Request headers**</span></span>

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Headers: content-type
Access-Control-Request-Method: DELETE
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/test?number=2
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

<span data-ttu-id="71f70-339">In den vorangehenden **Antwort Headern**legt der Server den [Access-Control-Allow-Origin-](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) Header in der Antwort fest.</span><span class="sxs-lookup"><span data-stu-id="71f70-339">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="71f70-340">Der `https://cors1.azurewebsites.net` Wert dieses Headers entspricht dem `Origin` Header aus der Anforderung.</span><span class="sxs-lookup"><span data-stu-id="71f70-340">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="71f70-341">Wenn <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> aufgerufen wird, wird der Platzhalter `Access-Control-Allow-Origin: *` Wert zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="71f70-341">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="71f70-342">`AllowAnyOrigin`ermöglicht einen beliebigen Ursprung.</span><span class="sxs-lookup"><span data-stu-id="71f70-342">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="71f70-343">Wenn die Antwort den Header nicht enthält `Access-Control-Allow-Origin` , schlägt die Ursprungs übergreifende Anforderung fehl.</span><span class="sxs-lookup"><span data-stu-id="71f70-343">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="71f70-344">Der Browser lässt die Anforderung insbesondere nicht zu.</span><span class="sxs-lookup"><span data-stu-id="71f70-344">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="71f70-345">Auch wenn der Server eine erfolgreiche Antwort zurückgibt, stellt der Browser die Antwort nicht für die Client-App zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="71f70-345">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="71f70-346">Anzeige Options Anforderungen</span><span class="sxs-lookup"><span data-stu-id="71f70-346">Display OPTIONS requests</span></span>

<span data-ttu-id="71f70-347">Standardmäßig zeigen die Browser Chrome und Edge keine Options Anforderungen auf der Registerkarte Netzwerk der F12-Tools an.</span><span class="sxs-lookup"><span data-stu-id="71f70-347">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="71f70-348">So zeigen Sie Options Anforderungen in diesen Browsern an:</span><span class="sxs-lookup"><span data-stu-id="71f70-348">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="71f70-349">`chrome://flags/#out-of-blink-cors` oder `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="71f70-349">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="71f70-350">Deaktivieren Sie das-Flag.</span><span class="sxs-lookup"><span data-stu-id="71f70-350">disable the flag.</span></span>
* <span data-ttu-id="71f70-351">„Neu starten“.</span><span class="sxs-lookup"><span data-stu-id="71f70-351">restart.</span></span>

<span data-ttu-id="71f70-352">Firefox zeigt Options Anforderungen standardmäßig an.</span><span class="sxs-lookup"><span data-stu-id="71f70-352">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="71f70-353">Cors in IIS</span><span class="sxs-lookup"><span data-stu-id="71f70-353">CORS in IIS</span></span>

<span data-ttu-id="71f70-354">Beim Bereitstellen in IIS muss cors vor der Windows-Authentifizierung ausgeführt werden, wenn der Server nicht für den anonymen Zugriff konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="71f70-354">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="71f70-355">Zur Unterstützung dieses Szenarios muss das [IIS cors-Modul](https://www.iis.net/downloads/microsoft/iis-cors-module) für die APP installiert und konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="71f70-355">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="71f70-356">Testen von CORS</span><span class="sxs-lookup"><span data-stu-id="71f70-356">Test CORS</span></span>

<span data-ttu-id="71f70-357">Der [Beispiel Download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) enthält Code zum Testen von cors.</span><span class="sxs-lookup"><span data-stu-id="71f70-357">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="71f70-358">[Informationen zum Herunterladen](xref:index#how-to-download-a-sample) finden Sie hier.</span><span class="sxs-lookup"><span data-stu-id="71f70-358">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="71f70-359">Das Beispiel ist ein API-Projekt mit Razor hinzugefügten Seiten:</span><span class="sxs-lookup"><span data-stu-id="71f70-359">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="71f70-360">`WithOrigins("https://localhost:<port>");`sollte nur zum Testen einer Beispiel-App verwendet werden, die dem [Download Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors)ähnelt.</span><span class="sxs-lookup"><span data-stu-id="71f70-360">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="71f70-361">Im folgenden `ValuesController` finden Sie die Endpunkte für Tests:</span><span class="sxs-lookup"><span data-stu-id="71f70-361">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="71f70-362">[Mydisplayrouteinfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) wird vom [Rick.Docs. Samples. routeinfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) -nuget-Paket bereitgestellt und zeigt Routeninformationen an.</span><span class="sxs-lookup"><span data-stu-id="71f70-362">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="71f70-363">Testen Sie den vorangehenden Beispielcode, indem Sie einen der folgenden Ansätze verwenden:</span><span class="sxs-lookup"><span data-stu-id="71f70-363">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="71f70-364">Verwenden Sie die bereitgestellte Beispiel-app unter [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/) .</span><span class="sxs-lookup"><span data-stu-id="71f70-364">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="71f70-365">Das Beispiel muss nicht heruntergeladen werden.</span><span class="sxs-lookup"><span data-stu-id="71f70-365">There is no need to download the sample.</span></span>
* <span data-ttu-id="71f70-366">Führen Sie das Beispiel mit `dotnet run` der Standard-URL von aus `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="71f70-366">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="71f70-367">Führen Sie das Beispiel aus Visual Studio aus, wobei der Port auf 44398 für eine URL von festgelegt ist `https://localhost:44398` .</span><span class="sxs-lookup"><span data-stu-id="71f70-367">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="71f70-368">Verwenden eines Browsers mit den F12-Tools:</span><span class="sxs-lookup"><span data-stu-id="71f70-368">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="71f70-369">Wählen Sie die Schaltfläche **Werte** , und überprüfen Sie die Header auf der Registerkarte **Netzwerk** .</span><span class="sxs-lookup"><span data-stu-id="71f70-369">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="71f70-370">Wählen Sie die Schaltfläche **Test platzieren** aus.</span><span class="sxs-lookup"><span data-stu-id="71f70-370">Select the **PUT test** button.</span></span> <span data-ttu-id="71f70-371">Anweisungen zum Anzeigen der Options Anforderung finden Sie unter [Anzeigen von Options Anforderungen](#options) .</span><span class="sxs-lookup"><span data-stu-id="71f70-371">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="71f70-372">Der **Put-Test** erstellt zwei Anforderungen, eine Preflight-Anforderung für Optionen und die PUT-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="71f70-372">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="71f70-373">Wählen Sie die **`GetValues2 [DisableCors]`** Schaltfläche aus, um eine fehlgeschlagene cors-Anforderung</span><span class="sxs-lookup"><span data-stu-id="71f70-373">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="71f70-374">Wie im Dokument erwähnt, gibt die Antwort 200 Erfolg zurück, aber die cors-Anforderung wird nicht hergestellt.</span><span class="sxs-lookup"><span data-stu-id="71f70-374">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="71f70-375">Wählen Sie die Registerkarte **Konsole** aus, um den cors-Fehler anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="71f70-375">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="71f70-376">Abhängig vom Browser wird ein Fehler angezeigt, der dem folgenden Beispiel ähnelt:</span><span class="sxs-lookup"><span data-stu-id="71f70-376">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="71f70-377">Der Zugriff zum Abrufen auf `'https://cors1.azurewebsites.net/api/values/GetValues2'` der Ursprungsseite wurde `'https://cors3.azurewebsites.net'` durch die cors-Richtlinie blockiert: für die angeforderte Ressource ist kein "Access-Control-Allow-Origin"-Header vorhanden.</span><span class="sxs-lookup"><span data-stu-id="71f70-377">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="71f70-378">Falls eine opake Antwort Ihre Anforderungen erfüllt, legen Sie den Modus der Anforderung auf 'no-cors' fest, um CORS für den Ressourcenabruf zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="71f70-378">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="71f70-379">Cors-aktivierte Endpunkte können mit einem Tool wie [curl](https://curl.haxx.se/), " [fddler](https://www.telerik.com/fiddler)" oder [Postman](https://www.getpostman.com/)getestet werden.</span><span class="sxs-lookup"><span data-stu-id="71f70-379">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="71f70-380">Wenn Sie ein Tool verwenden, muss sich der Ursprung der durch den-Header angegebenen Anforderung `Origin` von dem Host unterscheiden, der die Anforderung empfängt.</span><span class="sxs-lookup"><span data-stu-id="71f70-380">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="71f70-381">Wenn die Anforderung aufgrund des Werts des Headers nicht *Kreuz Ursprungs* ist `Origin` :</span><span class="sxs-lookup"><span data-stu-id="71f70-381">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="71f70-382">Es ist nicht erforderlich, dass cors-Middleware die Anforderung verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="71f70-382">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="71f70-383">Cors-Header werden in der Antwort nicht zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="71f70-383">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="71f70-384">Der folgende Befehl verwendet `curl` , um eine Options Anforderung mit Informationen auszugeben:</span><span class="sxs-lookup"><span data-stu-id="71f70-384">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="71f70-385">Testen von cors mit dem Endpunkt Routing und [httpoptions]</span><span class="sxs-lookup"><span data-stu-id="71f70-385">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="71f70-386">Die Aktivierung von cors auf Basis von Endpunkten `RequireCors` unterstützt **not** derzeit keine [automatischen Preflight-Anforderungen](#apf).</span><span class="sxs-lookup"><span data-stu-id="71f70-386">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="71f70-387">Beachten Sie den folgenden Code, der das [Endpunkt Routing verwendet, um cors zu aktivieren](#ecors):</span><span class="sxs-lookup"><span data-stu-id="71f70-387">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="71f70-388">Im folgenden `TodoItems1Controller` finden Sie Endpunkte zum Testen:</span><span class="sxs-lookup"><span data-stu-id="71f70-388">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="71f70-389">Testen Sie den vorangehenden Code auf der [Seite "Test](https://cors1.azurewebsites.net/test?number=1) " des bereitgestellten [Beispiels.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span><span class="sxs-lookup"><span data-stu-id="71f70-389">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="71f70-390">Die Schaltflächen " **delete [enablecors]** " und " **Get [enablecors]** " sind erfolgreich, da die Endpunkte `[EnableCors]` Preflight-Anforderungen aufweisen und darauf reagieren.</span><span class="sxs-lookup"><span data-stu-id="71f70-390">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="71f70-391">Die anderen Endpunkte schlagen fehl.</span><span class="sxs-lookup"><span data-stu-id="71f70-391">The other endpoints fails.</span></span> <span data-ttu-id="71f70-392">Die Schaltfläche **Get** schlägt fehl, da das [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) Folgendes sendet:</span><span class="sxs-lookup"><span data-stu-id="71f70-392">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="71f70-393">Im folgenden `TodoItems2Controller` werden ähnliche Endpunkte bereitgestellt, aber es ist expliziter Code enthalten, der auf Options Anforderungen antwortet:</span><span class="sxs-lookup"><span data-stu-id="71f70-393">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="71f70-394">Testen Sie den vorangehenden Code auf der [Seite "Test](https://cors1.azurewebsites.net/test?number=2) " des bereitgestellten Beispiels.</span><span class="sxs-lookup"><span data-stu-id="71f70-394">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="71f70-395">Wählen Sie in der Dropdown Liste **Controller** den Wert **Preflight** aus, und legen Sie dann **Controller fest**.</span><span class="sxs-lookup"><span data-stu-id="71f70-395">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="71f70-396">Alle cors-Aufrufe an die `TodoItems2Controller` Endpunkte sind erfolgreich.</span><span class="sxs-lookup"><span data-stu-id="71f70-396">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="71f70-397">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="71f70-397">Additional resources</span></span>

* [<span data-ttu-id="71f70-398">Cross-Origin Resource Sharing (cors)</span><span class="sxs-lookup"><span data-stu-id="71f70-398">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="71f70-399">Einführung in das IIS cors-Modul</span><span class="sxs-lookup"><span data-stu-id="71f70-399">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="71f70-400">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="71f70-400">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="71f70-401">In diesem Artikel wird gezeigt, wie Sie cors in einer ASP.net Core-App aktivieren.</span><span class="sxs-lookup"><span data-stu-id="71f70-401">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="71f70-402">Die Browsersicherheit verhindert, dass eine Webseite Anforderungen an eine andere Domäne als diejenige stellt, die die Webseite versorgt hat.</span><span class="sxs-lookup"><span data-stu-id="71f70-402">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="71f70-403">Diese Einschränkung wird als *Richtlinie des gleichen Ursprungs* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="71f70-403">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="71f70-404">Die Richtlinie des gleichen Ursprungs verhindert, dass eine schädliche Website sensible Daten von einer anderen Website liest.</span><span class="sxs-lookup"><span data-stu-id="71f70-404">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="71f70-405">Manchmal möchten Sie möglicherweise, dass andere Websites Ursprungs übergreifende Anforderungen an Ihre APP stellen.</span><span class="sxs-lookup"><span data-stu-id="71f70-405">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="71f70-406">Weitere Informationen finden Sie im [Artikel zu Mozilla cors](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="71f70-406">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="71f70-407">[Cross-Origin Resource Sharing](https://www.w3.org/TR/cors/) (cors):</span><span class="sxs-lookup"><span data-stu-id="71f70-407">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="71f70-408">Ist ein W3C-Standard, der es einem Server ermöglicht, die Richtlinie für denselben Ursprung zu lockern.</span><span class="sxs-lookup"><span data-stu-id="71f70-408">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="71f70-409">Bei handelt es sich **nicht** um ein Sicherheits Feature, sondern um die Sicherheit von cors.</span><span class="sxs-lookup"><span data-stu-id="71f70-409">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="71f70-410">Eine API wird durch das Zulassen von cors nicht sicherer.</span><span class="sxs-lookup"><span data-stu-id="71f70-410">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="71f70-411">Weitere Informationen finden Sie unter [Funktionsweise von cors](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="71f70-411">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="71f70-412">Ermöglicht einem Server das explizite zulassen einiger Ursprungs übergreifender Anforderungen, während andere abgelehnt werden.</span><span class="sxs-lookup"><span data-stu-id="71f70-412">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="71f70-413">Ist sicherer und flexibler als frühere Techniken, z. b. [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="71f70-413">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="71f70-414">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="71f70-414">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="71f70-415">Gleicher Ursprung</span><span class="sxs-lookup"><span data-stu-id="71f70-415">Same origin</span></span>

<span data-ttu-id="71f70-416">Zwei URLs haben denselben Ursprung, wenn Sie identische Schemas, Hosts und Ports aufweisen ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="71f70-416">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="71f70-417">Diese beiden URLs haben denselben Ursprung:</span><span class="sxs-lookup"><span data-stu-id="71f70-417">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="71f70-418">Diese URLs haben andere Ursprünge als die beiden vorherigen URLs:</span><span class="sxs-lookup"><span data-stu-id="71f70-418">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="71f70-419">`https://example.net`: Andere Domäne</span><span class="sxs-lookup"><span data-stu-id="71f70-419">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="71f70-420">`https://www.example.com/foo.html`: Untergeordnete Domäne</span><span class="sxs-lookup"><span data-stu-id="71f70-420">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="71f70-421">`http://example.com/foo.html`: Anderes Schema</span><span class="sxs-lookup"><span data-stu-id="71f70-421">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="71f70-422">`https://example.com:9000/foo.html`: Anderer Port</span><span class="sxs-lookup"><span data-stu-id="71f70-422">`https://example.com:9000/foo.html`: Different port</span></span>

<span data-ttu-id="71f70-423">Internet Explorer berücksichtigt den Port nicht, wenn Ursprünge verglichen werden.</span><span class="sxs-lookup"><span data-stu-id="71f70-423">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="71f70-424">Cors mit benannten Richtlinie und Middleware</span><span class="sxs-lookup"><span data-stu-id="71f70-424">CORS with named policy and middleware</span></span>

<span data-ttu-id="71f70-425">Cors-Middleware verarbeitet Ursprungs übergreifende Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="71f70-425">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="71f70-426">Der folgende Code aktiviert cors für die gesamte App mit dem angegebenen Ursprung:</span><span class="sxs-lookup"><span data-stu-id="71f70-426">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="71f70-427">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="71f70-427">The preceding code:</span></span>

* <span data-ttu-id="71f70-428">Legt den Richtlinien Namen auf " \_ myallowspecificorigins" fest.</span><span class="sxs-lookup"><span data-stu-id="71f70-428">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="71f70-429">Der Richtlinien Name ist willkürlich.</span><span class="sxs-lookup"><span data-stu-id="71f70-429">The policy name is arbitrary.</span></span>
* <span data-ttu-id="71f70-430">Ruft die <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> Erweiterungsmethode auf, die cors aktiviert.</span><span class="sxs-lookup"><span data-stu-id="71f70-430">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="71f70-431">Ruft <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> mit einem [Lambda-Ausdruck](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)auf.</span><span class="sxs-lookup"><span data-stu-id="71f70-431">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="71f70-432">Der Lambda-Ausdruck nimmt ein- <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> Objekt an.</span><span class="sxs-lookup"><span data-stu-id="71f70-432">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="71f70-433">[Konfigurationsoptionen](#cors-policy-options), wie z `WithOrigins` . b., werden weiter unten in diesem Artikel beschrieben.</span><span class="sxs-lookup"><span data-stu-id="71f70-433">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="71f70-434">Der <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> -Methodenaufrufe fügt cors-Dienste zum Dienst Container der APP hinzu:</span><span class="sxs-lookup"><span data-stu-id="71f70-434">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="71f70-435">Weitere Informationen finden Sie unter [cors-Richtlinien Optionen](#cpo) in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="71f70-435">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="71f70-436">Die- <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> Methode kann Methoden verketten, wie im folgenden Code gezeigt:</span><span class="sxs-lookup"><span data-stu-id="71f70-436">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="71f70-437">Hinweis: die URL darf **keinen** nachgestellten Schrägstrich ( `/` ) enthalten.</span><span class="sxs-lookup"><span data-stu-id="71f70-437">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="71f70-438">Wenn die URL mit beendet `/` wird, gibt der Vergleich zurück, `false` und es wird kein-Header zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="71f70-438">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="71f70-439">Mit dem folgenden Code werden cors-Richtlinien auf alle App-Endpunkte über cors-Middleware angewendet:</span><span class="sxs-lookup"><span data-stu-id="71f70-439">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseHsts();
    }

    app.UseCors();

    app.UseHttpsRedirection();
    app.UseMvc();
}
```
<span data-ttu-id="71f70-440">Hinweis: `UseCors` muss vor aufgerufen werden `UseMvc` .</span><span class="sxs-lookup"><span data-stu-id="71f70-440">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="71f70-441">Weitere Informationen finden Sie unter [Aktivieren von cors in Razor Seiten, Controllern und Aktionsmethoden](#ecors) zum Anwenden der cors-Richtlinie auf Seiten-/Controller-/Aktions</span><span class="sxs-lookup"><span data-stu-id="71f70-441">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="71f70-442">Anweisungen zum Testen von Code, der dem vorangehenden Code ähnelt, finden Sie unter [Test-cors](#test) .</span><span class="sxs-lookup"><span data-stu-id="71f70-442">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="71f70-443">Aktivieren von cors mit Attributen</span><span class="sxs-lookup"><span data-stu-id="71f70-443">Enable CORS with attributes</span></span>

<span data-ttu-id="71f70-444">Das [ &lbrack; enablecors &rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) -Attribut bietet eine Alternative zum globalen Anwenden von cors.</span><span class="sxs-lookup"><span data-stu-id="71f70-444">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="71f70-445">Das- `[EnableCors]` Attribut aktiviert cors für ausgewählte Endpunkte anstelle aller Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="71f70-445">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="71f70-446">Verwenden `[EnableCors]` Sie, um die Standard Richtlinie anzugeben und `[EnableCors("{Policy String}")]` eine Richtlinie anzugeben.</span><span class="sxs-lookup"><span data-stu-id="71f70-446">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="71f70-447">Das- `[EnableCors]` Attribut kann auf Folgendes angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="71f70-447">The `[EnableCors]` attribute can be applied to:</span></span>

* Razor<span data-ttu-id="71f70-448">S`PageModel`</span><span class="sxs-lookup"><span data-stu-id="71f70-448"> Page `PageModel`</span></span>
* <span data-ttu-id="71f70-449">Controller</span><span class="sxs-lookup"><span data-stu-id="71f70-449">Controller</span></span>
* <span data-ttu-id="71f70-450">Controller-Aktionsmethode</span><span class="sxs-lookup"><span data-stu-id="71f70-450">Controller action method</span></span>

<span data-ttu-id="71f70-451">Mit dem-Attribut können Sie verschiedene Richtlinien auf Controller/Seiten Modell/Aktion anwenden `[EnableCors]` .</span><span class="sxs-lookup"><span data-stu-id="71f70-451">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="71f70-452">Wenn das `[EnableCors]` -Attribut auf ein Controller/Seiten Modell/eine Aktionsmethode angewendet wird und cors in der Middleware aktiviert ist, werden **beide** Richtlinien angewendet.</span><span class="sxs-lookup"><span data-stu-id="71f70-452">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="71f70-453">Wir empfehlen, Richtlinien **nicht** zu kombinieren.</span><span class="sxs-lookup"><span data-stu-id="71f70-453">We recommend **not** combining policies.</span></span> <span data-ttu-id="71f70-454">Verwenden Sie das `[EnableCors]` Attribut oder die Middleware, **nicht beides**.</span><span class="sxs-lookup"><span data-stu-id="71f70-454">Use the `[EnableCors]` attribute or middleware, **not both**.</span></span> <span data-ttu-id="71f70-455">Definieren Sie bei Verwendung von `[EnableCors]` **keine** Standard Richtlinie.</span><span class="sxs-lookup"><span data-stu-id="71f70-455">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="71f70-456">Der folgende Code wendet eine andere Richtlinie auf jede Methode an:</span><span class="sxs-lookup"><span data-stu-id="71f70-456">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="71f70-457">Der folgende Code erstellt eine cors-Standard Richtlinie und eine Richtlinie mit dem Namen `"AnotherPolicy"` :</span><span class="sxs-lookup"><span data-stu-id="71f70-457">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="71f70-458">Cors deaktivieren</span><span class="sxs-lookup"><span data-stu-id="71f70-458">Disable CORS</span></span>

<span data-ttu-id="71f70-459">Das [ &lbrack; disablecors &rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) -Attribut deaktiviert cors für Controller/Page-Model/Action.</span><span class="sxs-lookup"><span data-stu-id="71f70-459">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="71f70-460">Cors-Richtlinien Optionen</span><span class="sxs-lookup"><span data-stu-id="71f70-460">CORS policy options</span></span>

<span data-ttu-id="71f70-461">In diesem Abschnitt werden die verschiedenen Optionen beschrieben, die in einer cors-Richtlinie festgelegt werden können:</span><span class="sxs-lookup"><span data-stu-id="71f70-461">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="71f70-462">Festlegen der zulässigen Ursprünge</span><span class="sxs-lookup"><span data-stu-id="71f70-462">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="71f70-463">Festlegen der zulässigen HTTP-Methoden</span><span class="sxs-lookup"><span data-stu-id="71f70-463">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="71f70-464">Festlegen der zulässigen Anforderungs Header</span><span class="sxs-lookup"><span data-stu-id="71f70-464">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="71f70-465">Festlegen der verfügbar gemachten Antwortheader</span><span class="sxs-lookup"><span data-stu-id="71f70-465">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="71f70-466">Anmelde Informationen in Ursprungs übergreifenden Anforderungen</span><span class="sxs-lookup"><span data-stu-id="71f70-466">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="71f70-467">Festlegen der Preflight-Ablaufzeit</span><span class="sxs-lookup"><span data-stu-id="71f70-467">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="71f70-468"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>wird in aufgerufen `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="71f70-468"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="71f70-469">Für einige Optionen kann es hilfreich sein, zuerst den Abschnitt [wie cors Works](#how-cors) zu lesen.</span><span class="sxs-lookup"><span data-stu-id="71f70-469">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="71f70-470">Festlegen der zulässigen Ursprünge</span><span class="sxs-lookup"><span data-stu-id="71f70-470">Set the allowed origins</span></span>

<span data-ttu-id="71f70-471"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Ermöglicht cors-Anforderungen von allen Ursprüngen mit einem beliebigen Schema ( `http` oder `https` ).</span><span class="sxs-lookup"><span data-stu-id="71f70-471"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="71f70-472">`AllowAnyOrigin`ist unsicher, weil *jede Website* Ursprungs übergreifende Anforderungen an die APP stellen kann.</span><span class="sxs-lookup"><span data-stu-id="71f70-472">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="71f70-473">Die Angabe von `AllowAnyOrigin` und `AllowCredentials` ist eine unsichere Konfiguration und kann zu einer Website übergreifenden Anforderungs Fälschung führen.</span><span class="sxs-lookup"><span data-stu-id="71f70-473">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="71f70-474">Geben Sie für eine sichere App eine exakte Liste von Ursprüngen an, wenn sich der Client für den Zugriff auf Server Ressourcen selbst autorisieren muss.</span><span class="sxs-lookup"><span data-stu-id="71f70-474">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="71f70-475">`AllowAnyOrigin`wirkt sich auf Preflight-Anforderungen und den `Access-Control-Allow-Origin` Header aus.</span><span class="sxs-lookup"><span data-stu-id="71f70-475">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="71f70-476">Weitere Informationen finden Sie im Abschnitt [Preflight Requests (Preflight-Anforderungen](#preflight-requests) ).</span><span class="sxs-lookup"><span data-stu-id="71f70-476">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="71f70-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Legt fest, dass die- <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> Eigenschaft der Richtlinie eine Funktion ist, die es den Ursprüngen ermöglicht, eine konfigurierte Platzhalter Domäne zuzuordnen, wenn ausgewertet wird, ob der Ursprung zulässig ist</span><span class="sxs-lookup"><span data-stu-id="71f70-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="71f70-478">Festlegen der zulässigen HTTP-Methoden</span><span class="sxs-lookup"><span data-stu-id="71f70-478">Set the allowed HTTP methods</span></span>

<span data-ttu-id="71f70-479"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="71f70-479"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="71f70-480">Lässt jede HTTP-Methode zu:</span><span class="sxs-lookup"><span data-stu-id="71f70-480">Allows any HTTP method:</span></span>
* <span data-ttu-id="71f70-481">Wirkt sich auf Preflight-Anforderungen und den `Access-Control-Allow-Methods` Header aus.</span><span class="sxs-lookup"><span data-stu-id="71f70-481">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="71f70-482">Weitere Informationen finden Sie im Abschnitt [Preflight Requests (Preflight-Anforderungen](#preflight-requests) ).</span><span class="sxs-lookup"><span data-stu-id="71f70-482">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="71f70-483">Festlegen der zulässigen Anforderungs Header</span><span class="sxs-lookup"><span data-stu-id="71f70-483">Set the allowed request headers</span></span>

<span data-ttu-id="71f70-484">Um zu ermöglichen, dass bestimmte Header in einer cors-Anforderung gesendet werden, die als *Autor Request Headers*bezeichnet wird, müssen Sie aufrufen <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> und die zulässigen Header angeben:</span><span class="sxs-lookup"><span data-stu-id="71f70-484">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="71f70-485">Um alle Autoren Anforderungs Header zuzulassen, geben Sie Folgendes an <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="71f70-485">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="71f70-486">Diese Einstellung wirkt sich auf Preflight-Anforderungen und den- `Access-Control-Request-Headers` Header aus.</span><span class="sxs-lookup"><span data-stu-id="71f70-486">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="71f70-487">Weitere Informationen finden Sie im Abschnitt [Preflight Requests (Preflight-Anforderungen](#preflight-requests) ).</span><span class="sxs-lookup"><span data-stu-id="71f70-487">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="71f70-488">Cors-Middleware erlaubt immer, dass vier Header in der `Access-Control-Request-Headers` gesendet werden, unabhängig von den in corspolicy. Headers konfigurierten Werten.</span><span class="sxs-lookup"><span data-stu-id="71f70-488">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="71f70-489">Diese Liste von Headern umfasst Folgendes:</span><span class="sxs-lookup"><span data-stu-id="71f70-489">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="71f70-490">Nehmen Sie beispielsweise an, dass eine APP wie folgt konfiguriert ist:</span><span class="sxs-lookup"><span data-stu-id="71f70-490">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="71f70-491">Cors-Middleware antwortet erfolgreich auf eine Preflight-Anforderung mit dem folgenden Anforderungs Header, da immer in der `Content-Language` Whitelist aufgeführt ist:</span><span class="sxs-lookup"><span data-stu-id="71f70-491">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="71f70-492">Festlegen der verfügbar gemachten Antwortheader</span><span class="sxs-lookup"><span data-stu-id="71f70-492">Set the exposed response headers</span></span>

<span data-ttu-id="71f70-493">Standardmäßig macht der Browser nicht alle Antwortheader für die app verfügbar.</span><span class="sxs-lookup"><span data-stu-id="71f70-493">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="71f70-494">Weitere Informationen finden Sie unter [W3C Cross-Origin Resource Sharing (Terminologie): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="71f70-494">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="71f70-495">Die standardmäßig verfügbaren Antwortheader lauten wie folgt:</span><span class="sxs-lookup"><span data-stu-id="71f70-495">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="71f70-496">Mit der cors-Spezifikation werden die Header für *einfache Antworten*aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="71f70-496">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="71f70-497">Um andere Header für die app verfügbar zu machen, wenden Sie Folgendes an <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="71f70-497">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="71f70-498">Anmelde Informationen in Ursprungs übergreifenden Anforderungen</span><span class="sxs-lookup"><span data-stu-id="71f70-498">Credentials in cross-origin requests</span></span>

<span data-ttu-id="71f70-499">Anmelde Informationen erfordern eine spezielle Behandlung in einer cors-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="71f70-499">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="71f70-500">Standardmäßig sendet der Browser keine Anmelde Informationen mit einer Ursprungs übergreifenden Anforderung.</span><span class="sxs-lookup"><span data-stu-id="71f70-500">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="71f70-501">Anmelde Informationen umfassen Cookies und http-Authentifizierungs Schemas.</span><span class="sxs-lookup"><span data-stu-id="71f70-501">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="71f70-502">Um Anmelde Informationen mit einer Ursprungs übergreifenden Anforderung zu senden, muss der Client `XMLHttpRequest.withCredentials` auf festlegen `true` .</span><span class="sxs-lookup"><span data-stu-id="71f70-502">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="71f70-503">`XMLHttpRequest`Direkt verwenden:</span><span class="sxs-lookup"><span data-stu-id="71f70-503">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="71f70-504">Verwenden von jQuery:</span><span class="sxs-lookup"><span data-stu-id="71f70-504">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="71f70-505">Verwenden der [Fetch-API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="71f70-505">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="71f70-506">Der Server muss die Anmelde Informationen zulassen.</span><span class="sxs-lookup"><span data-stu-id="71f70-506">The server must allow the credentials.</span></span> <span data-ttu-id="71f70-507">Um Ursprungs übergreifende Anmelde Informationen zuzulassen, geben Sie Folgendes an <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :</span><span class="sxs-lookup"><span data-stu-id="71f70-507">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="71f70-508">Die HTTP-Antwort enthält einen- `Access-Control-Allow-Credentials` Header, der dem Browser mitteilt, dass der Server Anmelde Informationen für eine Ursprungs übergreifende Anforderung zulässt.</span><span class="sxs-lookup"><span data-stu-id="71f70-508">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="71f70-509">Wenn der Browser Anmelde Informationen sendet, die Antwort jedoch keinen gültigen `Access-Control-Allow-Credentials` Header enthält, macht der Browser die Antwort nicht an die app verfügbar, und die Ursprungs übergreifende Anforderung schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="71f70-509">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="71f70-510">Das Zulassen von Ursprungs übergreifenden Anmelde Informationen stellt ein Sicherheitsrisiko dar.</span><span class="sxs-lookup"><span data-stu-id="71f70-510">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="71f70-511">Eine Website in einer anderen Domäne kann die Anmelde Informationen des angemeldeten Benutzers ohne das Wissen des Benutzers an die APP im Auftrag des Benutzers senden.</span><span class="sxs-lookup"><span data-stu-id="71f70-511">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="71f70-512">In der cors-Spezifikation wird auch festgelegt, dass das Festlegen von Ursprüngen auf `"*"` (alle Ursprünge) ungültig ist, wenn der `Access-Control-Allow-Credentials` Header vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="71f70-512">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="71f70-513">Preflight-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="71f70-513">Preflight requests</span></span>

<span data-ttu-id="71f70-514">Bei einigen cors-Anforderungen sendet der Browser eine zusätzliche Anforderung, bevor die tatsächliche Anforderung gesendet wird.</span><span class="sxs-lookup"><span data-stu-id="71f70-514">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="71f70-515">Diese Anforderung wird als *Preflight-Anforderung*bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="71f70-515">This request is called a *preflight request*.</span></span> <span data-ttu-id="71f70-516">Der Browser kann die Preflight-Anforderung überspringen, wenn die folgenden Bedingungen zutreffen:</span><span class="sxs-lookup"><span data-stu-id="71f70-516">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="71f70-517">Die Anforderungs Methode ist Get, Head oder Post.</span><span class="sxs-lookup"><span data-stu-id="71f70-517">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="71f70-518">Von der App werden keine anderen Anforderungs Header als `Accept` , `Accept-Language` , `Content-Language` , `Content-Type` oder `Last-Event-ID` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="71f70-518">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="71f70-519">`Content-Type`Wenn festgelegt, hat der-Header einen der folgenden Werte:</span><span class="sxs-lookup"><span data-stu-id="71f70-519">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="71f70-520">Die Regel für Anforderungs Header, die für die Client Anforderung festgelegt wurde, gilt für Header, die die APP festlegt, indem `setRequestHeader` für das-Objekt aufgerufen wird `XMLHttpRequest` .</span><span class="sxs-lookup"><span data-stu-id="71f70-520">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="71f70-521">In der cors-Spezifikation werden diese Header *Anforderungs Header für Autoren*aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="71f70-521">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="71f70-522">Die Regel gilt nicht für Header, die vom Browser festgelegt werden können, z `User-Agent` `Host` . b., oder `Content-Length` .</span><span class="sxs-lookup"><span data-stu-id="71f70-522">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="71f70-523">Im folgenden finden Sie ein Beispiel für eine Preflight-Anforderung:</span><span class="sxs-lookup"><span data-stu-id="71f70-523">The following is an example of a preflight request:</span></span>

```
OPTIONS https://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: https://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

<span data-ttu-id="71f70-524">Die Pre-Flight-Anforderung verwendet die HTTP OPTIONS-Methode.</span><span class="sxs-lookup"><span data-stu-id="71f70-524">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="71f70-525">Es enthält zwei spezielle Header:</span><span class="sxs-lookup"><span data-stu-id="71f70-525">It includes two special headers:</span></span>

* <span data-ttu-id="71f70-526">`Access-Control-Request-Method`: Die HTTP-Methode, die für die tatsächliche Anforderung verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="71f70-526">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="71f70-527">`Access-Control-Request-Headers`: Eine Liste mit Anforderungs Headern, die von der APP für die tatsächliche Anforderung festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="71f70-527">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="71f70-528">Wie bereits erwähnt, enthält dies keine Header, die vom Browser festgelegt werden, z `User-Agent` . b..</span><span class="sxs-lookup"><span data-stu-id="71f70-528">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="71f70-529">Wenn cors mit der entsprechenden Richtlinie aktiviert ist, antwortet ASP.net Core in der Regel automatisch auf cors-Preflight-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="71f70-529">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="71f70-530">Weitere Informationen finden Sie unter [[httpoptions]-Attribut für Preflight-Anforderungen](#pro).</span><span class="sxs-lookup"><span data-stu-id="71f70-530">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="71f70-531">Eine cors-Preflight-Anforderung kann einen- `Access-Control-Request-Headers` Header enthalten, der dem Server die Header angibt, die mit der tatsächlichen Anforderung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="71f70-531">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="71f70-532">Um bestimmte Header zuzulassen, geben Sie Folgendes an <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="71f70-532">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="71f70-533">Um alle Autoren Anforderungs Header zuzulassen, geben Sie Folgendes an <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="71f70-533">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="71f70-534">Browser sind nicht vollständig konsistent, wenn Sie festgelegt werden `Access-Control-Request-Headers` .</span><span class="sxs-lookup"><span data-stu-id="71f70-534">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="71f70-535">Wenn Sie Header auf einen anderen Wert als festlegen `"*"` (oder verwenden <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> ), sollten Sie mindestens `Accept` , `Content-Type` , und `Origin` sowie alle benutzerdefinierten Header einschließen, die Sie unterstützen möchten.</span><span class="sxs-lookup"><span data-stu-id="71f70-535">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="71f70-536">Im folgenden finden Sie ein Beispiel für eine Antwort auf die Preflight-Anforderung (vorausgesetzt, der Server lässt die Anforderung zu):</span><span class="sxs-lookup"><span data-stu-id="71f70-536">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

<span data-ttu-id="71f70-537">Die Antwort enthält einen `Access-Control-Allow-Methods` Header, der die zulässigen Methoden auflistet, und optional einen `Access-Control-Allow-Headers` Header, der die zulässigen Header auflistet.</span><span class="sxs-lookup"><span data-stu-id="71f70-537">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="71f70-538">Wenn die Preflight-Anforderung erfolgreich ist, sendet der Browser die tatsächliche Anforderung.</span><span class="sxs-lookup"><span data-stu-id="71f70-538">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="71f70-539">Wenn die Preflight-Anforderung verweigert wird, gibt die APP eine *200 OK* -Antwort zurück, sendet jedoch keine cors-Header zurück.</span><span class="sxs-lookup"><span data-stu-id="71f70-539">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="71f70-540">Der Browser versucht daher nicht, die Ursprungs übergreifende Anforderung zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="71f70-540">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="71f70-541">Festlegen der Preflight-Ablaufzeit</span><span class="sxs-lookup"><span data-stu-id="71f70-541">Set the preflight expiration time</span></span>

<span data-ttu-id="71f70-542">Der- `Access-Control-Max-Age` Header gibt an, wie lange die Antwort auf die Preflight-Anforderung zwischengespeichert werden kann.</span><span class="sxs-lookup"><span data-stu-id="71f70-542">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="71f70-543">Um diesen Header festzulegen, wenden Sie Folgendes an <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :</span><span class="sxs-lookup"><span data-stu-id="71f70-543">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="71f70-544">Funktionsweise von cors</span><span class="sxs-lookup"><span data-stu-id="71f70-544">How CORS works</span></span>

<span data-ttu-id="71f70-545">In diesem Abschnitt wird beschrieben, was in einer [cors](https://developer.mozilla.org/docs/Web/HTTP/CORS) -Anforderung auf der Ebene der HTTP-Nachrichten geschieht.</span><span class="sxs-lookup"><span data-stu-id="71f70-545">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="71f70-546">Cors ist **kein** Sicherheits Feature.</span><span class="sxs-lookup"><span data-stu-id="71f70-546">CORS is **not** a security feature.</span></span> <span data-ttu-id="71f70-547">Cors ist ein W3C-Standard, der es einem Server ermöglicht, die Richtlinie für denselben Ursprung zu lockern.</span><span class="sxs-lookup"><span data-stu-id="71f70-547">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="71f70-548">Beispielsweise könnte ein böswilliger Akteur die Verwendung von [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) für Ihre Website verhindern und eine Website übergreifende Anforderung an Ihre cors-aktivierte Website ausführen, um Informationen zu stehlen.</span><span class="sxs-lookup"><span data-stu-id="71f70-548">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="71f70-549">Ihre API ist nicht sicherer, da Sie cors zulässt.</span><span class="sxs-lookup"><span data-stu-id="71f70-549">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="71f70-550">Es liegt an dem Client (Browser), cors zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="71f70-550">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="71f70-551">Der Server führt die Anforderung aus und gibt die Antwort zurück. dabei handelt es sich um den Client, der einen Fehler zurückgibt und die Antwort blockiert.</span><span class="sxs-lookup"><span data-stu-id="71f70-551">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="71f70-552">Eines der folgenden Tools zeigt z. b. die Serverantwort an:</span><span class="sxs-lookup"><span data-stu-id="71f70-552">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="71f70-553">Fiddler</span><span class="sxs-lookup"><span data-stu-id="71f70-553">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="71f70-554">Postman</span><span class="sxs-lookup"><span data-stu-id="71f70-554">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="71f70-555">.Net httpclient</span><span class="sxs-lookup"><span data-stu-id="71f70-555">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="71f70-556">Einen Webbrowser, indem Sie die URL in die Adressleiste eingeben.</span><span class="sxs-lookup"><span data-stu-id="71f70-556">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="71f70-557">Es ist eine Möglichkeit für einen Server, Browser das Ausführen einer Ursprungs übergreifenden [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) -oder [Fetch-API](https://developer.mozilla.org/docs/Web/API/Fetch_API) -Anforderung zu gestatten, die andernfalls unzulässig wäre.</span><span class="sxs-lookup"><span data-stu-id="71f70-557">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="71f70-558">Browser (ohne cors) können keine Ursprungs übergreifenden Anforderungen ausführen.</span><span class="sxs-lookup"><span data-stu-id="71f70-558">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="71f70-559">Vor cors wurde [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) verwendet, um diese Einschränkung zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="71f70-559">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="71f70-560">JSONP verwendet nicht XHR, sondern verwendet das- `<script>` Tag, um die Antwort zu empfangen.</span><span class="sxs-lookup"><span data-stu-id="71f70-560">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="71f70-561">Skripts können Ursprungs übergreifend geladen werden.</span><span class="sxs-lookup"><span data-stu-id="71f70-561">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="71f70-562">In der [cors-Spezifikation](https://www.w3.org/TR/cors/) wurden mehrere neue HTTP-Header eingeführt, die Ursprungs übergreifende Anforderungen ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="71f70-562">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="71f70-563">Wenn ein Browser cors unterstützt, werden diese Header automatisch für Ursprungs übergreifende Anforderungen festgelegt.</span><span class="sxs-lookup"><span data-stu-id="71f70-563">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="71f70-564">Benutzerdefinierter JavaScript-Code ist nicht erforderlich, um cors zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="71f70-564">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="71f70-565">Im folgenden finden Sie ein Beispiel für eine Ursprungs übergreifende Anforderung.</span><span class="sxs-lookup"><span data-stu-id="71f70-565">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="71f70-566">Der `Origin` -Header stellt die Domäne der Site bereit, von der die Anforderung stammt.</span><span class="sxs-lookup"><span data-stu-id="71f70-566">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="71f70-567">Der `Origin` -Header ist erforderlich und muss sich vom Host unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="71f70-567">The `Origin` header is required and must be different from the host.</span></span>

```
GET https://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: https://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: https://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

<span data-ttu-id="71f70-568">Wenn der Server die Anforderung zulässt, wird der- `Access-Control-Allow-Origin` Header in der Antwort festgelegt.</span><span class="sxs-lookup"><span data-stu-id="71f70-568">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="71f70-569">Der Wert dieses Headers stimmt entweder mit dem `Origin` Header aus der Anforderung überein, oder ist der Platzhalter Wert `"*"` , was bedeutet, dass ein beliebiger Ursprung zulässig ist:</span><span class="sxs-lookup"><span data-stu-id="71f70-569">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

<span data-ttu-id="71f70-570">Wenn die Antwort den Header nicht enthält `Access-Control-Allow-Origin` , schlägt die Ursprungs übergreifende Anforderung fehl.</span><span class="sxs-lookup"><span data-stu-id="71f70-570">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="71f70-571">Der Browser lässt die Anforderung insbesondere nicht zu.</span><span class="sxs-lookup"><span data-stu-id="71f70-571">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="71f70-572">Auch wenn der Server eine erfolgreiche Antwort zurückgibt, stellt der Browser die Antwort nicht für die Client-App zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="71f70-572">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="71f70-573">Testen von CORS</span><span class="sxs-lookup"><span data-stu-id="71f70-573">Test CORS</span></span>

<span data-ttu-id="71f70-574">So testen Sie cors:</span><span class="sxs-lookup"><span data-stu-id="71f70-574">To test CORS:</span></span>

1. <span data-ttu-id="71f70-575">[Erstellen Sie ein API-Projekt](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="71f70-575">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="71f70-576">Alternativ können Sie [das Beispiel herunterladen](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="71f70-576">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="71f70-577">Aktivieren Sie cors mithilfe eines der Vorgehensweisen in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="71f70-577">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="71f70-578">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="71f70-578">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="71f70-579">`WithOrigins("https://localhost:<port>");`sollte nur zum Testen einer Beispiel-App verwendet werden, die dem [Download Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)ähnelt.</span><span class="sxs-lookup"><span data-stu-id="71f70-579">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="71f70-580">Erstellen Sie ein Web-App-Projekt ( Razor Pages oder MVC).</span><span class="sxs-lookup"><span data-stu-id="71f70-580">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="71f70-581">Im Beispiel werden Razor Seiten verwendet.</span><span class="sxs-lookup"><span data-stu-id="71f70-581">The sample uses Razor Pages.</span></span> <span data-ttu-id="71f70-582">Sie können die Web-App in der gleichen Projekt Mappe wie das API-Projekt erstellen.</span><span class="sxs-lookup"><span data-stu-id="71f70-582">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="71f70-583">Fügen Sie der Datei " *Index. cshtml* " den folgenden hervorgehobenen Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="71f70-583">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="71f70-584">Ersetzen Sie im vorangehenden Code `url: 'https://<web app>.azurewebsites.net/api/values/1',` durch die URL zur bereitgestellten app.</span><span class="sxs-lookup"><span data-stu-id="71f70-584">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="71f70-585">Stellen Sie das API-Projekt bereit.</span><span class="sxs-lookup"><span data-stu-id="71f70-585">Deploy the API project.</span></span> <span data-ttu-id="71f70-586">Nehmen Sie beispielsweise die Bereitstellung [in Azure vor](xref:host-and-deploy/azure-apps/index).</span><span class="sxs-lookup"><span data-stu-id="71f70-586">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="71f70-587">Führen Razor Sie die Seiten oder die MVC-App über den Desktop aus, und klicken Sie auf die Schaltfläche **Testen** .</span><span class="sxs-lookup"><span data-stu-id="71f70-587">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="71f70-588">Verwenden Sie die F12-Tools, um Fehlermeldungen zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="71f70-588">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="71f70-589">Entfernen Sie den localhost-Ursprung aus, und stellen Sie `WithOrigins` die APP bereit.</span><span class="sxs-lookup"><span data-stu-id="71f70-589">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="71f70-590">Alternativ können Sie die Client-App mit einem anderen Port ausführen.</span><span class="sxs-lookup"><span data-stu-id="71f70-590">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="71f70-591">Führen Sie z. b. in Visual Studio aus.</span><span class="sxs-lookup"><span data-stu-id="71f70-591">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="71f70-592">Testen Sie mit der Client-App.</span><span class="sxs-lookup"><span data-stu-id="71f70-592">Test with the client app.</span></span> <span data-ttu-id="71f70-593">Cors-Fehler geben einen Fehler zurück, aber die Fehlermeldung ist für JavaScript nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="71f70-593">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="71f70-594">Verwenden Sie die Registerkarte Konsole in den F12-Tools, um den Fehler anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="71f70-594">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="71f70-595">Abhängig vom Browser erhalten Sie eine Fehlermeldung (in der F12-toolkonsole) ähnlich der folgenden:</span><span class="sxs-lookup"><span data-stu-id="71f70-595">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="71f70-596">Verwenden von Microsoft Edge:</span><span class="sxs-lookup"><span data-stu-id="71f70-596">Using Microsoft Edge:</span></span>

     <span data-ttu-id="71f70-597">**SEC7120: [cors] der Ursprung `https://localhost:44375` wurde `https://localhost:44375` im Antwortheader "Access-Control-Allow-Origin" für die Ursprungs übergreifende Ressource nicht gefunden in`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="71f70-597">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="71f70-598">Verwenden von Chrome:</span><span class="sxs-lookup"><span data-stu-id="71f70-598">Using Chrome:</span></span>

     <span data-ttu-id="71f70-599">**Der Zugriff auf XMLHttpRequest auf `https://webapi.azurewebsites.net/api/values/1` der Ursprungsseite wurde `https://localhost:44375` durch die cors-Richtlinie blockiert: für die angeforderte Ressource ist kein "Access-Control-Allow-Origin"-Header vorhanden.**</span><span class="sxs-lookup"><span data-stu-id="71f70-599">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="71f70-600">Cors-aktivierte Endpunkte können mit einem Tool wie z. b. " [fddler](https://www.telerik.com/fiddler) " oder [Postman](https://www.getpostman.com/)getestet werden.</span><span class="sxs-lookup"><span data-stu-id="71f70-600">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="71f70-601">Wenn Sie ein Tool verwenden, muss sich der Ursprung der durch den-Header angegebenen Anforderung `Origin` von dem Host unterscheiden, der die Anforderung empfängt.</span><span class="sxs-lookup"><span data-stu-id="71f70-601">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="71f70-602">Wenn die Anforderung aufgrund des Werts des Headers nicht *Kreuz Ursprungs* ist `Origin` :</span><span class="sxs-lookup"><span data-stu-id="71f70-602">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="71f70-603">Es ist nicht erforderlich, dass cors-Middleware die Anforderung verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="71f70-603">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="71f70-604">Cors-Header werden in der Antwort nicht zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="71f70-604">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="71f70-605">Cors in IIS</span><span class="sxs-lookup"><span data-stu-id="71f70-605">CORS in IIS</span></span>

<span data-ttu-id="71f70-606">Beim Bereitstellen in IIS muss cors vor der Windows-Authentifizierung ausgeführt werden, wenn der Server nicht für den anonymen Zugriff konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="71f70-606">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="71f70-607">Zur Unterstützung dieses Szenarios muss das [IIS cors-Modul](https://www.iis.net/downloads/microsoft/iis-cors-module) für die APP installiert und konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="71f70-607">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="71f70-608">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="71f70-608">Additional resources</span></span>

* [<span data-ttu-id="71f70-609">Cross-Origin Resource Sharing (cors)</span><span class="sxs-lookup"><span data-stu-id="71f70-609">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="71f70-610">Einführung in das IIS cors-Modul</span><span class="sxs-lookup"><span data-stu-id="71f70-610">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

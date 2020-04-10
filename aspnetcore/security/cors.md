---
title: Aktivieren von Cross-Origin-Anforderungen (CROSS-Origin Requests, CORS) in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie CORS als Standard ursprungsübergreifende Anforderungen in einer ASP.NET Core-App zugelassen oder abgelehnt werden kann.
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
uid: security/cors
ms.openlocfilehash: 601e26e1990a86ad60aa50c8c93ffa490ff6b708
ms.sourcegitcommit: e72a58d6ebde8604badd254daae8077628f9d63e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81007183"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="cccab-103">Aktivieren von Cross-Origin-Anforderungen (CROSS-Origin Requests, CORS) in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cccab-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cccab-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und Kirk [Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="cccab-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="cccab-105">Dieser Artikel zeigt, wie CORS in einer ASP.NET Core-App aktiviert wird.</span><span class="sxs-lookup"><span data-stu-id="cccab-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="cccab-106">Die Browsersicherheit verhindert, dass eine Webseite Anforderungen an eine andere Domäne als die, die die Webseite bereitgestellt hat, stellt.</span><span class="sxs-lookup"><span data-stu-id="cccab-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="cccab-107">Diese Einschränkung wird als *Richtlinie mit gleichem Ursprung*bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="cccab-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="cccab-108">Die Richtlinie mit demselben Ursprung verhindert, dass eine böswillige Website vertrauliche Daten von einer anderen Website liest.</span><span class="sxs-lookup"><span data-stu-id="cccab-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="cccab-109">Manchmal möchten Sie möglicherweise zulassen, dass andere Websites ursprungsübergreifende Anforderungen an Ihre App stellen.</span><span class="sxs-lookup"><span data-stu-id="cccab-109">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="cccab-110">Weitere Informationen finden Sie im [Mozilla CORS-Artikel](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="cccab-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="cccab-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="cccab-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="cccab-112">Ein W3C-Standard, der es einem Server ermöglicht, die Richtlinie mit demselben Ursprung zu entspannen.</span><span class="sxs-lookup"><span data-stu-id="cccab-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="cccab-113">Ist **kein** Sicherheitsmerkmal, CORS entspannt die Sicherheit.</span><span class="sxs-lookup"><span data-stu-id="cccab-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="cccab-114">Eine API ist nicht sicherer, wenn CORS zugelassen wird.</span><span class="sxs-lookup"><span data-stu-id="cccab-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="cccab-115">Weitere Informationen finden Sie unter Funktionsweise von [CORS](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="cccab-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="cccab-116">Ermöglicht einem Server, einige ursprungsübergreifende Anforderungen explizit zuzulassen, während andere abgelehnt werden.</span><span class="sxs-lookup"><span data-stu-id="cccab-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="cccab-117">Ist sicherer und flexibler als frühere Techniken, wie z. B. [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="cccab-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="cccab-118">[Beispielcode anzeigen oder herunterladen](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([downloaden](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cccab-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="cccab-119">Gleicher Ursprung</span><span class="sxs-lookup"><span data-stu-id="cccab-119">Same origin</span></span>

<span data-ttu-id="cccab-120">Zwei URLs haben denselben Ursprung, wenn sie identische Schemata, Hosts und Ports haben ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="cccab-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="cccab-121">Diese beiden URLs haben den gleichen Ursprung:</span><span class="sxs-lookup"><span data-stu-id="cccab-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="cccab-122">Diese URLs haben andere Ursprünge als die beiden vorherigen URLs:</span><span class="sxs-lookup"><span data-stu-id="cccab-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="cccab-123">`https://example.net`&ndash; Verschiedene Domäne</span><span class="sxs-lookup"><span data-stu-id="cccab-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="cccab-124">`https://www.example.com/foo.html`&ndash; Verschiedene Subdomain</span><span class="sxs-lookup"><span data-stu-id="cccab-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="cccab-125">`http://example.com/foo.html`&ndash; Unterschiedlicheregelung</span><span class="sxs-lookup"><span data-stu-id="cccab-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="cccab-126">`https://example.com:9000/foo.html`&ndash; Verschiedene randder Hafen</span><span class="sxs-lookup"><span data-stu-id="cccab-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="cccab-127">Aktivieren von CORS</span><span class="sxs-lookup"><span data-stu-id="cccab-127">Enable CORS</span></span>

<span data-ttu-id="cccab-128">Es gibt drei Möglichkeiten, CORS zu aktivieren:</span><span class="sxs-lookup"><span data-stu-id="cccab-128">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="cccab-129">In Middleware mit einer [benannten Richtlinie](#np) oder [Standardrichtlinie](#dp).</span><span class="sxs-lookup"><span data-stu-id="cccab-129">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="cccab-130">Verwenden von [Endpunktrouting](#ecors).</span><span class="sxs-lookup"><span data-stu-id="cccab-130">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="cccab-131">Mit dem [Attribut [EnableCors].](#attr)</span><span class="sxs-lookup"><span data-stu-id="cccab-131">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="cccab-132">Die Verwendung des [[EnableCors]-Attributs](#attr) mit einer benannten Richtlinie bietet die beste Kontrolle beim Einschränken von Endpunkten, die CORS unterstützen.</span><span class="sxs-lookup"><span data-stu-id="cccab-132">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

<span data-ttu-id="cccab-133">Jeder Ansatz wird in den folgenden Abschnitten ausführlich beschrieben.</span><span class="sxs-lookup"><span data-stu-id="cccab-133">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="cccab-134">CORS mit benannter Richtlinie und Middleware</span><span class="sxs-lookup"><span data-stu-id="cccab-134">CORS with named policy and middleware</span></span>

<span data-ttu-id="cccab-135">CORS Middleware verarbeitet ursprungsübergreifende Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="cccab-135">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="cccab-136">Der folgende Code wendet eine CORS-Richtlinie auf alle Endpunkte der App mit den angegebenen Ursprüngen an:</span><span class="sxs-lookup"><span data-stu-id="cccab-136">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

<span data-ttu-id="cccab-137">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="cccab-137">The preceding code:</span></span>

* <span data-ttu-id="cccab-138">Legt den Richtliniennamen auf fest. `_myAllowSpecificOrigins`</span><span class="sxs-lookup"><span data-stu-id="cccab-138">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="cccab-139">Der Richtlinienname ist willkürlich.</span><span class="sxs-lookup"><span data-stu-id="cccab-139">The policy name is arbitrary.</span></span>
* <span data-ttu-id="cccab-140">Ruft <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> die Erweiterungsmethode `_myAllowSpecificOrigins` auf und gibt die CORS-Richtlinie an.</span><span class="sxs-lookup"><span data-stu-id="cccab-140">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="cccab-141">`UseCors`fügt die CORS-Middleware hinzu.</span><span class="sxs-lookup"><span data-stu-id="cccab-141">`UseCors` adds the CORS middleware.</span></span>
* <span data-ttu-id="cccab-142">Aufrufe <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> mit einem [Lambda-Ausdruck](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="cccab-142">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="cccab-143">Der Lambda <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> nimmt ein Objekt.</span><span class="sxs-lookup"><span data-stu-id="cccab-143">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="cccab-144">[Konfigurationsoptionen](#cors-policy-options), `WithOrigins`z. B. , werden weiter unten in diesem Artikel beschrieben.</span><span class="sxs-lookup"><span data-stu-id="cccab-144">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="cccab-145">Aktiviert `_myAllowSpecificOrigins` die CORS-Richtlinie für alle Controllerendpunkte.</span><span class="sxs-lookup"><span data-stu-id="cccab-145">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="cccab-146">Siehe [Endpunktrouting,](#ecors) um eine CORS-Richtlinie auf bestimmte Endpunkte anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="cccab-146">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>

<span data-ttu-id="cccab-147">Beim Endpunktrouting ***muss*** die CORS-Middleware so konfiguriert `UseRouting` werden, dass sie zwischen den Aufrufen von und `UseEndpoints`ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="cccab-147">With endpoint routing, the CORS middleware ***must*** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="cccab-148">Anweisungen zum Testen von Code ähnlich dem vorherigen Code finden Sie unter [Test-CORS.](#testc)</span><span class="sxs-lookup"><span data-stu-id="cccab-148">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="cccab-149">Der <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> Methodenaufruf fügt DEM Dienstcontainer der App CORS-Dienste hinzu:</span><span class="sxs-lookup"><span data-stu-id="cccab-149">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="cccab-150">Weitere Informationen finden Sie unter [CORS-Richtlinienoptionen](#cpo) in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="cccab-150">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="cccab-151">Die <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> Methoden können verkettet werden, wie im folgenden Code gezeigt:</span><span class="sxs-lookup"><span data-stu-id="cccab-151">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="cccab-152">Hinweis: Die angegebene URL darf **keinen** nachgestellten Schrägstrich (`/`) enthalten.</span><span class="sxs-lookup"><span data-stu-id="cccab-152">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="cccab-153">Wenn die URL `/`mit beendet `false` wird, wird der Vergleich zurückgegeben, und es wird kein Header zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="cccab-153">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="cccab-154">CORS mit Standardrichtlinie und Middleware</span><span class="sxs-lookup"><span data-stu-id="cccab-154">CORS with default policy and middleware</span></span>

<span data-ttu-id="cccab-155">Der folgende hervorgehobene Code aktiviert die Standardmäßige CORS-Richtlinie:</span><span class="sxs-lookup"><span data-stu-id="cccab-155">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="cccab-156">Der vorherige Code wendet die Standard-CORS-Richtlinie auf alle Controllerendpunkte an.</span><span class="sxs-lookup"><span data-stu-id="cccab-156">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="cccab-157">Aktivieren von Cors mit Endpunktrouting</span><span class="sxs-lookup"><span data-stu-id="cccab-157">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="cccab-158">Das Aktivieren von CORS pro `RequireCors` Endpunkt mithilfe der aktuellen Verwendung unterstützt ***keine*** [automatischen Preflight-Anforderungen](#apf).</span><span class="sxs-lookup"><span data-stu-id="cccab-158">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="cccab-159">Weitere Informationen finden Sie unter [dieses GitHub-Problem](https://github.com/dotnet/aspnetcore/issues/20709) und Testen von [CORS mit Endpunktrouting und [HttpOptions]](#tcer).</span><span class="sxs-lookup"><span data-stu-id="cccab-159">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="cccab-160">Mit Endpunktrouting kann CORS pro Endpunkt mithilfe der <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> Erweiterungsmethoden aktiviert werden:</span><span class="sxs-lookup"><span data-stu-id="cccab-160">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,41,44)]

<span data-ttu-id="cccab-161">Für den Code oben gilt:</span><span class="sxs-lookup"><span data-stu-id="cccab-161">In the preceding code:</span></span>

* <span data-ttu-id="cccab-162">`app.UseCors`ermöglicht die CORS Middleware.</span><span class="sxs-lookup"><span data-stu-id="cccab-162">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="cccab-163">Da eine Standardrichtlinie nicht konfiguriert wurde, `app.UseCors()` aktiviert ALLEIN CORS nicht.</span><span class="sxs-lookup"><span data-stu-id="cccab-163">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="cccab-164">Die `/echo` und controller-Endpunkte erlauben ursprungsübergreifende Anforderungen mithilfe der angegebenen Richtlinie.</span><span class="sxs-lookup"><span data-stu-id="cccab-164">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="cccab-165">Die `/echo2` und Razor Pages-Endpunkte lassen ***keine*** ursprungsübergreifenden Anforderungen zu, da keine Standardrichtlinie angegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="cccab-165">The `/echo2` and Razor Pages endpoints do ***not*** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="cccab-166">Das [Attribut [DisableCors]](#dc) deaktiviert CORS ***nicht,*** das durch `RequireCors`Endpunktrouting mit aktiviert wurde.</span><span class="sxs-lookup"><span data-stu-id="cccab-166">The [[DisableCors]](#dc) attribute does ***not***  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="cccab-167">Anweisungen zum Testen von Code ähnlich dem vorherigen finden Sie unter [Test-CORS mit Endpunktrouting und [HttpOptions].](#tcer)</span><span class="sxs-lookup"><span data-stu-id="cccab-167">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="cccab-168">CORS mit Attributen aktivieren</span><span class="sxs-lookup"><span data-stu-id="cccab-168">Enable CORS with attributes</span></span>

<span data-ttu-id="cccab-169">Das Aktivieren von CORS mit dem [Attribut [EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) und das Anwenden einer benannten Richtlinie auf die Endpunkte, die CORS erfordern, bietet die beste Kontrolle.</span><span class="sxs-lookup"><span data-stu-id="cccab-169">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="cccab-170">Das [Attribut [EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) bietet eine Alternative zum globalen Anwenden von CORS.</span><span class="sxs-lookup"><span data-stu-id="cccab-170">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="cccab-171">Das `[EnableCors]` Attribut aktiviert CORS für ausgewählte Endpunkte und nicht für alle Endpunkte:</span><span class="sxs-lookup"><span data-stu-id="cccab-171">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="cccab-172">`[EnableCors]`gibt die Standardrichtlinie an.</span><span class="sxs-lookup"><span data-stu-id="cccab-172">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="cccab-173">`[EnableCors("{Policy String}")]`gibt eine benannte Richtlinie an.</span><span class="sxs-lookup"><span data-stu-id="cccab-173">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="cccab-174">Das `[EnableCors]` Attribut kann angewendet werden auf:</span><span class="sxs-lookup"><span data-stu-id="cccab-174">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="cccab-175">Razor Page`PageModel`</span><span class="sxs-lookup"><span data-stu-id="cccab-175">Razor Page `PageModel`</span></span>
* <span data-ttu-id="cccab-176">Controller</span><span class="sxs-lookup"><span data-stu-id="cccab-176">Controller</span></span>
* <span data-ttu-id="cccab-177">Controller-Aktionsmethode</span><span class="sxs-lookup"><span data-stu-id="cccab-177">Controller action method</span></span>

<span data-ttu-id="cccab-178">Verschiedene Richtlinien können auf Controller, Seitenmodelle oder `[EnableCors]` Aktionsmethoden mit dem Attribut angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="cccab-178">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="cccab-179">Wenn `[EnableCors]` das Attribut auf einen Controller, ein Seitenmodell oder eine Aktionsmethode angewendet wird und CORS in Middleware aktiviert ist, werden ***beide*** Richtlinien angewendet.</span><span class="sxs-lookup"><span data-stu-id="cccab-179">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="cccab-180">***Wir empfehlen, Richtlinien nicht zu kombinieren. Verwenden Sie das*** `[EnableCors]` Attribut oder die ***Middleware, nicht beide in derselben App.***</span><span class="sxs-lookup"><span data-stu-id="cccab-180">***We recommend against combining policies. Use the*** `[EnableCors]` ***attribute or middleware, not both in the same app.***</span></span>

<span data-ttu-id="cccab-181">Der folgende Code wendet für jede Methode eine andere Richtlinie an:</span><span class="sxs-lookup"><span data-stu-id="cccab-181">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="cccab-182">Der folgende Code erstellt zwei CORS-Richtlinien:</span><span class="sxs-lookup"><span data-stu-id="cccab-182">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="cccab-183">Für die beste Kontrolle der Begrenzung von CORS-Anforderungen:</span><span class="sxs-lookup"><span data-stu-id="cccab-183">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="cccab-184">Wird `[EnableCors("MyPolicy")]` mit einer benannten Richtlinie verwendet.</span><span class="sxs-lookup"><span data-stu-id="cccab-184">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="cccab-185">Definieren Sie keine Standardrichtlinie.</span><span class="sxs-lookup"><span data-stu-id="cccab-185">Don't define a default policy.</span></span>
* <span data-ttu-id="cccab-186">Verwenden Sie kein [Endpunktrouting](#ecors).</span><span class="sxs-lookup"><span data-stu-id="cccab-186">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="cccab-187">Der Code im nächsten Abschnitt entspricht der vorherigen Liste.</span><span class="sxs-lookup"><span data-stu-id="cccab-187">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="cccab-188">Anweisungen zum Testen von Code ähnlich dem vorherigen Code finden Sie unter [Test-CORS.](#testc)</span><span class="sxs-lookup"><span data-stu-id="cccab-188">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="cccab-189">DEAKTIVIEREN von CORS</span><span class="sxs-lookup"><span data-stu-id="cccab-189">Disable CORS</span></span>

<span data-ttu-id="cccab-190">Das [Attribut [DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) deaktiviert CORS ***nicht,*** das durch [Endpunktrouting](#ecors)aktiviert wurde.</span><span class="sxs-lookup"><span data-stu-id="cccab-190">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does ***not***  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="cccab-191">Der folgende Code definiert die `"MyPolicy"`CORS-Richtlinie:</span><span class="sxs-lookup"><span data-stu-id="cccab-191">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="cccab-192">Der folgende Code deaktiviert CORS für die `GetValues2` Aktion:</span><span class="sxs-lookup"><span data-stu-id="cccab-192">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="cccab-193">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="cccab-193">The preceding code:</span></span>

* <span data-ttu-id="cccab-194">Aktiviert CORS nicht mit [Endpunktrouting](#ecors).</span><span class="sxs-lookup"><span data-stu-id="cccab-194">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="cccab-195">Definiert keine [CORS-Standardrichtlinie](#dp).</span><span class="sxs-lookup"><span data-stu-id="cccab-195">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="cccab-196">Verwendet [[EnableCors("MyPolicy")],](#attr) `"MyPolicy"` um die CORS-Richtlinie für den Controller zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="cccab-196">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="cccab-197">Deaktiviert CORS für `GetValues2` die Methode.</span><span class="sxs-lookup"><span data-stu-id="cccab-197">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="cccab-198">Anweisungen zum Testen des vorherigen Codes finden Sie unter [Test-CORS.](#testc)</span><span class="sxs-lookup"><span data-stu-id="cccab-198">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="cccab-199">CORS-Richtlinienoptionen</span><span class="sxs-lookup"><span data-stu-id="cccab-199">CORS policy options</span></span>

<span data-ttu-id="cccab-200">In diesem Abschnitt werden die verschiedenen Optionen beschrieben, die in einer CORS-Richtlinie festgelegt werden können:</span><span class="sxs-lookup"><span data-stu-id="cccab-200">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="cccab-201">Festlegen der zulässigen Ursprünge</span><span class="sxs-lookup"><span data-stu-id="cccab-201">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="cccab-202">Festlegen der zulässigen HTTP-Methoden</span><span class="sxs-lookup"><span data-stu-id="cccab-202">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="cccab-203">Festlegen der zulässigen Anforderungsheader</span><span class="sxs-lookup"><span data-stu-id="cccab-203">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="cccab-204">Festlegen der freiliegenden Antwortheader</span><span class="sxs-lookup"><span data-stu-id="cccab-204">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="cccab-205">Anmeldeinformationen in ursprungsübergreifenden Anforderungen</span><span class="sxs-lookup"><span data-stu-id="cccab-205">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="cccab-206">Festlegen der Ablaufzeit vor dem Flug</span><span class="sxs-lookup"><span data-stu-id="cccab-206">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="cccab-207"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>wird in `Startup.ConfigureServices`aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="cccab-207"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="cccab-208">Bei einigen Optionen kann es hilfreich sein, zuerst den Abschnitt ["Funktionsweise von CORS"](#how-cors) zu lesen.</span><span class="sxs-lookup"><span data-stu-id="cccab-208">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="cccab-209">Festlegen der zulässigen Ursprünge</span><span class="sxs-lookup"><span data-stu-id="cccab-209">Set the allowed origins</span></span>

<span data-ttu-id="cccab-210"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Ermöglicht CORS-Anforderungen aller Herkunftsherkunft mit einem beliebigen Schema (`http` oder `https`).</span><span class="sxs-lookup"><span data-stu-id="cccab-210"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="cccab-211">`AllowAnyOrigin`ist unsicher, da *jede Website* ursprungsübergreifende Anforderungen an die App stellen kann.</span><span class="sxs-lookup"><span data-stu-id="cccab-211">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="cccab-212">Angeben `AllowAnyOrigin` und `AllowCredentials` ist eine unsichere Konfiguration und kann zu standortübergreifenden Anforderungsfälschungen führen.</span><span class="sxs-lookup"><span data-stu-id="cccab-212">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="cccab-213">Der CORS-Dienst gibt eine ungültige CORS-Antwort zurück, wenn eine App mit beiden Methoden konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="cccab-213">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="cccab-214">`AllowAnyOrigin`wirkt sich auf `Access-Control-Allow-Origin` Preflight-Anforderungen und den Header aus.</span><span class="sxs-lookup"><span data-stu-id="cccab-214">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="cccab-215">Weitere Informationen finden Sie im Abschnitt [Preflight-Anforderungen.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="cccab-215">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="cccab-216"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Legt <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> die Eigenschaft der Richtlinie als funktion fest, die es dem Ursprung ermöglicht, eine konfigurierte Platzhalterdomäne abzugleichen, wenn ausgewertet wird, ob der Ursprung zulässig ist.</span><span class="sxs-lookup"><span data-stu-id="cccab-216"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="cccab-217">Festlegen der zulässigen HTTP-Methoden</span><span class="sxs-lookup"><span data-stu-id="cccab-217">Set the allowed HTTP methods</span></span>

<span data-ttu-id="cccab-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="cccab-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="cccab-219">Erlaubt jede HTTP-Methode:</span><span class="sxs-lookup"><span data-stu-id="cccab-219">Allows any HTTP method:</span></span>
* <span data-ttu-id="cccab-220">Beeinflusst Preflight-Anforderungen `Access-Control-Allow-Methods` und den Header.</span><span class="sxs-lookup"><span data-stu-id="cccab-220">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="cccab-221">Weitere Informationen finden Sie im Abschnitt [Preflight-Anforderungen.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="cccab-221">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="cccab-222">Festlegen der zulässigen Anforderungsheader</span><span class="sxs-lookup"><span data-stu-id="cccab-222">Set the allowed request headers</span></span>

<span data-ttu-id="cccab-223">Um das Senden bestimmter Header in einer CORS-Anforderung zuzulassen, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> rufen Sie die [zulässigen](https://xhr.spec.whatwg.org/#request)Header an, um das Senden bestimmter Header in einer CORS-Anforderung zu ermöglichen:</span><span class="sxs-lookup"><span data-stu-id="cccab-223">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="cccab-224">Um alle [Autorenanforderungsheader](https://www.w3.org/TR/cors/#author-request-headers) <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>zuzulassen, rufen Sie :</span><span class="sxs-lookup"><span data-stu-id="cccab-224">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="cccab-225">`AllowAnyHeader`wirkt sich auf Preflight-Anforderungen und den [Access-Control-Request-Headers-Header aus.](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method)</span><span class="sxs-lookup"><span data-stu-id="cccab-225">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="cccab-226">Weitere Informationen finden Sie im Abschnitt [Preflight-Anforderungen.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="cccab-226">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="cccab-227">Eine CORS Middleware-Richtlinie, die `WithHeaders` mit bestimmten Headern übereinstimmt, `Access-Control-Request-Headers` die von angegeben `WithHeaders`werden, ist nur möglich, wenn die in .</span><span class="sxs-lookup"><span data-stu-id="cccab-227">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="cccab-228">Betrachten Sie beispielsweise eine App, die wie folgt konfiguriert ist:</span><span class="sxs-lookup"><span data-stu-id="cccab-228">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="cccab-229">CORS Middleware lehnt eine Preflight-Anforderung mit `Content-Language` dem folgenden Anforderungsheader ab, da `WithHeaders`([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) nicht in aufgeführt ist:</span><span class="sxs-lookup"><span data-stu-id="cccab-229">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="cccab-230">Die App gibt eine *200 OK-Antwort* zurück, sendet die CORS-Header jedoch nicht zurück.</span><span class="sxs-lookup"><span data-stu-id="cccab-230">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="cccab-231">Daher versucht der Browser nicht, die ursprungsübergreifende Anforderung zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="cccab-231">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="cccab-232">Festlegen der freiliegenden Antwortheader</span><span class="sxs-lookup"><span data-stu-id="cccab-232">Set the exposed response headers</span></span>

<span data-ttu-id="cccab-233">Standardmäßig macht der Browser nicht alle Antwortheader für die App verfügbar.</span><span class="sxs-lookup"><span data-stu-id="cccab-233">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="cccab-234">Weitere Informationen finden Sie unter [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="cccab-234">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="cccab-235">Die standardmäßig verfügbaren Antwortheader sind:</span><span class="sxs-lookup"><span data-stu-id="cccab-235">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="cccab-236">Die CORS-Spezifikation ruft diese Header *auf einfache Antwortheader*.</span><span class="sxs-lookup"><span data-stu-id="cccab-236">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="cccab-237">Um andere Header für die App <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>verfügbar zu machen, rufen Sie :</span><span class="sxs-lookup"><span data-stu-id="cccab-237">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="cccab-238">Anmeldeinformationen in ursprungsübergreifenden Anforderungen</span><span class="sxs-lookup"><span data-stu-id="cccab-238">Credentials in cross-origin requests</span></span>

<span data-ttu-id="cccab-239">Anmeldeinformationen erfordern eine spezielle Behandlung in einer CORS-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="cccab-239">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="cccab-240">Standardmäßig sendet der Browser keine Anmeldeinformationen mit einer ursprungsübergreifenden Anforderung.</span><span class="sxs-lookup"><span data-stu-id="cccab-240">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="cccab-241">Anmeldeinformationen umfassen Cookies und HTTP-Authentifizierungsschemata.</span><span class="sxs-lookup"><span data-stu-id="cccab-241">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="cccab-242">Um Anmeldeinformationen mit einer ursprungsübergreifenden Anforderung `XMLHttpRequest.withCredentials` zu `true`senden, muss der Client auf festlegen.</span><span class="sxs-lookup"><span data-stu-id="cccab-242">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="cccab-243">Direkt `XMLHttpRequest` verwenden:</span><span class="sxs-lookup"><span data-stu-id="cccab-243">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="cccab-244">Verwenden von jQuery:</span><span class="sxs-lookup"><span data-stu-id="cccab-244">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="cccab-245">Verwenden der [Abruf-API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="cccab-245">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="cccab-246">Der Server muss die Anmeldeinformationen zulassen.</span><span class="sxs-lookup"><span data-stu-id="cccab-246">The server must allow the credentials.</span></span> <span data-ttu-id="cccab-247">Um ursprungsübergreifende Anmeldeinformationen zuzulassen, rufen Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span><span class="sxs-lookup"><span data-stu-id="cccab-247">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="cccab-248">Die HTTP-Antwort `Access-Control-Allow-Credentials` enthält einen Header, der dem Browser mitteilt, dass der Server Anmeldeinformationen für eine ursprungsübergreifende Anforderung zulässt.</span><span class="sxs-lookup"><span data-stu-id="cccab-248">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="cccab-249">Wenn der Browser Anmeldeinformationen sendet, die Antwort `Access-Control-Allow-Credentials` jedoch keinen gültigen Header enthält, macht der Browser die Antwort für die App nicht verfügbar, und die ursprungsübergreifende Anforderung schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="cccab-249">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="cccab-250">Das Zulassen ursprungsübergreifender Anmeldeinformationen stellt ein Sicherheitsrisiko dar.</span><span class="sxs-lookup"><span data-stu-id="cccab-250">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="cccab-251">Eine Website in einer anderen Domäne kann die Anmeldeinformationen eines angemeldeten Benutzers im Namen des Benutzers ohne Wissen des Benutzers an die App senden.</span><span class="sxs-lookup"><span data-stu-id="cccab-251">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="cccab-252">Die CORS-Spezifikation gibt auch `"*"` an, dass das Festlegen `Access-Control-Allow-Credentials` von Ursprüngen auf (alle Ursprünge) ungültig ist, wenn der Header vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="cccab-252">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="cccab-253">Preflight-Anfragen</span><span class="sxs-lookup"><span data-stu-id="cccab-253">Preflight requests</span></span>

<span data-ttu-id="cccab-254">Bei einigen CORS-Anforderungen sendet [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) der Browser eine zusätzliche OPTIONS-Anforderung, bevor er die eigentliche Anforderung stellt.</span><span class="sxs-lookup"><span data-stu-id="cccab-254">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="cccab-255">Diese Anforderung wird als [Preflight-Anforderung](https://developer.mozilla.org/docs/Glossary/Preflight_request)bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="cccab-255">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="cccab-256">Der Browser kann die Preflight-Anforderung überspringen, wenn ***alle*** folgenden Bedingungen erfüllt sind:</span><span class="sxs-lookup"><span data-stu-id="cccab-256">The browser can skip the preflight request if ***all*** the following conditions are true:</span></span>

* <span data-ttu-id="cccab-257">Die Anforderungsmethode ist GET, HEAD oder POST.</span><span class="sxs-lookup"><span data-stu-id="cccab-257">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="cccab-258">Die App stellt keine anderen Anforderungsheader `Content-Language` `Content-Type`als `Accept`, `Accept-Language`, , oder `Last-Event-ID`fest.</span><span class="sxs-lookup"><span data-stu-id="cccab-258">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="cccab-259">Der `Content-Type` Header, sofern festgelegt, hat einen der folgenden Werte:</span><span class="sxs-lookup"><span data-stu-id="cccab-259">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="cccab-260">Die Regel für Anforderungsheader, die für die Clientanforderung festgelegt `setRequestHeader` wurden, gilt für Header, die die App durch Aufrufen des `XMLHttpRequest` Objekts festlegt.</span><span class="sxs-lookup"><span data-stu-id="cccab-260">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="cccab-261">Die CORS-Spezifikation ruft diese [Header-Author-Anforderungsheader](https://www.w3.org/TR/cors/#author-request-headers)auf.</span><span class="sxs-lookup"><span data-stu-id="cccab-261">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="cccab-262">Die Regel gilt nicht für Header, die der `User-Agent` `Host`Browser `Content-Length`festlegen kann, z. B. , oder .</span><span class="sxs-lookup"><span data-stu-id="cccab-262">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="cccab-263">Im Folgenden finden Sie eine Beispielantwort, die der Preflight-Anforderung ähnelt, die von der Schaltfläche **[Test test]** im Abschnitt [Test CORS](#testc) dieses Dokuments gestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="cccab-263">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

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

<span data-ttu-id="cccab-264">Die Preflight-Anforderung verwendet die [HTTP OPTIONS-Methode.](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS)</span><span class="sxs-lookup"><span data-stu-id="cccab-264">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="cccab-265">Es kann die folgenden Header enthalten:</span><span class="sxs-lookup"><span data-stu-id="cccab-265">It may include the following headers:</span></span>

* <span data-ttu-id="cccab-266">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): Die HTTP-Methode, die für die eigentliche Anforderung verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="cccab-266">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="cccab-267">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): Eine Liste von Anforderungsheadern, die die App für die eigentliche Anforderung festlegt.</span><span class="sxs-lookup"><span data-stu-id="cccab-267">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="cccab-268">Wie bereits erwähnt, enthält dies keine Header, die `User-Agent`der Browser festlegt, z. B. .</span><span class="sxs-lookup"><span data-stu-id="cccab-268">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="cccab-269">Access-Control-Allow-Methoden</span><span class="sxs-lookup"><span data-stu-id="cccab-269">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="cccab-270">Wenn die Preflight-Anforderung abgelehnt wird, gibt die App eine `200 OK` Antwort zurück, legt jedoch die CORS-Header nicht fest.</span><span class="sxs-lookup"><span data-stu-id="cccab-270">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="cccab-271">Daher versucht der Browser nicht, die ursprungsübergreifende Anforderung zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="cccab-271">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="cccab-272">Ein Beispiel für eine abgelehnte Preflight-Anforderung finden Sie im Abschnitt [TEST CORS](#testc) dieses Dokuments.</span><span class="sxs-lookup"><span data-stu-id="cccab-272">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="cccab-273">Mit den F12-Tools zeigt die Konsolen-App je nach Browser einen ähnlichen Fehler wie einen der folgenden Optionen an:</span><span class="sxs-lookup"><span data-stu-id="cccab-273">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="cccab-274">Firefox: Cross-Origin-Anforderung blockiert: Die Richtlinie für denselben `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`Ursprung verbietet das Lesen der Remoteressource bei .</span><span class="sxs-lookup"><span data-stu-id="cccab-274">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="cccab-275">(Grund: CORS-Anforderung war nicht erfolgreich).</span><span class="sxs-lookup"><span data-stu-id="cccab-275">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="cccab-276">Weitere Informationen</span><span class="sxs-lookup"><span data-stu-id="cccab-276">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="cccab-277">Chromium-basiert: Der Zugriffhttps://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5auf abruf en ' from origin 'https://cors3.azurewebsites.netwurde durch die CORS-Richtlinie blockiert: Die Antwort auf die Preflight-Anforderung besteht keine Zugriffssteuerungsüberprüfung: Es ist kein 'Access-Control-Allow-Origin'-Header auf der angeforderten Ressource vorhanden.</span><span class="sxs-lookup"><span data-stu-id="cccab-277">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="cccab-278">Falls eine opake Antwort Ihre Anforderungen erfüllt, legen Sie den Modus der Anforderung auf 'no-cors' fest, um CORS für den Ressourcenabruf zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="cccab-278">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="cccab-279">Um bestimmte Header zuzulassen, rufen Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span><span class="sxs-lookup"><span data-stu-id="cccab-279">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="cccab-280">Um alle [Autorenanforderungsheader](https://www.w3.org/TR/cors/#author-request-headers) <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>zuzulassen, rufen Sie :</span><span class="sxs-lookup"><span data-stu-id="cccab-280">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="cccab-281">Browser sind nicht konsistent, wie `Access-Control-Request-Headers`sie festlegen.</span><span class="sxs-lookup"><span data-stu-id="cccab-281">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="cccab-282">Wenn beides:</span><span class="sxs-lookup"><span data-stu-id="cccab-282">If either:</span></span>

* <span data-ttu-id="cccab-283">Header werden auf etwas anderes als`"*"`</span><span class="sxs-lookup"><span data-stu-id="cccab-283">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="cccab-284"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>heißt: Schließen Sie `Accept` `Content-Type`mindestens `Origin`, und ein, sowie alle benutzerdefinierten Header, die Sie unterstützen möchten.</span><span class="sxs-lookup"><span data-stu-id="cccab-284"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="cccab-285">Automatischer Preflight-Anforderungscode</span><span class="sxs-lookup"><span data-stu-id="cccab-285">Automatic preflight request code</span></span>

<span data-ttu-id="cccab-286">Wenn die CORS-Richtlinie angewendet wird, wird:</span><span class="sxs-lookup"><span data-stu-id="cccab-286">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="cccab-287">Global durch `app.UseCors` Aufrufen `Startup.Configure`von .</span><span class="sxs-lookup"><span data-stu-id="cccab-287">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="cccab-288">Verwenden `[EnableCors]` des Attributs.</span><span class="sxs-lookup"><span data-stu-id="cccab-288">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="cccab-289">ASP.NET Core reagiert auf die Preflight OPTIONS-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="cccab-289">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="cccab-290">Das Aktivieren von CORS pro `RequireCors` Endpunkt mithilfe der aktuellen Verwendung unterstützt ***keine*** automatischen Preflight-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="cccab-290">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support automatic preflight requests.</span></span>

<span data-ttu-id="cccab-291">Der Abschnitt [TEST CORS](#testc) dieses Dokuments veranschaulicht dieses Verhalten.</span><span class="sxs-lookup"><span data-stu-id="cccab-291">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="cccab-292">[HttpOptions]-Attribut für Preflight-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="cccab-292">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="cccab-293">Wenn CORS mit der entsprechenden Richtlinie aktiviert ist, reagiert ASP.NET Core in der Regel automatisch auf CORS-Preflight-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="cccab-293">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="cccab-294">In einigen Szenarien ist dies möglicherweise nicht der Fall.</span><span class="sxs-lookup"><span data-stu-id="cccab-294">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="cccab-295">Verwenden von [CORS mit Endpunktrouting](#ecors).</span><span class="sxs-lookup"><span data-stu-id="cccab-295">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="cccab-296">Der folgende Code verwendet das [Attribut [HttpOptions],](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) um Endpunkte für OPTIONS-Anforderungen zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="cccab-296">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="cccab-297">Anweisungen zum Testen des vorherigen Codes finden Sie unter Testen von [CORS mit Endpunktrouting und [HttpOptions].](#tcer)</span><span class="sxs-lookup"><span data-stu-id="cccab-297">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="cccab-298">Festlegen der Ablaufzeit vor dem Flug</span><span class="sxs-lookup"><span data-stu-id="cccab-298">Set the preflight expiration time</span></span>

<span data-ttu-id="cccab-299">Der `Access-Control-Max-Age` Header gibt an, wie lange die Antwort auf die Preflight-Anforderung zwischengespeichert werden kann.</span><span class="sxs-lookup"><span data-stu-id="cccab-299">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="cccab-300">Um diesen Header <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>festzulegen, rufen Sie :</span><span class="sxs-lookup"><span data-stu-id="cccab-300">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="cccab-301">Funktionsweise von CORS</span><span class="sxs-lookup"><span data-stu-id="cccab-301">How CORS works</span></span>

<span data-ttu-id="cccab-302">In diesem Abschnitt wird beschrieben, was in einer [CORS-Anforderung](https://developer.mozilla.org/docs/Web/HTTP/CORS) auf der Ebene der HTTP-Nachrichten geschieht.</span><span class="sxs-lookup"><span data-stu-id="cccab-302">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="cccab-303">CORS ist **kein** Sicherheitsfeature.</span><span class="sxs-lookup"><span data-stu-id="cccab-303">CORS is **not** a security feature.</span></span> <span data-ttu-id="cccab-304">CORS ist ein W3C-Standard, der es einem Server ermöglicht, die Richtlinie mit demselben Ursprung zu entspannen.</span><span class="sxs-lookup"><span data-stu-id="cccab-304">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="cccab-305">Beispielsweise könnte ein böswilliger Akteur [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) für Ihre Website verwenden und eine websiteübergreifende Anforderung an seine CORS-fähige Website ausführen, um Informationen zu stehlen.</span><span class="sxs-lookup"><span data-stu-id="cccab-305">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="cccab-306">Eine API ist nicht sicherer, wenn CORS zugelassen wird.</span><span class="sxs-lookup"><span data-stu-id="cccab-306">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="cccab-307">Es liegt am Client (Browser), CORS zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="cccab-307">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="cccab-308">Der Server führt die Anforderung aus und gibt die Antwort zurück, es ist der Client, der einen Fehler zurückgibt und die Antwort blockiert.</span><span class="sxs-lookup"><span data-stu-id="cccab-308">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="cccab-309">Eines der folgenden Tools zeigt z. B. die Serverantwort an:</span><span class="sxs-lookup"><span data-stu-id="cccab-309">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="cccab-310">Fiddler</span><span class="sxs-lookup"><span data-stu-id="cccab-310">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="cccab-311">Postman</span><span class="sxs-lookup"><span data-stu-id="cccab-311">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="cccab-312">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="cccab-312">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="cccab-313">Ein Webbrowser, indem er die URL in die Adressleiste eingibt.</span><span class="sxs-lookup"><span data-stu-id="cccab-313">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="cccab-314">Es ist eine Möglichkeit für einen Server, Browsern die Ausführung einer [ursprungsübergreifenden XHR-](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) oder [Fetch-API-Anforderung](https://developer.mozilla.org/docs/Web/API/Fetch_API) zu ermöglichen, die andernfalls verboten wäre.</span><span class="sxs-lookup"><span data-stu-id="cccab-314">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="cccab-315">Browser ohne CORS können keine ursprungsübergreifenden Anforderungen verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="cccab-315">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="cccab-316">Vor CORS wurde [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) verwendet, um diese Einschränkung zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="cccab-316">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="cccab-317">JSONP verwendet XHR nicht, es `<script>` verwendet das Tag, um die Antwort zu empfangen.</span><span class="sxs-lookup"><span data-stu-id="cccab-317">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="cccab-318">Skripts dürfen ursprungsübergreifend geladen werden.</span><span class="sxs-lookup"><span data-stu-id="cccab-318">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="cccab-319">Die [CORS-Spezifikation](https://www.w3.org/TR/cors/) führte mehrere neue HTTP-Header ein, die ursprungsübergreifende Anforderungen ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="cccab-319">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="cccab-320">Wenn ein Browser CORS unterstützt, werden diese Header automatisch für ursprungsübergreifende Anforderungen festgelegt.</span><span class="sxs-lookup"><span data-stu-id="cccab-320">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="cccab-321">Benutzerdefinierter JavaScript-Code ist zum Aktivieren von CORS nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="cccab-321">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="cccab-322">Die [PUT-Testschaltfläche](https://cors3.azurewebsites.net/test) auf dem [bereitgestellten Beispiel](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span><span class="sxs-lookup"><span data-stu-id="cccab-322">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="cccab-323">Im Folgenden finden Sie ein Beispiel für eine ursprungsübergreifende Anforderung von der [Schaltfläche Wertetest](https://cors3.azurewebsites.net/) an `https://cors1.azurewebsites.net/api/values`.</span><span class="sxs-lookup"><span data-stu-id="cccab-323">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="cccab-324">Der `Origin` Header:</span><span class="sxs-lookup"><span data-stu-id="cccab-324">The `Origin` header:</span></span>

* <span data-ttu-id="cccab-325">Stellt die Domäne der Site bereit, die die Anforderung stellt.</span><span class="sxs-lookup"><span data-stu-id="cccab-325">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="cccab-326">Ist erforderlich und muss sich vom Host unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="cccab-326">Is required and must be different from the host.</span></span>

<span data-ttu-id="cccab-327">**Allgemeine Kopfzeilen**</span><span class="sxs-lookup"><span data-stu-id="cccab-327">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="cccab-328">**Antwortheader**</span><span class="sxs-lookup"><span data-stu-id="cccab-328">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="cccab-329">**Anforderungsheader**</span><span class="sxs-lookup"><span data-stu-id="cccab-329">**Request headers**</span></span>

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

<span data-ttu-id="cccab-330">Bei `OPTIONS` Anforderungen legt der Server den **Headerheader "Antwort"** `Access-Control-Allow-Origin: {allowed origin}` in der Antwort fest.</span><span class="sxs-lookup"><span data-stu-id="cccab-330">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="cccab-331">Die Schaltflächenanforderung `OPTIONS` "Das bereitgestellte [Beispiel](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [[EnableCors]](https://cors1.azurewebsites.net/test?number=2) löschen enthält beispielsweise die folgenden Header:</span><span class="sxs-lookup"><span data-stu-id="cccab-331">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="cccab-332">**Allgemeine Kopfzeilen**</span><span class="sxs-lookup"><span data-stu-id="cccab-332">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="cccab-333">**Antwortheader**</span><span class="sxs-lookup"><span data-stu-id="cccab-333">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="cccab-334">**Anforderungsheader**</span><span class="sxs-lookup"><span data-stu-id="cccab-334">**Request headers**</span></span>

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

<span data-ttu-id="cccab-335">In den vorherigen **Response-Headern**legt der Server den [Access-Control-Allow-Origin-Header](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) in der Antwort fest.</span><span class="sxs-lookup"><span data-stu-id="cccab-335">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="cccab-336">Der `https://cors1.azurewebsites.net` Wert dieses Headers entspricht dem `Origin` Header aus der Anforderung.</span><span class="sxs-lookup"><span data-stu-id="cccab-336">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="cccab-337">Wenn <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> aufgerufen wird, wird der `Access-Control-Allow-Origin: *`, der Platzhalterwert, zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="cccab-337">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="cccab-338">`AllowAnyOrigin`ermöglicht jede Herkunft.</span><span class="sxs-lookup"><span data-stu-id="cccab-338">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="cccab-339">Wenn die Antwort den `Access-Control-Allow-Origin` Header nicht enthält, schlägt die ursprungsübergreifende Anforderung fehl.</span><span class="sxs-lookup"><span data-stu-id="cccab-339">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="cccab-340">Insbesondere lässt der Browser die Anforderung nicht zu.</span><span class="sxs-lookup"><span data-stu-id="cccab-340">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="cccab-341">Selbst wenn der Server eine erfolgreiche Antwort zurückgibt, stellt der Browser die Antwort nicht für die Client-App bereit.</span><span class="sxs-lookup"><span data-stu-id="cccab-341">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="cccab-342">OPTIONS-Anforderungen anzeigen</span><span class="sxs-lookup"><span data-stu-id="cccab-342">Display OPTIONS requests</span></span>

<span data-ttu-id="cccab-343">Standardmäßig zeigen die Chrome- und Edge-Browser keine OPTIONS-Anforderungen auf der Registerkarte Netzwerk der F12-Tools an.</span><span class="sxs-lookup"><span data-stu-id="cccab-343">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="cccab-344">So zeigen Sie OPTIONS-Anforderungen in diesen Browsern an:</span><span class="sxs-lookup"><span data-stu-id="cccab-344">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="cccab-345">`chrome://flags/#out-of-blink-cors` oder `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="cccab-345">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="cccab-346">deaktivieren Sie das Flag.</span><span class="sxs-lookup"><span data-stu-id="cccab-346">disable the flag.</span></span>
* <span data-ttu-id="cccab-347">„Neu starten“.</span><span class="sxs-lookup"><span data-stu-id="cccab-347">restart.</span></span>

<span data-ttu-id="cccab-348">Firefox zeigt OPTIONS-Anforderungen standardmäßig an.</span><span class="sxs-lookup"><span data-stu-id="cccab-348">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="cccab-349">CORS in IIS</span><span class="sxs-lookup"><span data-stu-id="cccab-349">CORS in IIS</span></span>

<span data-ttu-id="cccab-350">Bei der Bereitstellung in IIS muss CORS vor der Windows-Authentifizierung ausgeführt werden, wenn der Server nicht für den anonymen Zugriff konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="cccab-350">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="cccab-351">Um dieses Szenario zu unterstützen, muss das [IIS CORS-Modul](https://www.iis.net/downloads/microsoft/iis-cors-module) für die App installiert und konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="cccab-351">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="cccab-352">Testen von CORS</span><span class="sxs-lookup"><span data-stu-id="cccab-352">Test CORS</span></span>

<span data-ttu-id="cccab-353">Der [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) enthält Code zum Testen von CORS.</span><span class="sxs-lookup"><span data-stu-id="cccab-353">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="cccab-354">Informationen [zum Herunterladen](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="cccab-354">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="cccab-355">Das Beispiel ist ein API-Projekt mit Razor Pages hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="cccab-355">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="cccab-356">`WithOrigins("https://localhost:<port>");`sollte nur zum Testen einer Beispiel-App verwendet werden, die dem [Download-Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors)ähnelt.</span><span class="sxs-lookup"><span data-stu-id="cccab-356">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="cccab-357">Im `ValuesController` Folgenden werden die Endpunkte zum Testen angezeigt:</span><span class="sxs-lookup"><span data-stu-id="cccab-357">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="cccab-358">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) wird vom [Paket Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet bereitgestellt und zeigt Routeninformationen an.</span><span class="sxs-lookup"><span data-stu-id="cccab-358">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="cccab-359">Testen Sie den vorherigen Beispielcode mit einem der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="cccab-359">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="cccab-360">Verwenden Sie die [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/)bereitgestellte Beispiel-App unter .</span><span class="sxs-lookup"><span data-stu-id="cccab-360">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="cccab-361">Es ist nicht erforderlich, das Beispiel herunterzuladen.</span><span class="sxs-lookup"><span data-stu-id="cccab-361">There is no need to download the sample.</span></span>
* <span data-ttu-id="cccab-362">Führen Sie `dotnet run` das Beispiel mit `https://localhost:5001`der Standard-URL von aus.</span><span class="sxs-lookup"><span data-stu-id="cccab-362">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="cccab-363">Führen Sie das Beispiel aus Visual Studio aus, wobei der `https://localhost:44398`Port für eine URL von auf 44398 festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="cccab-363">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="cccab-364">Verwenden eines Browsers mit den F12-Tools:</span><span class="sxs-lookup"><span data-stu-id="cccab-364">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="cccab-365">Wählen Sie die Schaltfläche **Werte aus,** und überprüfen Sie die Kopfzeilen auf der Registerkarte **Netzwerk.**</span><span class="sxs-lookup"><span data-stu-id="cccab-365">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="cccab-366">Wählen Sie die **PUT-Testschaltfläche** aus.</span><span class="sxs-lookup"><span data-stu-id="cccab-366">Select the **PUT test** button.</span></span> <span data-ttu-id="cccab-367">Siehe Anzeigen von [OPTIONS-Anforderungen](#options) für Anweisungen zum Anzeigen der OPTIONS-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="cccab-367">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="cccab-368">Der **PUT-Test** erstellt zwei Anforderungen, eine OPTIONS-Preflight-Anforderung und die PUT-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="cccab-368">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="cccab-369">Wählen **`GetValues2 [DisableCors]`** Sie die Schaltfläche aus, um eine fehlgeschlagene CORS-Anforderung auszulösen.</span><span class="sxs-lookup"><span data-stu-id="cccab-369">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="cccab-370">Wie im Dokument erwähnt, gibt die Antwort 200 Erfolge zurück, aber die CORS-Anforderung wird nicht gestellt.</span><span class="sxs-lookup"><span data-stu-id="cccab-370">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="cccab-371">Wählen Sie die Registerkarte **Konsole** aus, um den CORS-Fehler anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="cccab-371">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="cccab-372">Je nach Browser wird ein ähnlicher Fehler wie folgt angezeigt:</span><span class="sxs-lookup"><span data-stu-id="cccab-372">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="cccab-373">Der Zugriff `'https://cors1.azurewebsites.net/api/values/GetValues2'` auf `'https://cors3.azurewebsites.net'` abrufen vom Ursprung wurde durch die CORS-Richtlinie blockiert: In der angeforderten Ressource ist kein 'Access-Control-Allow-Origin'-Header vorhanden.</span><span class="sxs-lookup"><span data-stu-id="cccab-373">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="cccab-374">Falls eine opake Antwort Ihre Anforderungen erfüllt, legen Sie den Modus der Anforderung auf 'no-cors' fest, um CORS für den Ressourcenabruf zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="cccab-374">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="cccab-375">CORS-fähige Endpunkte können mit einem Tool getestet werden, z. B. [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler)oder [Postman](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="cccab-375">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="cccab-376">Bei Verwendung eines Tools muss der Ursprung `Origin` der vom Header angegebenen Anforderung vom Host, der die Anforderung empfängt, abweichen.</span><span class="sxs-lookup"><span data-stu-id="cccab-376">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="cccab-377">Wenn die Anforderung nicht *ursprungsübergreifend* ist, basiert `Origin` sie auf dem Wert des Headers:</span><span class="sxs-lookup"><span data-stu-id="cccab-377">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="cccab-378">CORS Middleware muss die Anforderung nicht verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="cccab-378">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="cccab-379">CORS-Header werden in der Antwort nicht zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="cccab-379">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="cccab-380">Der folgende `curl` Befehl verwendet, um eine OPTIONS-Anforderung mit Informationen ausstellen:</span><span class="sxs-lookup"><span data-stu-id="cccab-380">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="cccab-381">Testen von CORS mit Endpunktrouting und [HttpOptions]</span><span class="sxs-lookup"><span data-stu-id="cccab-381">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="cccab-382">Das Aktivieren von CORS pro `RequireCors` Endpunkt mithilfe der aktuellen Verwendung unterstützt ***keine*** [automatischen Preflight-Anforderungen](#apf).</span><span class="sxs-lookup"><span data-stu-id="cccab-382">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="cccab-383">Berücksichtigen Sie den folgenden Code, der [Endpunktrouting verwendet, um CORS zu aktivieren:](#ecors)</span><span class="sxs-lookup"><span data-stu-id="cccab-383">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="cccab-384">Im `TodoItems1Controller` Folgenden finden Sie Endpunkte zum Testen:</span><span class="sxs-lookup"><span data-stu-id="cccab-384">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="cccab-385">Testen Sie den vorherigen Code auf der [Testseite](https://cors1.azurewebsites.net/test?number=1) des bereitgestellten [Beispiels](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span><span class="sxs-lookup"><span data-stu-id="cccab-385">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="cccab-386">Die Schaltflächen **[EnableCors]** und **GET [EnableCors]** sind `[EnableCors]` erfolgreich, da die Endpunkte preflight-Anforderungen haben und darauf reagieren.</span><span class="sxs-lookup"><span data-stu-id="cccab-386">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="cccab-387">Die anderen Endpunkte schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="cccab-387">The other endpoints fails.</span></span> <span data-ttu-id="cccab-388">Die **GET-Schaltfläche** schlägt fehl, da [JavaScript:](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js)</span><span class="sxs-lookup"><span data-stu-id="cccab-388">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="cccab-389">Das `TodoItems2Controller` Folgende stellt ähnliche Endpunkte bereit, enthält jedoch expliziten Code, um auf OPTIONS-Anforderungen zu reagieren:</span><span class="sxs-lookup"><span data-stu-id="cccab-389">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="cccab-390">Testen Sie den vorherigen Code auf der [Testseite](https://cors1.azurewebsites.net/test?number=2) des bereitgestellten Beispiels.</span><span class="sxs-lookup"><span data-stu-id="cccab-390">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="cccab-391">Wählen Sie in der **Dropdown-Liste Controller** **Preflight** und set **Controller**aus.</span><span class="sxs-lookup"><span data-stu-id="cccab-391">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="cccab-392">Alle CORS-Aufrufe `TodoItems2Controller` an die Endpunkte sind erfolgreich.</span><span class="sxs-lookup"><span data-stu-id="cccab-392">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cccab-393">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="cccab-393">Additional resources</span></span>

* [<span data-ttu-id="cccab-394">Cross-Origin Resource Sharing (CORS)</span><span class="sxs-lookup"><span data-stu-id="cccab-394">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="cccab-395">Erste Schritte mit dem IIS CORS-Modul</span><span class="sxs-lookup"><span data-stu-id="cccab-395">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cccab-396">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cccab-396">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="cccab-397">Dieser Artikel zeigt, wie CORS in einer ASP.NET Core-App aktiviert wird.</span><span class="sxs-lookup"><span data-stu-id="cccab-397">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="cccab-398">Die Browsersicherheit verhindert, dass eine Webseite Anforderungen an eine andere Domäne als die, die die Webseite bereitgestellt hat, stellt.</span><span class="sxs-lookup"><span data-stu-id="cccab-398">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="cccab-399">Diese Einschränkung wird als *Richtlinie mit gleichem Ursprung*bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="cccab-399">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="cccab-400">Die Richtlinie mit demselben Ursprung verhindert, dass eine böswillige Website vertrauliche Daten von einer anderen Website liest.</span><span class="sxs-lookup"><span data-stu-id="cccab-400">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="cccab-401">Manchmal möchten Sie möglicherweise zulassen, dass andere Websites ursprungsübergreifende Anforderungen an Ihre App stellen.</span><span class="sxs-lookup"><span data-stu-id="cccab-401">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="cccab-402">Weitere Informationen finden Sie im [Mozilla CORS-Artikel](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="cccab-402">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="cccab-403">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="cccab-403">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="cccab-404">Ein W3C-Standard, der es einem Server ermöglicht, die Richtlinie mit demselben Ursprung zu entspannen.</span><span class="sxs-lookup"><span data-stu-id="cccab-404">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="cccab-405">Ist **kein** Sicherheitsmerkmal, CORS entspannt die Sicherheit.</span><span class="sxs-lookup"><span data-stu-id="cccab-405">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="cccab-406">Eine API ist nicht sicherer, wenn CORS zugelassen wird.</span><span class="sxs-lookup"><span data-stu-id="cccab-406">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="cccab-407">Weitere Informationen finden Sie unter Funktionsweise von [CORS](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="cccab-407">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="cccab-408">Ermöglicht einem Server, einige ursprungsübergreifende Anforderungen explizit zuzulassen, während andere abgelehnt werden.</span><span class="sxs-lookup"><span data-stu-id="cccab-408">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="cccab-409">Ist sicherer und flexibler als frühere Techniken, wie z. B. [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="cccab-409">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="cccab-410">[Beispielcode anzeigen oder herunterladen](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([downloaden](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cccab-410">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="cccab-411">Gleicher Ursprung</span><span class="sxs-lookup"><span data-stu-id="cccab-411">Same origin</span></span>

<span data-ttu-id="cccab-412">Zwei URLs haben denselben Ursprung, wenn sie identische Schemata, Hosts und Ports haben ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="cccab-412">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="cccab-413">Diese beiden URLs haben den gleichen Ursprung:</span><span class="sxs-lookup"><span data-stu-id="cccab-413">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="cccab-414">Diese URLs haben andere Ursprünge als die beiden vorherigen URLs:</span><span class="sxs-lookup"><span data-stu-id="cccab-414">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="cccab-415">`https://example.net`&ndash; Verschiedene Domäne</span><span class="sxs-lookup"><span data-stu-id="cccab-415">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="cccab-416">`https://www.example.com/foo.html`&ndash; Verschiedene Subdomain</span><span class="sxs-lookup"><span data-stu-id="cccab-416">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="cccab-417">`http://example.com/foo.html`&ndash; Unterschiedlicheregelung</span><span class="sxs-lookup"><span data-stu-id="cccab-417">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="cccab-418">`https://example.com:9000/foo.html`&ndash; Verschiedene randder Hafen</span><span class="sxs-lookup"><span data-stu-id="cccab-418">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="cccab-419">Internet Explorer berücksichtigt den Port nicht beim Vergleich der Ursprünge.</span><span class="sxs-lookup"><span data-stu-id="cccab-419">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="cccab-420">CORS mit benannter Richtlinie und Middleware</span><span class="sxs-lookup"><span data-stu-id="cccab-420">CORS with named policy and middleware</span></span>

<span data-ttu-id="cccab-421">CORS Middleware verarbeitet ursprungsübergreifende Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="cccab-421">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="cccab-422">Der folgende Code aktiviert CORS für die gesamte App mit dem angegebenen Ursprung:</span><span class="sxs-lookup"><span data-stu-id="cccab-422">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="cccab-423">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="cccab-423">The preceding code:</span></span>

* <span data-ttu-id="cccab-424">Legt den Richtliniennamen\_auf "myAllowSpecificOrigins" fest.</span><span class="sxs-lookup"><span data-stu-id="cccab-424">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="cccab-425">Der Richtlinienname ist willkürlich.</span><span class="sxs-lookup"><span data-stu-id="cccab-425">The policy name is arbitrary.</span></span>
* <span data-ttu-id="cccab-426">Ruft <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> die Erweiterungsmethode auf, die CORS aktiviert.</span><span class="sxs-lookup"><span data-stu-id="cccab-426">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="cccab-427">Aufrufe <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> mit einem [Lambda-Ausdruck](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="cccab-427">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="cccab-428">Der Lambda <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> nimmt ein Objekt.</span><span class="sxs-lookup"><span data-stu-id="cccab-428">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="cccab-429">[Konfigurationsoptionen](#cors-policy-options), `WithOrigins`z. B. , werden weiter unten in diesem Artikel beschrieben.</span><span class="sxs-lookup"><span data-stu-id="cccab-429">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="cccab-430">Der <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> Methodenaufruf fügt DEM Dienstcontainer der App CORS-Dienste hinzu:</span><span class="sxs-lookup"><span data-stu-id="cccab-430">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="cccab-431">Weitere Informationen finden Sie unter [CORS-Richtlinienoptionen](#cpo) in diesem Dokument .</span><span class="sxs-lookup"><span data-stu-id="cccab-431">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="cccab-432">Die <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> Methode kann Methoden verketten, wie im folgenden Code gezeigt:</span><span class="sxs-lookup"><span data-stu-id="cccab-432">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="cccab-433">Hinweis: Die URL darf **keinen** nachgestellten Schrägstrich (`/`) enthalten.</span><span class="sxs-lookup"><span data-stu-id="cccab-433">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="cccab-434">Wenn die URL `/`mit beendet `false` wird, wird der Vergleich zurückgegeben, und es wird kein Header zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="cccab-434">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="cccab-435">Der folgende Code wendet CORS-Richtlinien auf alle App-Endpunkte über CORS Middleware an:</span><span class="sxs-lookup"><span data-stu-id="cccab-435">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
<span data-ttu-id="cccab-436">Hinweis: `UseCors` muss vor `UseMvc`aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="cccab-436">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="cccab-437">Weitere Informationen finden Sie unter Aktivieren von [CORS in Razor Pages, Controllern und Aktionsmethoden](#ecors) zum Anwenden der CORS-Richtlinie auf Seiten-/Controller-/Aktionsebene.</span><span class="sxs-lookup"><span data-stu-id="cccab-437">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="cccab-438">Anweisungen zum Testen von Code ähnlich dem vorherigen Code finden Sie unter [Test-CORS.](#test)</span><span class="sxs-lookup"><span data-stu-id="cccab-438">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="cccab-439">CORS mit Attributen aktivieren</span><span class="sxs-lookup"><span data-stu-id="cccab-439">Enable CORS with attributes</span></span>

<span data-ttu-id="cccab-440">Das [ &lbrack;&rbrack; EnableCors-Attribut](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) bietet eine Alternative zum globalen Anwenden von CORS.</span><span class="sxs-lookup"><span data-stu-id="cccab-440">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="cccab-441">Das `[EnableCors]` Attribut aktiviert CORS für ausgewählte Endpunkte und nicht für alle Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="cccab-441">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="cccab-442">Verwenden `[EnableCors]` Sie diese Option, um die Standardrichtlinie und `[EnableCors("{Policy String}")]` eine Richtlinie anzugeben.</span><span class="sxs-lookup"><span data-stu-id="cccab-442">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="cccab-443">Das `[EnableCors]` Attribut kann angewendet werden auf:</span><span class="sxs-lookup"><span data-stu-id="cccab-443">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="cccab-444">Razor Page`PageModel`</span><span class="sxs-lookup"><span data-stu-id="cccab-444">Razor Page `PageModel`</span></span>
* <span data-ttu-id="cccab-445">Controller</span><span class="sxs-lookup"><span data-stu-id="cccab-445">Controller</span></span>
* <span data-ttu-id="cccab-446">Controller-Aktionsmethode</span><span class="sxs-lookup"><span data-stu-id="cccab-446">Controller action method</span></span>

<span data-ttu-id="cccab-447">Sie können mit dem `[EnableCors]` Attribut unterschiedliche Richtlinien auf Controller/Seitenmodell/Aktion anwenden.</span><span class="sxs-lookup"><span data-stu-id="cccab-447">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="cccab-448">Wenn `[EnableCors]` das Attribut auf eine Controller-/Seitenmodell-/Aktionsmethode angewendet wird und CORS in Middleware aktiviert ist, werden ***beide*** Richtlinien angewendet.</span><span class="sxs-lookup"><span data-stu-id="cccab-448">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="cccab-449">Es wird empfohlen, richtlinien ***nicht*** zu kombinieren.</span><span class="sxs-lookup"><span data-stu-id="cccab-449">We recommend ***not*** combining policies.</span></span> <span data-ttu-id="cccab-450">Verwenden `[EnableCors]` Sie das Attribut oder die Middleware, \***nicht beides**.</span><span class="sxs-lookup"><span data-stu-id="cccab-450">Use the `[EnableCors]` attribute or middleware, \***not both**.</span></span> <span data-ttu-id="cccab-451">Bei `[EnableCors]`Verwendung von definieren Sie **keine** Standardrichtlinie.</span><span class="sxs-lookup"><span data-stu-id="cccab-451">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="cccab-452">Der folgende Code wendet für jede Methode eine andere Richtlinie an:</span><span class="sxs-lookup"><span data-stu-id="cccab-452">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="cccab-453">Der folgende Code erstellt eine CORS-Standardrichtlinie und eine Richtlinie mit dem Namen: `"AnotherPolicy"`</span><span class="sxs-lookup"><span data-stu-id="cccab-453">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="cccab-454">DEAKTIVIEREN von CORS</span><span class="sxs-lookup"><span data-stu-id="cccab-454">Disable CORS</span></span>

<span data-ttu-id="cccab-455">Das [ &lbrack;&rbrack; DisableCors-Attribut](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) deaktiviert CORS für den Controller/Seitenmodell/die Aktion.</span><span class="sxs-lookup"><span data-stu-id="cccab-455">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="cccab-456">CORS-Richtlinienoptionen</span><span class="sxs-lookup"><span data-stu-id="cccab-456">CORS policy options</span></span>

<span data-ttu-id="cccab-457">In diesem Abschnitt werden die verschiedenen Optionen beschrieben, die in einer CORS-Richtlinie festgelegt werden können:</span><span class="sxs-lookup"><span data-stu-id="cccab-457">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="cccab-458">Festlegen der zulässigen Ursprünge</span><span class="sxs-lookup"><span data-stu-id="cccab-458">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="cccab-459">Festlegen der zulässigen HTTP-Methoden</span><span class="sxs-lookup"><span data-stu-id="cccab-459">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="cccab-460">Festlegen der zulässigen Anforderungsheader</span><span class="sxs-lookup"><span data-stu-id="cccab-460">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="cccab-461">Festlegen der freiliegenden Antwortheader</span><span class="sxs-lookup"><span data-stu-id="cccab-461">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="cccab-462">Anmeldeinformationen in ursprungsübergreifenden Anforderungen</span><span class="sxs-lookup"><span data-stu-id="cccab-462">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="cccab-463">Festlegen der Ablaufzeit vor dem Flug</span><span class="sxs-lookup"><span data-stu-id="cccab-463">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="cccab-464"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>wird in `Startup.ConfigureServices`aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="cccab-464"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="cccab-465">Bei einigen Optionen kann es hilfreich sein, zuerst den Abschnitt ["Funktionsweise von CORS"](#how-cors) zu lesen.</span><span class="sxs-lookup"><span data-stu-id="cccab-465">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="cccab-466">Festlegen der zulässigen Ursprünge</span><span class="sxs-lookup"><span data-stu-id="cccab-466">Set the allowed origins</span></span>

<span data-ttu-id="cccab-467"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Ermöglicht CORS-Anforderungen aller Herkunftsherkunft mit einem beliebigen Schema (`http` oder `https`).</span><span class="sxs-lookup"><span data-stu-id="cccab-467"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="cccab-468">`AllowAnyOrigin`ist unsicher, da *jede Website* ursprungsübergreifende Anforderungen an die App stellen kann.</span><span class="sxs-lookup"><span data-stu-id="cccab-468">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="cccab-469">Angeben `AllowAnyOrigin` und `AllowCredentials` ist eine unsichere Konfiguration und kann zu standortübergreifenden Anforderungsfälschungen führen.</span><span class="sxs-lookup"><span data-stu-id="cccab-469">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="cccab-470">Geben Sie für eine sichere App eine genaue Liste der Ursprünge an, wenn der Client sich selbst für den Zugriff auf Serverressourcen autorisieren muss.</span><span class="sxs-lookup"><span data-stu-id="cccab-470">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="cccab-471">`AllowAnyOrigin`wirkt sich auf `Access-Control-Allow-Origin` Preflight-Anforderungen und den Header aus.</span><span class="sxs-lookup"><span data-stu-id="cccab-471">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="cccab-472">Weitere Informationen finden Sie im Abschnitt [Preflight-Anforderungen.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="cccab-472">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="cccab-473"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Legt <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> die Eigenschaft der Richtlinie als funktion fest, die es dem Ursprung ermöglicht, eine konfigurierte Platzhalterdomäne abzugleichen, wenn ausgewertet wird, ob der Ursprung zulässig ist.</span><span class="sxs-lookup"><span data-stu-id="cccab-473"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="cccab-474">Festlegen der zulässigen HTTP-Methoden</span><span class="sxs-lookup"><span data-stu-id="cccab-474">Set the allowed HTTP methods</span></span>

<span data-ttu-id="cccab-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="cccab-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="cccab-476">Erlaubt jede HTTP-Methode:</span><span class="sxs-lookup"><span data-stu-id="cccab-476">Allows any HTTP method:</span></span>
* <span data-ttu-id="cccab-477">Beeinflusst Preflight-Anforderungen `Access-Control-Allow-Methods` und den Header.</span><span class="sxs-lookup"><span data-stu-id="cccab-477">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="cccab-478">Weitere Informationen finden Sie im Abschnitt [Preflight-Anforderungen.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="cccab-478">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="cccab-479">Festlegen der zulässigen Anforderungsheader</span><span class="sxs-lookup"><span data-stu-id="cccab-479">Set the allowed request headers</span></span>

<span data-ttu-id="cccab-480">Um das Senden bestimmter Header in einer CORS-Anforderung zuzulassen, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> rufen Sie die *zulässigen*Header an, um das Senden bestimmter Header in einer CORS-Anforderung zu ermöglichen:</span><span class="sxs-lookup"><span data-stu-id="cccab-480">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="cccab-481">Um alle Autorenanforderungsheader <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>zuzulassen, rufen Sie :</span><span class="sxs-lookup"><span data-stu-id="cccab-481">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="cccab-482">Diese Einstellung wirkt sich `Access-Control-Request-Headers` auf Preflight-Anforderungen und den Header aus.</span><span class="sxs-lookup"><span data-stu-id="cccab-482">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="cccab-483">Weitere Informationen finden Sie im Abschnitt [Preflight-Anforderungen.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="cccab-483">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="cccab-484">CORS Middleware ermöglicht immer, `Access-Control-Request-Headers` dass vier Header in der gesendet werden, unabhängig von den in CorsPolicy.Headers konfigurierten Werten.</span><span class="sxs-lookup"><span data-stu-id="cccab-484">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="cccab-485">Diese Liste der Header enthält:</span><span class="sxs-lookup"><span data-stu-id="cccab-485">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="cccab-486">Betrachten Sie beispielsweise eine App, die wie folgt konfiguriert ist:</span><span class="sxs-lookup"><span data-stu-id="cccab-486">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="cccab-487">CORS Middleware reagiert erfolgreich auf eine Preflight-Anforderung `Content-Language` mit dem folgenden Anforderungsheader, da immer auf der Whitelist steht:</span><span class="sxs-lookup"><span data-stu-id="cccab-487">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="cccab-488">Festlegen der freiliegenden Antwortheader</span><span class="sxs-lookup"><span data-stu-id="cccab-488">Set the exposed response headers</span></span>

<span data-ttu-id="cccab-489">Standardmäßig macht der Browser nicht alle Antwortheader für die App verfügbar.</span><span class="sxs-lookup"><span data-stu-id="cccab-489">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="cccab-490">Weitere Informationen finden Sie unter [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="cccab-490">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="cccab-491">Die standardmäßig verfügbaren Antwortheader sind:</span><span class="sxs-lookup"><span data-stu-id="cccab-491">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="cccab-492">Die CORS-Spezifikation ruft diese Header *auf einfache Antwortheader*.</span><span class="sxs-lookup"><span data-stu-id="cccab-492">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="cccab-493">Um andere Header für die App <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>verfügbar zu machen, rufen Sie :</span><span class="sxs-lookup"><span data-stu-id="cccab-493">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="cccab-494">Anmeldeinformationen in ursprungsübergreifenden Anforderungen</span><span class="sxs-lookup"><span data-stu-id="cccab-494">Credentials in cross-origin requests</span></span>

<span data-ttu-id="cccab-495">Anmeldeinformationen erfordern eine spezielle Behandlung in einer CORS-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="cccab-495">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="cccab-496">Standardmäßig sendet der Browser keine Anmeldeinformationen mit einer ursprungsübergreifenden Anforderung.</span><span class="sxs-lookup"><span data-stu-id="cccab-496">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="cccab-497">Anmeldeinformationen umfassen Cookies und HTTP-Authentifizierungsschemata.</span><span class="sxs-lookup"><span data-stu-id="cccab-497">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="cccab-498">Um Anmeldeinformationen mit einer ursprungsübergreifenden Anforderung `XMLHttpRequest.withCredentials` zu `true`senden, muss der Client auf festlegen.</span><span class="sxs-lookup"><span data-stu-id="cccab-498">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="cccab-499">Direkt `XMLHttpRequest` verwenden:</span><span class="sxs-lookup"><span data-stu-id="cccab-499">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="cccab-500">Verwenden von jQuery:</span><span class="sxs-lookup"><span data-stu-id="cccab-500">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="cccab-501">Verwenden der [Abruf-API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="cccab-501">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="cccab-502">Der Server muss die Anmeldeinformationen zulassen.</span><span class="sxs-lookup"><span data-stu-id="cccab-502">The server must allow the credentials.</span></span> <span data-ttu-id="cccab-503">Um ursprungsübergreifende Anmeldeinformationen zuzulassen, rufen Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span><span class="sxs-lookup"><span data-stu-id="cccab-503">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="cccab-504">Die HTTP-Antwort `Access-Control-Allow-Credentials` enthält einen Header, der dem Browser mitteilt, dass der Server Anmeldeinformationen für eine ursprungsübergreifende Anforderung zulässt.</span><span class="sxs-lookup"><span data-stu-id="cccab-504">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="cccab-505">Wenn der Browser Anmeldeinformationen sendet, die Antwort `Access-Control-Allow-Credentials` jedoch keinen gültigen Header enthält, macht der Browser die Antwort für die App nicht verfügbar, und die ursprungsübergreifende Anforderung schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="cccab-505">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="cccab-506">Das Zulassen ursprungsübergreifender Anmeldeinformationen stellt ein Sicherheitsrisiko dar.</span><span class="sxs-lookup"><span data-stu-id="cccab-506">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="cccab-507">Eine Website in einer anderen Domäne kann die Anmeldeinformationen eines angemeldeten Benutzers im Namen des Benutzers ohne Wissen des Benutzers an die App senden.</span><span class="sxs-lookup"><span data-stu-id="cccab-507">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="cccab-508">Die CORS-Spezifikation gibt auch `"*"` an, dass das Festlegen `Access-Control-Allow-Credentials` von Ursprüngen auf (alle Ursprünge) ungültig ist, wenn der Header vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="cccab-508">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="cccab-509">Preflight-Anfragen</span><span class="sxs-lookup"><span data-stu-id="cccab-509">Preflight requests</span></span>

<span data-ttu-id="cccab-510">Bei einigen CORS-Anforderungen sendet der Browser eine zusätzliche Anforderung, bevor er die eigentliche Anforderung stellt.</span><span class="sxs-lookup"><span data-stu-id="cccab-510">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="cccab-511">Diese Anforderung wird als *Preflight-Anforderung*bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="cccab-511">This request is called a *preflight request*.</span></span> <span data-ttu-id="cccab-512">Der Browser kann die Preflight-Anforderung überspringen, wenn die folgenden Bedingungen zutreffen:</span><span class="sxs-lookup"><span data-stu-id="cccab-512">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="cccab-513">Die Anforderungsmethode ist GET, HEAD oder POST.</span><span class="sxs-lookup"><span data-stu-id="cccab-513">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="cccab-514">Die App stellt keine anderen Anforderungsheader `Content-Language` `Content-Type`als `Accept`, `Accept-Language`, , oder `Last-Event-ID`fest.</span><span class="sxs-lookup"><span data-stu-id="cccab-514">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="cccab-515">Der `Content-Type` Header, sofern festgelegt, hat einen der folgenden Werte:</span><span class="sxs-lookup"><span data-stu-id="cccab-515">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="cccab-516">Die Regel für Anforderungsheader, die für die Clientanforderung festgelegt `setRequestHeader` wurden, gilt für Header, die die App durch Aufrufen des `XMLHttpRequest` Objekts festlegt.</span><span class="sxs-lookup"><span data-stu-id="cccab-516">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="cccab-517">Die CORS-Spezifikation ruft diese *Header-Author-Anforderungsheader*auf.</span><span class="sxs-lookup"><span data-stu-id="cccab-517">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="cccab-518">Die Regel gilt nicht für Header, die der `User-Agent` `Host`Browser `Content-Length`festlegen kann, z. B. , oder .</span><span class="sxs-lookup"><span data-stu-id="cccab-518">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="cccab-519">Im Folgenden finden Sie ein Beispiel für eine Preflight-Anforderung:</span><span class="sxs-lookup"><span data-stu-id="cccab-519">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="cccab-520">Die Pre-Flight-Anforderung verwendet die HTTP OPTIONS-Methode.</span><span class="sxs-lookup"><span data-stu-id="cccab-520">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="cccab-521">Es enthält zwei spezielle Header:</span><span class="sxs-lookup"><span data-stu-id="cccab-521">It includes two special headers:</span></span>

* <span data-ttu-id="cccab-522">`Access-Control-Request-Method`: Die HTTP-Methode, die für die eigentliche Anforderung verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="cccab-522">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="cccab-523">`Access-Control-Request-Headers`: Eine Liste der Anforderungsheader, die die App für die eigentliche Anforderung festlegt.</span><span class="sxs-lookup"><span data-stu-id="cccab-523">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="cccab-524">Wie bereits erwähnt, enthält dies keine Header, die `User-Agent`der Browser festlegt, z. B. .</span><span class="sxs-lookup"><span data-stu-id="cccab-524">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="cccab-525">Wenn CORS mit der entsprechenden Richtlinie aktiviert ist, reagiert ASP.NET Core in der Regel automatisch auf CORS-Preflight-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="cccab-525">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="cccab-526">Siehe [[HttpOptions]-Attribut für Preflight-Anforderungen](#pro).</span><span class="sxs-lookup"><span data-stu-id="cccab-526">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="cccab-527">Eine CORS-Preflight-Anforderung `Access-Control-Request-Headers` kann einen Header enthalten, der dem Server die Header angibt, die mit der eigentlichen Anforderung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="cccab-527">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="cccab-528">Um bestimmte Header zuzulassen, rufen Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span><span class="sxs-lookup"><span data-stu-id="cccab-528">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="cccab-529">Um alle Autorenanforderungsheader <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>zuzulassen, rufen Sie :</span><span class="sxs-lookup"><span data-stu-id="cccab-529">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="cccab-530">Browser sind nicht vollständig konsistent in `Access-Control-Request-Headers`der Art und Weise, wie sie festlegen.</span><span class="sxs-lookup"><span data-stu-id="cccab-530">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="cccab-531">Wenn Sie Header auf etwas `"*"` anderes <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>als (oder verwenden `Accept` `Content-Type`) `Origin`festlegen, sollten Sie mindestens , und , sowie alle benutzerdefinierten Header einschließen, die Sie unterstützen möchten.</span><span class="sxs-lookup"><span data-stu-id="cccab-531">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="cccab-532">Im Folgenden finden Sie eine Beispielantwort auf die Preflight-Anforderung (vorausgesetzt, der Server lässt die Anforderung zu):</span><span class="sxs-lookup"><span data-stu-id="cccab-532">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="cccab-533">Die Antwort `Access-Control-Allow-Methods` enthält einen Header, der die `Access-Control-Allow-Headers` zulässigen Methoden auflistet, und optional einen Header, der die zulässigen Header auflistet.</span><span class="sxs-lookup"><span data-stu-id="cccab-533">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="cccab-534">Wenn die Preflight-Anforderung erfolgreich ist, sendet der Browser die eigentliche Anforderung.</span><span class="sxs-lookup"><span data-stu-id="cccab-534">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="cccab-535">Wenn die Preflight-Anforderung abgelehnt wird, gibt die App eine *200 OK-Antwort* zurück, sendet die CORS-Header jedoch nicht zurück.</span><span class="sxs-lookup"><span data-stu-id="cccab-535">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="cccab-536">Daher versucht der Browser nicht, die ursprungsübergreifende Anforderung zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="cccab-536">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="cccab-537">Festlegen der Ablaufzeit vor dem Flug</span><span class="sxs-lookup"><span data-stu-id="cccab-537">Set the preflight expiration time</span></span>

<span data-ttu-id="cccab-538">Der `Access-Control-Max-Age` Header gibt an, wie lange die Antwort auf die Preflight-Anforderung zwischengespeichert werden kann.</span><span class="sxs-lookup"><span data-stu-id="cccab-538">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="cccab-539">Um diesen Header <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>festzulegen, rufen Sie :</span><span class="sxs-lookup"><span data-stu-id="cccab-539">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="cccab-540">Funktionsweise von CORS</span><span class="sxs-lookup"><span data-stu-id="cccab-540">How CORS works</span></span>

<span data-ttu-id="cccab-541">In diesem Abschnitt wird beschrieben, was in einer [CORS-Anforderung](https://developer.mozilla.org/docs/Web/HTTP/CORS) auf der Ebene der HTTP-Nachrichten geschieht.</span><span class="sxs-lookup"><span data-stu-id="cccab-541">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="cccab-542">CORS ist **kein** Sicherheitsfeature.</span><span class="sxs-lookup"><span data-stu-id="cccab-542">CORS is **not** a security feature.</span></span> <span data-ttu-id="cccab-543">CORS ist ein W3C-Standard, der es einem Server ermöglicht, die Richtlinie mit demselben Ursprung zu entspannen.</span><span class="sxs-lookup"><span data-stu-id="cccab-543">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="cccab-544">Beispielsweise könnte ein böswilliger Akteur [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) für Ihre Website verhindern und eine websiteübergreifende Anforderung an seine CORS-fähige Website ausführen, um Informationen zu stehlen.</span><span class="sxs-lookup"><span data-stu-id="cccab-544">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="cccab-545">Ihre API ist nicht sicherer, wenn CORS zugelassen wird.</span><span class="sxs-lookup"><span data-stu-id="cccab-545">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="cccab-546">Es liegt am Client (Browser), CORS zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="cccab-546">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="cccab-547">Der Server führt die Anforderung aus und gibt die Antwort zurück, es ist der Client, der einen Fehler zurückgibt und die Antwort blockiert.</span><span class="sxs-lookup"><span data-stu-id="cccab-547">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="cccab-548">Eines der folgenden Tools zeigt z. B. die Serverantwort an:</span><span class="sxs-lookup"><span data-stu-id="cccab-548">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="cccab-549">Fiddler</span><span class="sxs-lookup"><span data-stu-id="cccab-549">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="cccab-550">Postman</span><span class="sxs-lookup"><span data-stu-id="cccab-550">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="cccab-551">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="cccab-551">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="cccab-552">Ein Webbrowser, indem er die URL in die Adressleiste eingibt.</span><span class="sxs-lookup"><span data-stu-id="cccab-552">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="cccab-553">Es ist eine Möglichkeit für einen Server, Browsern die Ausführung einer [ursprungsübergreifenden XHR-](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) oder [Fetch-API-Anforderung](https://developer.mozilla.org/docs/Web/API/Fetch_API) zu ermöglichen, die andernfalls verboten wäre.</span><span class="sxs-lookup"><span data-stu-id="cccab-553">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="cccab-554">Browser (ohne CORS) können keine ursprungsübergreifenden Anforderungen erfüllen.</span><span class="sxs-lookup"><span data-stu-id="cccab-554">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="cccab-555">Vor CORS wurde [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) verwendet, um diese Einschränkung zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="cccab-555">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="cccab-556">JSONP verwendet XHR nicht, es `<script>` verwendet das Tag, um die Antwort zu empfangen.</span><span class="sxs-lookup"><span data-stu-id="cccab-556">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="cccab-557">Skripts dürfen ursprungsübergreifend geladen werden.</span><span class="sxs-lookup"><span data-stu-id="cccab-557">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="cccab-558">Die [CORS-Spezifikation](https://www.w3.org/TR/cors/) führte mehrere neue HTTP-Header ein, die ursprungsübergreifende Anforderungen ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="cccab-558">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="cccab-559">Wenn ein Browser CORS unterstützt, werden diese Header automatisch für ursprungsübergreifende Anforderungen festgelegt.</span><span class="sxs-lookup"><span data-stu-id="cccab-559">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="cccab-560">Benutzerdefinierter JavaScript-Code ist zum Aktivieren von CORS nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="cccab-560">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="cccab-561">Im Folgenden finden Sie ein Beispiel für eine ursprungsübergreifende Anforderung.</span><span class="sxs-lookup"><span data-stu-id="cccab-561">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="cccab-562">Der `Origin` Header stellt die Domäne der Site bereit, die die Anforderung stellt.</span><span class="sxs-lookup"><span data-stu-id="cccab-562">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="cccab-563">Der `Origin` Header ist erforderlich und muss sich vom Host unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="cccab-563">The `Origin` header is required and must be different from the host.</span></span>

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

<span data-ttu-id="cccab-564">Wenn der Server die Anforderung `Access-Control-Allow-Origin` zulässt, legt er den Header in der Antwort fest.</span><span class="sxs-lookup"><span data-stu-id="cccab-564">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="cccab-565">Der Wert dieses Headers `Origin` entspricht entweder dem Header `"*"`aus der Anforderung oder ist der Platzhalterwert , was bedeutet, dass jeder Ursprung zulässig ist:</span><span class="sxs-lookup"><span data-stu-id="cccab-565">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="cccab-566">Wenn die Antwort den `Access-Control-Allow-Origin` Header nicht enthält, schlägt die ursprungsübergreifende Anforderung fehl.</span><span class="sxs-lookup"><span data-stu-id="cccab-566">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="cccab-567">Insbesondere lässt der Browser die Anforderung nicht zu.</span><span class="sxs-lookup"><span data-stu-id="cccab-567">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="cccab-568">Selbst wenn der Server eine erfolgreiche Antwort zurückgibt, stellt der Browser die Antwort nicht für die Client-App bereit.</span><span class="sxs-lookup"><span data-stu-id="cccab-568">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="cccab-569">Testen von CORS</span><span class="sxs-lookup"><span data-stu-id="cccab-569">Test CORS</span></span>

<span data-ttu-id="cccab-570">So testen Sie CORS:</span><span class="sxs-lookup"><span data-stu-id="cccab-570">To test CORS:</span></span>

1. <span data-ttu-id="cccab-571">[Erstellen Sie ein API-Projekt](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="cccab-571">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="cccab-572">Alternativ können Sie [das Beispiel herunterladen.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors)</span><span class="sxs-lookup"><span data-stu-id="cccab-572">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="cccab-573">Aktivieren Sie CORS mithilfe eines der Ansätze in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="cccab-573">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="cccab-574">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cccab-574">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="cccab-575">`WithOrigins("https://localhost:<port>");`sollte nur zum Testen einer Beispiel-App verwendet werden, die dem [Download-Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)ähnelt.</span><span class="sxs-lookup"><span data-stu-id="cccab-575">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="cccab-576">Erstellen Sie ein Web-App-Projekt (Razor Pages oder MVC).</span><span class="sxs-lookup"><span data-stu-id="cccab-576">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="cccab-577">Im Beispiel werden Razor-Seiten verwendet.</span><span class="sxs-lookup"><span data-stu-id="cccab-577">The sample uses Razor Pages.</span></span> <span data-ttu-id="cccab-578">Sie können die Web-App in derselben Lösung wie das API-Projekt erstellen.</span><span class="sxs-lookup"><span data-stu-id="cccab-578">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="cccab-579">Fügen Sie der Datei *Index.cshtml* den folgenden hervorgehobenen Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="cccab-579">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="cccab-580">Ersetzen Sie im `url: 'https://<web app>.azurewebsites.net/api/values/1',` vorherigen Code durch die URL zur bereitgestellten App.</span><span class="sxs-lookup"><span data-stu-id="cccab-580">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="cccab-581">Stellen Sie das API-Projekt bereit.</span><span class="sxs-lookup"><span data-stu-id="cccab-581">Deploy the API project.</span></span> <span data-ttu-id="cccab-582">Stellen Sie z. [B. in Azure bereit.](xref:host-and-deploy/azure-apps/index)</span><span class="sxs-lookup"><span data-stu-id="cccab-582">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="cccab-583">Führen Sie die Razor Pages oder MVC-App vom Desktop aus aus und klicken Sie auf die Schaltfläche **Testen.**</span><span class="sxs-lookup"><span data-stu-id="cccab-583">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="cccab-584">Verwenden Sie die F12-Tools, um Fehlermeldungen zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="cccab-584">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="cccab-585">Entfernen Sie den `WithOrigins` Ursprung des lokalen Hosts aus, und stellen Sie die App bereit.</span><span class="sxs-lookup"><span data-stu-id="cccab-585">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="cccab-586">Alternativ können Sie die Client-App mit einem anderen Port ausführen.</span><span class="sxs-lookup"><span data-stu-id="cccab-586">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="cccab-587">Führen Sie z. B. von Visual Studio aus.</span><span class="sxs-lookup"><span data-stu-id="cccab-587">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="cccab-588">Testen Sie mit der Client-App.</span><span class="sxs-lookup"><span data-stu-id="cccab-588">Test with the client app.</span></span> <span data-ttu-id="cccab-589">CORS-Fehler geben einen Fehler zurück, aber die Fehlermeldung ist für JavaScript nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="cccab-589">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="cccab-590">Verwenden Sie die Registerkarte Konsole in den F12-Tools, um den Fehler anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="cccab-590">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="cccab-591">Je nach Browser erhalten Sie einen Fehler (in der F12-Tools-Konsole), der den folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="cccab-591">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="cccab-592">Verwenden von Microsoft Edge:</span><span class="sxs-lookup"><span data-stu-id="cccab-592">Using Microsoft Edge:</span></span>

     <span data-ttu-id="cccab-593">**SEC7120: [CORS] `https://localhost:44375` Der Ursprung `https://localhost:44375` wurde im Access-Control-Allow-Origin-Antwortheader für ursprungsübergreifende Ressource unter`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="cccab-593">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="cccab-594">Verwenden von Chrome:</span><span class="sxs-lookup"><span data-stu-id="cccab-594">Using Chrome:</span></span>

     <span data-ttu-id="cccab-595">**Der Zugriff auf `https://webapi.azurewebsites.net/api/values/1` XMLHttpRequest vom Ursprung `https://localhost:44375` wurde durch die CORS-Richtlinie blockiert: In der angeforderten Ressource ist kein 'Access-Control-Allow-Origin'-Header vorhanden.**</span><span class="sxs-lookup"><span data-stu-id="cccab-595">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="cccab-596">CORS-fähige Endpunkte können mit einem Tool getestet werden, z. B. [Fiddler](https://www.telerik.com/fiddler) oder [Postman](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="cccab-596">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="cccab-597">Bei Verwendung eines Tools muss der Ursprung `Origin` der vom Header angegebenen Anforderung vom Host, der die Anforderung empfängt, abweichen.</span><span class="sxs-lookup"><span data-stu-id="cccab-597">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="cccab-598">Wenn die Anforderung nicht *ursprungsübergreifend* ist, basiert `Origin` sie auf dem Wert des Headers:</span><span class="sxs-lookup"><span data-stu-id="cccab-598">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="cccab-599">CORS Middleware muss die Anforderung nicht verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="cccab-599">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="cccab-600">CORS-Header werden in der Antwort nicht zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="cccab-600">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="cccab-601">CORS in IIS</span><span class="sxs-lookup"><span data-stu-id="cccab-601">CORS in IIS</span></span>

<span data-ttu-id="cccab-602">Bei der Bereitstellung in IIS muss CORS vor der Windows-Authentifizierung ausgeführt werden, wenn der Server nicht für den anonymen Zugriff konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="cccab-602">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="cccab-603">Um dieses Szenario zu unterstützen, muss das [IIS CORS-Modul](https://www.iis.net/downloads/microsoft/iis-cors-module) für die App installiert und konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="cccab-603">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cccab-604">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="cccab-604">Additional resources</span></span>

* [<span data-ttu-id="cccab-605">Cross-Origin Resource Sharing (CORS)</span><span class="sxs-lookup"><span data-stu-id="cccab-605">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="cccab-606">Erste Schritte mit dem IIS CORS-Modul</span><span class="sxs-lookup"><span data-stu-id="cccab-606">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

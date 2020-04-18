---
title: Aktivieren von Cross-Origin-Anforderungen (CROSS-Origin Requests, CORS) in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie CORS als Standard ursprungsübergreifende Anforderungen in einer ASP.NET Core-App zugelassen oder abgelehnt werden kann.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
uid: security/cors
ms.openlocfilehash: 56a339d9018f619af38aecc6f4c2ff40c3c43d2f
ms.sourcegitcommit: 3d07e21868dafc503530ecae2cfa18a7490b58a6
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/18/2020
ms.locfileid: "81642700"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="4de31-103">Aktivieren von Cross-Origin-Anforderungen (CROSS-Origin Requests, CORS) in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4de31-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4de31-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und Kirk [Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="4de31-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="4de31-105">Dieser Artikel zeigt, wie CORS in einer ASP.NET Core-App aktiviert wird.</span><span class="sxs-lookup"><span data-stu-id="4de31-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="4de31-106">Die Browsersicherheit verhindert, dass eine Webseite Anforderungen an eine andere Domäne als die, die die Webseite bereitgestellt hat, stellt.</span><span class="sxs-lookup"><span data-stu-id="4de31-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="4de31-107">Diese Einschränkung wird als *Richtlinie mit gleichem Ursprung*bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="4de31-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="4de31-108">Die Richtlinie mit demselben Ursprung verhindert, dass eine böswillige Website vertrauliche Daten von einer anderen Website liest.</span><span class="sxs-lookup"><span data-stu-id="4de31-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="4de31-109">Manchmal möchten Sie möglicherweise zulassen, dass andere Websites ursprungsübergreifende Anforderungen an Ihre App stellen.</span><span class="sxs-lookup"><span data-stu-id="4de31-109">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="4de31-110">Weitere Informationen finden Sie im [Mozilla CORS-Artikel](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="4de31-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="4de31-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="4de31-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="4de31-112">Ein W3C-Standard, der es einem Server ermöglicht, die Richtlinie mit demselben Ursprung zu entspannen.</span><span class="sxs-lookup"><span data-stu-id="4de31-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="4de31-113">Ist **kein** Sicherheitsmerkmal, CORS entspannt die Sicherheit.</span><span class="sxs-lookup"><span data-stu-id="4de31-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="4de31-114">Eine API ist nicht sicherer, wenn CORS zugelassen wird.</span><span class="sxs-lookup"><span data-stu-id="4de31-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="4de31-115">Weitere Informationen finden Sie unter Funktionsweise von [CORS](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="4de31-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="4de31-116">Ermöglicht einem Server, einige ursprungsübergreifende Anforderungen explizit zuzulassen, während andere abgelehnt werden.</span><span class="sxs-lookup"><span data-stu-id="4de31-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="4de31-117">Ist sicherer und flexibler als frühere Techniken, wie z. B. [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="4de31-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="4de31-118">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4de31-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="4de31-119">Gleicher Ursprung</span><span class="sxs-lookup"><span data-stu-id="4de31-119">Same origin</span></span>

<span data-ttu-id="4de31-120">Zwei URLs haben denselben Ursprung, wenn sie identische Schemata, Hosts und Ports haben ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="4de31-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="4de31-121">Diese beiden URLs haben den gleichen Ursprung:</span><span class="sxs-lookup"><span data-stu-id="4de31-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="4de31-122">Diese URLs haben andere Ursprünge als die beiden vorherigen URLs:</span><span class="sxs-lookup"><span data-stu-id="4de31-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="4de31-123">`https://example.net`&ndash; Verschiedene Domäne</span><span class="sxs-lookup"><span data-stu-id="4de31-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="4de31-124">`https://www.example.com/foo.html`&ndash; Verschiedene Subdomain</span><span class="sxs-lookup"><span data-stu-id="4de31-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="4de31-125">`http://example.com/foo.html`&ndash; Unterschiedlicheregelung</span><span class="sxs-lookup"><span data-stu-id="4de31-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="4de31-126">`https://example.com:9000/foo.html`&ndash; Verschiedene randder Hafen</span><span class="sxs-lookup"><span data-stu-id="4de31-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="4de31-127">Aktivieren von CORS</span><span class="sxs-lookup"><span data-stu-id="4de31-127">Enable CORS</span></span>

<span data-ttu-id="4de31-128">Es gibt drei Möglichkeiten, CORS zu aktivieren:</span><span class="sxs-lookup"><span data-stu-id="4de31-128">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="4de31-129">In Middleware mit einer [benannten Richtlinie](#np) oder [Standardrichtlinie](#dp).</span><span class="sxs-lookup"><span data-stu-id="4de31-129">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="4de31-130">Verwenden von [Endpunktrouting](#ecors).</span><span class="sxs-lookup"><span data-stu-id="4de31-130">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="4de31-131">Mit dem [Attribut [EnableCors].](#attr)</span><span class="sxs-lookup"><span data-stu-id="4de31-131">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="4de31-132">Die Verwendung des [[EnableCors]-Attributs](#attr) mit einer benannten Richtlinie bietet die beste Kontrolle beim Einschränken von Endpunkten, die CORS unterstützen.</span><span class="sxs-lookup"><span data-stu-id="4de31-132">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

<span data-ttu-id="4de31-133">Jeder Ansatz wird in den folgenden Abschnitten ausführlich beschrieben.</span><span class="sxs-lookup"><span data-stu-id="4de31-133">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="4de31-134">CORS mit benannter Richtlinie und Middleware</span><span class="sxs-lookup"><span data-stu-id="4de31-134">CORS with named policy and middleware</span></span>

<span data-ttu-id="4de31-135">CORS Middleware verarbeitet ursprungsübergreifende Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="4de31-135">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="4de31-136">Der folgende Code wendet eine CORS-Richtlinie auf alle Endpunkte der App mit den angegebenen Ursprüngen an:</span><span class="sxs-lookup"><span data-stu-id="4de31-136">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

<span data-ttu-id="4de31-137">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="4de31-137">The preceding code:</span></span>

* <span data-ttu-id="4de31-138">Legt den Richtliniennamen auf fest. `_myAllowSpecificOrigins`</span><span class="sxs-lookup"><span data-stu-id="4de31-138">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="4de31-139">Der Richtlinienname ist willkürlich.</span><span class="sxs-lookup"><span data-stu-id="4de31-139">The policy name is arbitrary.</span></span>
* <span data-ttu-id="4de31-140">Ruft <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> die Erweiterungsmethode `_myAllowSpecificOrigins` auf und gibt die CORS-Richtlinie an.</span><span class="sxs-lookup"><span data-stu-id="4de31-140">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="4de31-141">`UseCors`fügt die CORS-Middleware hinzu.</span><span class="sxs-lookup"><span data-stu-id="4de31-141">`UseCors` adds the CORS middleware.</span></span> <span data-ttu-id="4de31-142">Der Aufruf `UseCors` an muss `UseRouting`nach `UseAuthorization`platziert werden, aber vor .</span><span class="sxs-lookup"><span data-stu-id="4de31-142">The call to `UseCors` must be placed after `UseRouting`, but before `UseAuthorization`.</span></span> <span data-ttu-id="4de31-143">Weitere Informationen finden Sie unter [Middleware-Bestellung](xref:fundamentals/middleware/index#middleware-order).</span><span class="sxs-lookup"><span data-stu-id="4de31-143">For more information, see [Middleware order](xref:fundamentals/middleware/index#middleware-order).</span></span>
* <span data-ttu-id="4de31-144">Aufrufe <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> mit einem [Lambda-Ausdruck](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="4de31-144">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="4de31-145">Der Lambda <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> nimmt ein Objekt.</span><span class="sxs-lookup"><span data-stu-id="4de31-145">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="4de31-146">[Konfigurationsoptionen](#cors-policy-options), `WithOrigins`z. B. , werden weiter unten in diesem Artikel beschrieben.</span><span class="sxs-lookup"><span data-stu-id="4de31-146">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="4de31-147">Aktiviert `_myAllowSpecificOrigins` die CORS-Richtlinie für alle Controllerendpunkte.</span><span class="sxs-lookup"><span data-stu-id="4de31-147">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="4de31-148">Siehe [Endpunktrouting,](#ecors) um eine CORS-Richtlinie auf bestimmte Endpunkte anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="4de31-148">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>

<span data-ttu-id="4de31-149">Beim Endpunktrouting ***muss*** die CORS-Middleware so konfiguriert `UseRouting` werden, dass sie zwischen den Aufrufen von und `UseEndpoints`ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="4de31-149">With endpoint routing, the CORS middleware ***must*** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="4de31-150">Anweisungen zum Testen von Code ähnlich dem vorherigen Code finden Sie unter [Test-CORS.](#testc)</span><span class="sxs-lookup"><span data-stu-id="4de31-150">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="4de31-151">Der <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> Methodenaufruf fügt DEM Dienstcontainer der App CORS-Dienste hinzu:</span><span class="sxs-lookup"><span data-stu-id="4de31-151">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="4de31-152">Weitere Informationen finden Sie unter [CORS-Richtlinienoptionen](#cpo) in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="4de31-152">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="4de31-153">Die <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> Methoden können verkettet werden, wie im folgenden Code gezeigt:</span><span class="sxs-lookup"><span data-stu-id="4de31-153">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="4de31-154">Hinweis: Die angegebene URL darf **keinen** nachgestellten Schrägstrich (`/`) enthalten.</span><span class="sxs-lookup"><span data-stu-id="4de31-154">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="4de31-155">Wenn die URL `/`mit beendet `false` wird, wird der Vergleich zurückgegeben, und es wird kein Header zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="4de31-155">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="4de31-156">CORS mit Standardrichtlinie und Middleware</span><span class="sxs-lookup"><span data-stu-id="4de31-156">CORS with default policy and middleware</span></span>

<span data-ttu-id="4de31-157">Der folgende hervorgehobene Code aktiviert die Standardmäßige CORS-Richtlinie:</span><span class="sxs-lookup"><span data-stu-id="4de31-157">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="4de31-158">Der vorherige Code wendet die Standard-CORS-Richtlinie auf alle Controllerendpunkte an.</span><span class="sxs-lookup"><span data-stu-id="4de31-158">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="4de31-159">Aktivieren von Cors mit Endpunktrouting</span><span class="sxs-lookup"><span data-stu-id="4de31-159">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="4de31-160">Das Aktivieren von CORS pro `RequireCors` Endpunkt mithilfe der aktuellen Verwendung unterstützt ***keine*** [automatischen Preflight-Anforderungen](#apf).</span><span class="sxs-lookup"><span data-stu-id="4de31-160">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="4de31-161">Weitere Informationen finden Sie unter [dieses GitHub-Problem](https://github.com/dotnet/aspnetcore/issues/20709) und Testen von [CORS mit Endpunktrouting und [HttpOptions]](#tcer).</span><span class="sxs-lookup"><span data-stu-id="4de31-161">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="4de31-162">Mit Endpunktrouting kann CORS pro Endpunkt mithilfe der <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> Erweiterungsmethoden aktiviert werden:</span><span class="sxs-lookup"><span data-stu-id="4de31-162">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="4de31-163">Für den Code oben gilt:</span><span class="sxs-lookup"><span data-stu-id="4de31-163">In the preceding code:</span></span>

* <span data-ttu-id="4de31-164">`app.UseCors`ermöglicht die CORS Middleware.</span><span class="sxs-lookup"><span data-stu-id="4de31-164">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="4de31-165">Da eine Standardrichtlinie nicht konfiguriert wurde, `app.UseCors()` aktiviert ALLEIN CORS nicht.</span><span class="sxs-lookup"><span data-stu-id="4de31-165">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="4de31-166">Die `/echo` und controller-Endpunkte erlauben ursprungsübergreifende Anforderungen mithilfe der angegebenen Richtlinie.</span><span class="sxs-lookup"><span data-stu-id="4de31-166">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="4de31-167">Die `/echo2` und Razor Pages-Endpunkte lassen ***keine*** ursprungsübergreifenden Anforderungen zu, da keine Standardrichtlinie angegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="4de31-167">The `/echo2` and Razor Pages endpoints do ***not*** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="4de31-168">Das [Attribut [DisableCors]](#dc) deaktiviert CORS ***nicht,*** das durch `RequireCors`Endpunktrouting mit aktiviert wurde.</span><span class="sxs-lookup"><span data-stu-id="4de31-168">The [[DisableCors]](#dc) attribute does ***not***  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="4de31-169">Anweisungen zum Testen von Code ähnlich dem vorherigen finden Sie unter [Test-CORS mit Endpunktrouting und [HttpOptions].](#tcer)</span><span class="sxs-lookup"><span data-stu-id="4de31-169">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="4de31-170">CORS mit Attributen aktivieren</span><span class="sxs-lookup"><span data-stu-id="4de31-170">Enable CORS with attributes</span></span>

<span data-ttu-id="4de31-171">Das Aktivieren von CORS mit dem [Attribut [EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) und das Anwenden einer benannten Richtlinie auf die Endpunkte, die CORS erfordern, bietet die beste Kontrolle.</span><span class="sxs-lookup"><span data-stu-id="4de31-171">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="4de31-172">Das [Attribut [EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) bietet eine Alternative zum globalen Anwenden von CORS.</span><span class="sxs-lookup"><span data-stu-id="4de31-172">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="4de31-173">Das `[EnableCors]` Attribut aktiviert CORS für ausgewählte Endpunkte und nicht für alle Endpunkte:</span><span class="sxs-lookup"><span data-stu-id="4de31-173">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="4de31-174">`[EnableCors]`gibt die Standardrichtlinie an.</span><span class="sxs-lookup"><span data-stu-id="4de31-174">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="4de31-175">`[EnableCors("{Policy String}")]`gibt eine benannte Richtlinie an.</span><span class="sxs-lookup"><span data-stu-id="4de31-175">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="4de31-176">Das `[EnableCors]` Attribut kann angewendet werden auf:</span><span class="sxs-lookup"><span data-stu-id="4de31-176">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="4de31-177">Razor Page`PageModel`</span><span class="sxs-lookup"><span data-stu-id="4de31-177">Razor Page `PageModel`</span></span>
* <span data-ttu-id="4de31-178">Controller</span><span class="sxs-lookup"><span data-stu-id="4de31-178">Controller</span></span>
* <span data-ttu-id="4de31-179">Controller-Aktionsmethode</span><span class="sxs-lookup"><span data-stu-id="4de31-179">Controller action method</span></span>

<span data-ttu-id="4de31-180">Verschiedene Richtlinien können auf Controller, Seitenmodelle oder `[EnableCors]` Aktionsmethoden mit dem Attribut angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="4de31-180">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="4de31-181">Wenn `[EnableCors]` das Attribut auf einen Controller, ein Seitenmodell oder eine Aktionsmethode angewendet wird und CORS in Middleware aktiviert ist, werden ***beide*** Richtlinien angewendet.</span><span class="sxs-lookup"><span data-stu-id="4de31-181">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="4de31-182">***Wir empfehlen, Richtlinien nicht zu kombinieren. Verwenden Sie das*** `[EnableCors]` Attribut oder die ***Middleware, nicht beide in derselben App.***</span><span class="sxs-lookup"><span data-stu-id="4de31-182">***We recommend against combining policies. Use the*** `[EnableCors]` ***attribute or middleware, not both in the same app.***</span></span>

<span data-ttu-id="4de31-183">Der folgende Code wendet für jede Methode eine andere Richtlinie an:</span><span class="sxs-lookup"><span data-stu-id="4de31-183">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="4de31-184">Der folgende Code erstellt zwei CORS-Richtlinien:</span><span class="sxs-lookup"><span data-stu-id="4de31-184">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="4de31-185">Für die beste Kontrolle der Begrenzung von CORS-Anforderungen:</span><span class="sxs-lookup"><span data-stu-id="4de31-185">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="4de31-186">Wird `[EnableCors("MyPolicy")]` mit einer benannten Richtlinie verwendet.</span><span class="sxs-lookup"><span data-stu-id="4de31-186">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="4de31-187">Definieren Sie keine Standardrichtlinie.</span><span class="sxs-lookup"><span data-stu-id="4de31-187">Don't define a default policy.</span></span>
* <span data-ttu-id="4de31-188">Verwenden Sie kein [Endpunktrouting](#ecors).</span><span class="sxs-lookup"><span data-stu-id="4de31-188">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="4de31-189">Der Code im nächsten Abschnitt entspricht der vorherigen Liste.</span><span class="sxs-lookup"><span data-stu-id="4de31-189">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="4de31-190">Anweisungen zum Testen von Code ähnlich dem vorherigen Code finden Sie unter [Test-CORS.](#testc)</span><span class="sxs-lookup"><span data-stu-id="4de31-190">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="4de31-191">DEAKTIVIEREN von CORS</span><span class="sxs-lookup"><span data-stu-id="4de31-191">Disable CORS</span></span>

<span data-ttu-id="4de31-192">Das [Attribut [DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) deaktiviert CORS ***nicht,*** das durch [Endpunktrouting](#ecors)aktiviert wurde.</span><span class="sxs-lookup"><span data-stu-id="4de31-192">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does ***not***  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="4de31-193">Der folgende Code definiert die `"MyPolicy"`CORS-Richtlinie:</span><span class="sxs-lookup"><span data-stu-id="4de31-193">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="4de31-194">Der folgende Code deaktiviert CORS für die `GetValues2` Aktion:</span><span class="sxs-lookup"><span data-stu-id="4de31-194">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="4de31-195">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="4de31-195">The preceding code:</span></span>

* <span data-ttu-id="4de31-196">Aktiviert CORS nicht mit [Endpunktrouting](#ecors).</span><span class="sxs-lookup"><span data-stu-id="4de31-196">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="4de31-197">Definiert keine [CORS-Standardrichtlinie](#dp).</span><span class="sxs-lookup"><span data-stu-id="4de31-197">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="4de31-198">Verwendet [[EnableCors("MyPolicy")],](#attr) `"MyPolicy"` um die CORS-Richtlinie für den Controller zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="4de31-198">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="4de31-199">Deaktiviert CORS für `GetValues2` die Methode.</span><span class="sxs-lookup"><span data-stu-id="4de31-199">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="4de31-200">Anweisungen zum Testen des vorherigen Codes finden Sie unter [Test-CORS.](#testc)</span><span class="sxs-lookup"><span data-stu-id="4de31-200">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="4de31-201">CORS-Richtlinienoptionen</span><span class="sxs-lookup"><span data-stu-id="4de31-201">CORS policy options</span></span>

<span data-ttu-id="4de31-202">In diesem Abschnitt werden die verschiedenen Optionen beschrieben, die in einer CORS-Richtlinie festgelegt werden können:</span><span class="sxs-lookup"><span data-stu-id="4de31-202">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="4de31-203">Festlegen der zulässigen Ursprünge</span><span class="sxs-lookup"><span data-stu-id="4de31-203">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="4de31-204">Festlegen der zulässigen HTTP-Methoden</span><span class="sxs-lookup"><span data-stu-id="4de31-204">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="4de31-205">Festlegen der zulässigen Anforderungsheader</span><span class="sxs-lookup"><span data-stu-id="4de31-205">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="4de31-206">Festlegen der freiliegenden Antwortheader</span><span class="sxs-lookup"><span data-stu-id="4de31-206">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="4de31-207">Anmeldeinformationen in ursprungsübergreifenden Anforderungen</span><span class="sxs-lookup"><span data-stu-id="4de31-207">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="4de31-208">Festlegen der Ablaufzeit vor dem Flug</span><span class="sxs-lookup"><span data-stu-id="4de31-208">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="4de31-209"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>wird in `Startup.ConfigureServices`aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="4de31-209"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="4de31-210">Bei einigen Optionen kann es hilfreich sein, zuerst den Abschnitt ["Funktionsweise von CORS"](#how-cors) zu lesen.</span><span class="sxs-lookup"><span data-stu-id="4de31-210">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="4de31-211">Festlegen der zulässigen Ursprünge</span><span class="sxs-lookup"><span data-stu-id="4de31-211">Set the allowed origins</span></span>

<span data-ttu-id="4de31-212"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Ermöglicht CORS-Anforderungen aller Herkunftsherkunft mit einem beliebigen Schema (`http` oder `https`).</span><span class="sxs-lookup"><span data-stu-id="4de31-212"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="4de31-213">`AllowAnyOrigin`ist unsicher, da *jede Website* ursprungsübergreifende Anforderungen an die App stellen kann.</span><span class="sxs-lookup"><span data-stu-id="4de31-213">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="4de31-214">Angeben `AllowAnyOrigin` und `AllowCredentials` ist eine unsichere Konfiguration und kann zu standortübergreifenden Anforderungsfälschungen führen.</span><span class="sxs-lookup"><span data-stu-id="4de31-214">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="4de31-215">Der CORS-Dienst gibt eine ungültige CORS-Antwort zurück, wenn eine App mit beiden Methoden konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="4de31-215">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="4de31-216">`AllowAnyOrigin`wirkt sich auf `Access-Control-Allow-Origin` Preflight-Anforderungen und den Header aus.</span><span class="sxs-lookup"><span data-stu-id="4de31-216">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="4de31-217">Weitere Informationen finden Sie im Abschnitt [Preflight-Anforderungen.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="4de31-217">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="4de31-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Legt <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> die Eigenschaft der Richtlinie als funktion fest, die es dem Ursprung ermöglicht, eine konfigurierte Platzhalterdomäne abzugleichen, wenn ausgewertet wird, ob der Ursprung zulässig ist.</span><span class="sxs-lookup"><span data-stu-id="4de31-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="4de31-219">Festlegen der zulässigen HTTP-Methoden</span><span class="sxs-lookup"><span data-stu-id="4de31-219">Set the allowed HTTP methods</span></span>

<span data-ttu-id="4de31-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="4de31-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="4de31-221">Erlaubt jede HTTP-Methode:</span><span class="sxs-lookup"><span data-stu-id="4de31-221">Allows any HTTP method:</span></span>
* <span data-ttu-id="4de31-222">Beeinflusst Preflight-Anforderungen `Access-Control-Allow-Methods` und den Header.</span><span class="sxs-lookup"><span data-stu-id="4de31-222">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="4de31-223">Weitere Informationen finden Sie im Abschnitt [Preflight-Anforderungen.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="4de31-223">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="4de31-224">Festlegen der zulässigen Anforderungsheader</span><span class="sxs-lookup"><span data-stu-id="4de31-224">Set the allowed request headers</span></span>

<span data-ttu-id="4de31-225">Um das Senden bestimmter Header in einer CORS-Anforderung zuzulassen, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> rufen Sie die [zulässigen](https://xhr.spec.whatwg.org/#request)Header an, um das Senden bestimmter Header in einer CORS-Anforderung zu ermöglichen:</span><span class="sxs-lookup"><span data-stu-id="4de31-225">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="4de31-226">Um alle [Autorenanforderungsheader](https://www.w3.org/TR/cors/#author-request-headers) <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>zuzulassen, rufen Sie :</span><span class="sxs-lookup"><span data-stu-id="4de31-226">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="4de31-227">`AllowAnyHeader`wirkt sich auf Preflight-Anforderungen und den [Access-Control-Request-Headers-Header aus.](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method)</span><span class="sxs-lookup"><span data-stu-id="4de31-227">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="4de31-228">Weitere Informationen finden Sie im Abschnitt [Preflight-Anforderungen.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="4de31-228">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="4de31-229">Eine CORS Middleware-Richtlinie, die `WithHeaders` mit bestimmten Headern übereinstimmt, `Access-Control-Request-Headers` die von angegeben `WithHeaders`werden, ist nur möglich, wenn die in .</span><span class="sxs-lookup"><span data-stu-id="4de31-229">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="4de31-230">Betrachten Sie beispielsweise eine App, die wie folgt konfiguriert ist:</span><span class="sxs-lookup"><span data-stu-id="4de31-230">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="4de31-231">CORS Middleware lehnt eine Preflight-Anforderung mit `Content-Language` dem folgenden Anforderungsheader ab, da `WithHeaders`([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) nicht in aufgeführt ist:</span><span class="sxs-lookup"><span data-stu-id="4de31-231">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="4de31-232">Die App gibt eine *200 OK-Antwort* zurück, sendet die CORS-Header jedoch nicht zurück.</span><span class="sxs-lookup"><span data-stu-id="4de31-232">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="4de31-233">Daher versucht der Browser nicht, die ursprungsübergreifende Anforderung zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="4de31-233">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="4de31-234">Festlegen der freiliegenden Antwortheader</span><span class="sxs-lookup"><span data-stu-id="4de31-234">Set the exposed response headers</span></span>

<span data-ttu-id="4de31-235">Standardmäßig macht der Browser nicht alle Antwortheader für die App verfügbar.</span><span class="sxs-lookup"><span data-stu-id="4de31-235">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="4de31-236">Weitere Informationen finden Sie unter [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="4de31-236">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="4de31-237">Die standardmäßig verfügbaren Antwortheader sind:</span><span class="sxs-lookup"><span data-stu-id="4de31-237">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="4de31-238">Die CORS-Spezifikation ruft diese Header *auf einfache Antwortheader*.</span><span class="sxs-lookup"><span data-stu-id="4de31-238">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="4de31-239">Um andere Header für die App <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>verfügbar zu machen, rufen Sie :</span><span class="sxs-lookup"><span data-stu-id="4de31-239">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="4de31-240">Anmeldeinformationen in ursprungsübergreifenden Anforderungen</span><span class="sxs-lookup"><span data-stu-id="4de31-240">Credentials in cross-origin requests</span></span>

<span data-ttu-id="4de31-241">Anmeldeinformationen erfordern eine spezielle Behandlung in einer CORS-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="4de31-241">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="4de31-242">Standardmäßig sendet der Browser keine Anmeldeinformationen mit einer ursprungsübergreifenden Anforderung.</span><span class="sxs-lookup"><span data-stu-id="4de31-242">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="4de31-243">Anmeldeinformationen umfassen Cookies und HTTP-Authentifizierungsschemata.</span><span class="sxs-lookup"><span data-stu-id="4de31-243">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="4de31-244">Um Anmeldeinformationen mit einer ursprungsübergreifenden Anforderung `XMLHttpRequest.withCredentials` zu `true`senden, muss der Client auf festlegen.</span><span class="sxs-lookup"><span data-stu-id="4de31-244">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="4de31-245">Direkt `XMLHttpRequest` verwenden:</span><span class="sxs-lookup"><span data-stu-id="4de31-245">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="4de31-246">Verwenden von jQuery:</span><span class="sxs-lookup"><span data-stu-id="4de31-246">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="4de31-247">Verwenden der [Abruf-API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="4de31-247">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="4de31-248">Der Server muss die Anmeldeinformationen zulassen.</span><span class="sxs-lookup"><span data-stu-id="4de31-248">The server must allow the credentials.</span></span> <span data-ttu-id="4de31-249">Um ursprungsübergreifende Anmeldeinformationen zuzulassen, rufen Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span><span class="sxs-lookup"><span data-stu-id="4de31-249">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="4de31-250">Die HTTP-Antwort `Access-Control-Allow-Credentials` enthält einen Header, der dem Browser mitteilt, dass der Server Anmeldeinformationen für eine ursprungsübergreifende Anforderung zulässt.</span><span class="sxs-lookup"><span data-stu-id="4de31-250">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="4de31-251">Wenn der Browser Anmeldeinformationen sendet, die Antwort `Access-Control-Allow-Credentials` jedoch keinen gültigen Header enthält, macht der Browser die Antwort für die App nicht verfügbar, und die ursprungsübergreifende Anforderung schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="4de31-251">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="4de31-252">Das Zulassen ursprungsübergreifender Anmeldeinformationen stellt ein Sicherheitsrisiko dar.</span><span class="sxs-lookup"><span data-stu-id="4de31-252">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="4de31-253">Eine Website in einer anderen Domäne kann die Anmeldeinformationen eines angemeldeten Benutzers im Namen des Benutzers ohne Wissen des Benutzers an die App senden.</span><span class="sxs-lookup"><span data-stu-id="4de31-253">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="4de31-254">Die CORS-Spezifikation gibt auch `"*"` an, dass das Festlegen `Access-Control-Allow-Credentials` von Ursprüngen auf (alle Ursprünge) ungültig ist, wenn der Header vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="4de31-254">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="4de31-255">Preflight-Anfragen</span><span class="sxs-lookup"><span data-stu-id="4de31-255">Preflight requests</span></span>

<span data-ttu-id="4de31-256">Bei einigen CORS-Anforderungen sendet [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) der Browser eine zusätzliche OPTIONS-Anforderung, bevor er die eigentliche Anforderung stellt.</span><span class="sxs-lookup"><span data-stu-id="4de31-256">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="4de31-257">Diese Anforderung wird als [Preflight-Anforderung](https://developer.mozilla.org/docs/Glossary/Preflight_request)bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="4de31-257">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="4de31-258">Der Browser kann die Preflight-Anforderung überspringen, wenn ***alle*** folgenden Bedingungen erfüllt sind:</span><span class="sxs-lookup"><span data-stu-id="4de31-258">The browser can skip the preflight request if ***all*** the following conditions are true:</span></span>

* <span data-ttu-id="4de31-259">Die Anforderungsmethode ist GET, HEAD oder POST.</span><span class="sxs-lookup"><span data-stu-id="4de31-259">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="4de31-260">Die App stellt keine anderen Anforderungsheader `Content-Language` `Content-Type`als `Accept`, `Accept-Language`, , oder `Last-Event-ID`fest.</span><span class="sxs-lookup"><span data-stu-id="4de31-260">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="4de31-261">Der `Content-Type` Header, sofern festgelegt, hat einen der folgenden Werte:</span><span class="sxs-lookup"><span data-stu-id="4de31-261">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="4de31-262">Die Regel für Anforderungsheader, die für die Clientanforderung festgelegt `setRequestHeader` wurden, gilt für Header, die die App durch Aufrufen des `XMLHttpRequest` Objekts festlegt.</span><span class="sxs-lookup"><span data-stu-id="4de31-262">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="4de31-263">Die CORS-Spezifikation ruft diese [Header-Author-Anforderungsheader](https://www.w3.org/TR/cors/#author-request-headers)auf.</span><span class="sxs-lookup"><span data-stu-id="4de31-263">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="4de31-264">Die Regel gilt nicht für Header, die der `User-Agent` `Host`Browser `Content-Length`festlegen kann, z. B. , oder .</span><span class="sxs-lookup"><span data-stu-id="4de31-264">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="4de31-265">Im Folgenden finden Sie eine Beispielantwort, die der Preflight-Anforderung ähnelt, die von der Schaltfläche **[Test test]** im Abschnitt [Test CORS](#testc) dieses Dokuments gestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="4de31-265">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

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

<span data-ttu-id="4de31-266">Die Preflight-Anforderung verwendet die [HTTP OPTIONS-Methode.](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS)</span><span class="sxs-lookup"><span data-stu-id="4de31-266">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="4de31-267">Es kann die folgenden Header enthalten:</span><span class="sxs-lookup"><span data-stu-id="4de31-267">It may include the following headers:</span></span>

* <span data-ttu-id="4de31-268">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): Die HTTP-Methode, die für die eigentliche Anforderung verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="4de31-268">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="4de31-269">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): Eine Liste von Anforderungsheadern, die die App für die eigentliche Anforderung festlegt.</span><span class="sxs-lookup"><span data-stu-id="4de31-269">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="4de31-270">Wie bereits erwähnt, enthält dies keine Header, die `User-Agent`der Browser festlegt, z. B. .</span><span class="sxs-lookup"><span data-stu-id="4de31-270">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="4de31-271">Access-Control-Allow-Methoden</span><span class="sxs-lookup"><span data-stu-id="4de31-271">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="4de31-272">Wenn die Preflight-Anforderung abgelehnt wird, gibt die App eine `200 OK` Antwort zurück, legt jedoch die CORS-Header nicht fest.</span><span class="sxs-lookup"><span data-stu-id="4de31-272">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="4de31-273">Daher versucht der Browser nicht, die ursprungsübergreifende Anforderung zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="4de31-273">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="4de31-274">Ein Beispiel für eine abgelehnte Preflight-Anforderung finden Sie im Abschnitt [TEST CORS](#testc) dieses Dokuments.</span><span class="sxs-lookup"><span data-stu-id="4de31-274">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="4de31-275">Mit den F12-Tools zeigt die Konsolen-App je nach Browser einen ähnlichen Fehler wie einen der folgenden Optionen an:</span><span class="sxs-lookup"><span data-stu-id="4de31-275">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="4de31-276">Firefox: Cross-Origin-Anforderung blockiert: Die Richtlinie für denselben `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`Ursprung verbietet das Lesen der Remoteressource bei .</span><span class="sxs-lookup"><span data-stu-id="4de31-276">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="4de31-277">(Grund: CORS-Anforderung war nicht erfolgreich).</span><span class="sxs-lookup"><span data-stu-id="4de31-277">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="4de31-278">Weitere Informationen</span><span class="sxs-lookup"><span data-stu-id="4de31-278">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="4de31-279">Chromium-basiert: Der Zugriffhttps://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5auf abruf en ' from origin 'https://cors3.azurewebsites.netwurde durch die CORS-Richtlinie blockiert: Die Antwort auf die Preflight-Anforderung besteht keine Zugriffssteuerungsüberprüfung: Es ist kein 'Access-Control-Allow-Origin'-Header auf der angeforderten Ressource vorhanden.</span><span class="sxs-lookup"><span data-stu-id="4de31-279">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="4de31-280">Falls eine opake Antwort Ihre Anforderungen erfüllt, legen Sie den Modus der Anforderung auf 'no-cors' fest, um CORS für den Ressourcenabruf zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="4de31-280">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="4de31-281">Um bestimmte Header zuzulassen, rufen Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span><span class="sxs-lookup"><span data-stu-id="4de31-281">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="4de31-282">Um alle [Autorenanforderungsheader](https://www.w3.org/TR/cors/#author-request-headers) <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>zuzulassen, rufen Sie :</span><span class="sxs-lookup"><span data-stu-id="4de31-282">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="4de31-283">Browser sind nicht konsistent, wie `Access-Control-Request-Headers`sie festlegen.</span><span class="sxs-lookup"><span data-stu-id="4de31-283">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="4de31-284">Wenn beides:</span><span class="sxs-lookup"><span data-stu-id="4de31-284">If either:</span></span>

* <span data-ttu-id="4de31-285">Header werden auf etwas anderes als`"*"`</span><span class="sxs-lookup"><span data-stu-id="4de31-285">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="4de31-286"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>heißt: Schließen Sie `Accept` `Content-Type`mindestens `Origin`, und ein, sowie alle benutzerdefinierten Header, die Sie unterstützen möchten.</span><span class="sxs-lookup"><span data-stu-id="4de31-286"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="4de31-287">Automatischer Preflight-Anforderungscode</span><span class="sxs-lookup"><span data-stu-id="4de31-287">Automatic preflight request code</span></span>

<span data-ttu-id="4de31-288">Wenn die CORS-Richtlinie angewendet wird, wird:</span><span class="sxs-lookup"><span data-stu-id="4de31-288">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="4de31-289">Global durch `app.UseCors` Aufrufen `Startup.Configure`von .</span><span class="sxs-lookup"><span data-stu-id="4de31-289">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="4de31-290">Verwenden `[EnableCors]` des Attributs.</span><span class="sxs-lookup"><span data-stu-id="4de31-290">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="4de31-291">ASP.NET Core reagiert auf die Preflight OPTIONS-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="4de31-291">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="4de31-292">Das Aktivieren von CORS pro `RequireCors` Endpunkt mithilfe der aktuellen Verwendung unterstützt ***keine*** automatischen Preflight-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="4de31-292">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support automatic preflight requests.</span></span>

<span data-ttu-id="4de31-293">Der Abschnitt [TEST CORS](#testc) dieses Dokuments veranschaulicht dieses Verhalten.</span><span class="sxs-lookup"><span data-stu-id="4de31-293">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="4de31-294">[HttpOptions]-Attribut für Preflight-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="4de31-294">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="4de31-295">Wenn CORS mit der entsprechenden Richtlinie aktiviert ist, reagiert ASP.NET Core in der Regel automatisch auf CORS-Preflight-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="4de31-295">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="4de31-296">In einigen Szenarien ist dies möglicherweise nicht der Fall.</span><span class="sxs-lookup"><span data-stu-id="4de31-296">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="4de31-297">Verwenden von [CORS mit Endpunktrouting](#ecors).</span><span class="sxs-lookup"><span data-stu-id="4de31-297">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="4de31-298">Der folgende Code verwendet das [Attribut [HttpOptions],](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) um Endpunkte für OPTIONS-Anforderungen zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="4de31-298">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="4de31-299">Anweisungen zum Testen des vorherigen Codes finden Sie unter Testen von [CORS mit Endpunktrouting und [HttpOptions].](#tcer)</span><span class="sxs-lookup"><span data-stu-id="4de31-299">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="4de31-300">Festlegen der Ablaufzeit vor dem Flug</span><span class="sxs-lookup"><span data-stu-id="4de31-300">Set the preflight expiration time</span></span>

<span data-ttu-id="4de31-301">Der `Access-Control-Max-Age` Header gibt an, wie lange die Antwort auf die Preflight-Anforderung zwischengespeichert werden kann.</span><span class="sxs-lookup"><span data-stu-id="4de31-301">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="4de31-302">Um diesen Header <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>festzulegen, rufen Sie :</span><span class="sxs-lookup"><span data-stu-id="4de31-302">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="4de31-303">Funktionsweise von CORS</span><span class="sxs-lookup"><span data-stu-id="4de31-303">How CORS works</span></span>

<span data-ttu-id="4de31-304">In diesem Abschnitt wird beschrieben, was in einer [CORS-Anforderung](https://developer.mozilla.org/docs/Web/HTTP/CORS) auf der Ebene der HTTP-Nachrichten geschieht.</span><span class="sxs-lookup"><span data-stu-id="4de31-304">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="4de31-305">CORS ist **kein** Sicherheitsfeature.</span><span class="sxs-lookup"><span data-stu-id="4de31-305">CORS is **not** a security feature.</span></span> <span data-ttu-id="4de31-306">CORS ist ein W3C-Standard, der es einem Server ermöglicht, die Richtlinie mit demselben Ursprung zu entspannen.</span><span class="sxs-lookup"><span data-stu-id="4de31-306">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="4de31-307">Beispielsweise könnte ein böswilliger Akteur [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) für Ihre Website verwenden und eine websiteübergreifende Anforderung an seine CORS-fähige Website ausführen, um Informationen zu stehlen.</span><span class="sxs-lookup"><span data-stu-id="4de31-307">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="4de31-308">Eine API ist nicht sicherer, wenn CORS zugelassen wird.</span><span class="sxs-lookup"><span data-stu-id="4de31-308">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="4de31-309">Es liegt am Client (Browser), CORS zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="4de31-309">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="4de31-310">Der Server führt die Anforderung aus und gibt die Antwort zurück, es ist der Client, der einen Fehler zurückgibt und die Antwort blockiert.</span><span class="sxs-lookup"><span data-stu-id="4de31-310">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="4de31-311">Eines der folgenden Tools zeigt z. B. die Serverantwort an:</span><span class="sxs-lookup"><span data-stu-id="4de31-311">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="4de31-312">Fiddler</span><span class="sxs-lookup"><span data-stu-id="4de31-312">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="4de31-313">Postman</span><span class="sxs-lookup"><span data-stu-id="4de31-313">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="4de31-314">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="4de31-314">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="4de31-315">Ein Webbrowser, indem er die URL in die Adressleiste eingibt.</span><span class="sxs-lookup"><span data-stu-id="4de31-315">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="4de31-316">Es ist eine Möglichkeit für einen Server, Browsern die Ausführung einer [ursprungsübergreifenden XHR-](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) oder [Fetch-API-Anforderung](https://developer.mozilla.org/docs/Web/API/Fetch_API) zu ermöglichen, die andernfalls verboten wäre.</span><span class="sxs-lookup"><span data-stu-id="4de31-316">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="4de31-317">Browser ohne CORS können keine ursprungsübergreifenden Anforderungen verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="4de31-317">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="4de31-318">Vor CORS wurde [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) verwendet, um diese Einschränkung zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="4de31-318">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="4de31-319">JSONP verwendet XHR nicht, es `<script>` verwendet das Tag, um die Antwort zu empfangen.</span><span class="sxs-lookup"><span data-stu-id="4de31-319">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="4de31-320">Skripts dürfen ursprungsübergreifend geladen werden.</span><span class="sxs-lookup"><span data-stu-id="4de31-320">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="4de31-321">Die [CORS-Spezifikation](https://www.w3.org/TR/cors/) führte mehrere neue HTTP-Header ein, die ursprungsübergreifende Anforderungen ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="4de31-321">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="4de31-322">Wenn ein Browser CORS unterstützt, werden diese Header automatisch für ursprungsübergreifende Anforderungen festgelegt.</span><span class="sxs-lookup"><span data-stu-id="4de31-322">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="4de31-323">Benutzerdefinierter JavaScript-Code ist zum Aktivieren von CORS nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="4de31-323">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="4de31-324">Die [PUT-Testschaltfläche](https://cors3.azurewebsites.net/test) auf dem [bereitgestellten Beispiel](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span><span class="sxs-lookup"><span data-stu-id="4de31-324">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="4de31-325">Im Folgenden finden Sie ein Beispiel für eine ursprungsübergreifende Anforderung von der [Schaltfläche Wertetest](https://cors3.azurewebsites.net/) an `https://cors1.azurewebsites.net/api/values`.</span><span class="sxs-lookup"><span data-stu-id="4de31-325">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="4de31-326">Der `Origin` Header:</span><span class="sxs-lookup"><span data-stu-id="4de31-326">The `Origin` header:</span></span>

* <span data-ttu-id="4de31-327">Stellt die Domäne der Site bereit, die die Anforderung stellt.</span><span class="sxs-lookup"><span data-stu-id="4de31-327">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="4de31-328">Ist erforderlich und muss sich vom Host unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="4de31-328">Is required and must be different from the host.</span></span>

<span data-ttu-id="4de31-329">**Allgemeine Kopfzeilen**</span><span class="sxs-lookup"><span data-stu-id="4de31-329">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="4de31-330">**Antwortheader**</span><span class="sxs-lookup"><span data-stu-id="4de31-330">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="4de31-331">**Anforderungsheader**</span><span class="sxs-lookup"><span data-stu-id="4de31-331">**Request headers**</span></span>

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

<span data-ttu-id="4de31-332">Bei `OPTIONS` Anforderungen legt der Server den **Headerheader "Antwort"** `Access-Control-Allow-Origin: {allowed origin}` in der Antwort fest.</span><span class="sxs-lookup"><span data-stu-id="4de31-332">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="4de31-333">Die Schaltflächenanforderung `OPTIONS` "Das bereitgestellte [Beispiel](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [[EnableCors]](https://cors1.azurewebsites.net/test?number=2) löschen enthält beispielsweise die folgenden Header:</span><span class="sxs-lookup"><span data-stu-id="4de31-333">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="4de31-334">**Allgemeine Kopfzeilen**</span><span class="sxs-lookup"><span data-stu-id="4de31-334">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="4de31-335">**Antwortheader**</span><span class="sxs-lookup"><span data-stu-id="4de31-335">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="4de31-336">**Anforderungsheader**</span><span class="sxs-lookup"><span data-stu-id="4de31-336">**Request headers**</span></span>

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

<span data-ttu-id="4de31-337">In den vorherigen **Response-Headern**legt der Server den [Access-Control-Allow-Origin-Header](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) in der Antwort fest.</span><span class="sxs-lookup"><span data-stu-id="4de31-337">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="4de31-338">Der `https://cors1.azurewebsites.net` Wert dieses Headers entspricht dem `Origin` Header aus der Anforderung.</span><span class="sxs-lookup"><span data-stu-id="4de31-338">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="4de31-339">Wenn <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> aufgerufen wird, wird der `Access-Control-Allow-Origin: *`, der Platzhalterwert, zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="4de31-339">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="4de31-340">`AllowAnyOrigin`ermöglicht jede Herkunft.</span><span class="sxs-lookup"><span data-stu-id="4de31-340">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="4de31-341">Wenn die Antwort den `Access-Control-Allow-Origin` Header nicht enthält, schlägt die ursprungsübergreifende Anforderung fehl.</span><span class="sxs-lookup"><span data-stu-id="4de31-341">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="4de31-342">Insbesondere lässt der Browser die Anforderung nicht zu.</span><span class="sxs-lookup"><span data-stu-id="4de31-342">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="4de31-343">Selbst wenn der Server eine erfolgreiche Antwort zurückgibt, stellt der Browser die Antwort nicht für die Client-App bereit.</span><span class="sxs-lookup"><span data-stu-id="4de31-343">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="4de31-344">OPTIONS-Anforderungen anzeigen</span><span class="sxs-lookup"><span data-stu-id="4de31-344">Display OPTIONS requests</span></span>

<span data-ttu-id="4de31-345">Standardmäßig zeigen die Chrome- und Edge-Browser keine OPTIONS-Anforderungen auf der Registerkarte Netzwerk der F12-Tools an.</span><span class="sxs-lookup"><span data-stu-id="4de31-345">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="4de31-346">So zeigen Sie OPTIONS-Anforderungen in diesen Browsern an:</span><span class="sxs-lookup"><span data-stu-id="4de31-346">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="4de31-347">`chrome://flags/#out-of-blink-cors` oder `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="4de31-347">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="4de31-348">deaktivieren Sie das Flag.</span><span class="sxs-lookup"><span data-stu-id="4de31-348">disable the flag.</span></span>
* <span data-ttu-id="4de31-349">„Neu starten“.</span><span class="sxs-lookup"><span data-stu-id="4de31-349">restart.</span></span>

<span data-ttu-id="4de31-350">Firefox zeigt OPTIONS-Anforderungen standardmäßig an.</span><span class="sxs-lookup"><span data-stu-id="4de31-350">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="4de31-351">CORS in IIS</span><span class="sxs-lookup"><span data-stu-id="4de31-351">CORS in IIS</span></span>

<span data-ttu-id="4de31-352">Bei der Bereitstellung in IIS muss CORS vor der Windows-Authentifizierung ausgeführt werden, wenn der Server nicht für den anonymen Zugriff konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="4de31-352">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="4de31-353">Um dieses Szenario zu unterstützen, muss das [IIS CORS-Modul](https://www.iis.net/downloads/microsoft/iis-cors-module) für die App installiert und konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="4de31-353">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="4de31-354">Testen von CORS</span><span class="sxs-lookup"><span data-stu-id="4de31-354">Test CORS</span></span>

<span data-ttu-id="4de31-355">Der [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) enthält Code zum Testen von CORS.</span><span class="sxs-lookup"><span data-stu-id="4de31-355">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="4de31-356">Informationen [zum Herunterladen](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="4de31-356">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="4de31-357">Das Beispiel ist ein API-Projekt mit Razor Pages hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="4de31-357">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="4de31-358">`WithOrigins("https://localhost:<port>");`sollte nur zum Testen einer Beispiel-App verwendet werden, die dem [Download-Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors)ähnelt.</span><span class="sxs-lookup"><span data-stu-id="4de31-358">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="4de31-359">Im `ValuesController` Folgenden werden die Endpunkte zum Testen angezeigt:</span><span class="sxs-lookup"><span data-stu-id="4de31-359">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="4de31-360">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) wird vom [Paket Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet bereitgestellt und zeigt Routeninformationen an.</span><span class="sxs-lookup"><span data-stu-id="4de31-360">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="4de31-361">Testen Sie den vorherigen Beispielcode mit einem der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="4de31-361">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="4de31-362">Verwenden Sie die [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/)bereitgestellte Beispiel-App unter .</span><span class="sxs-lookup"><span data-stu-id="4de31-362">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="4de31-363">Es ist nicht erforderlich, das Beispiel herunterzuladen.</span><span class="sxs-lookup"><span data-stu-id="4de31-363">There is no need to download the sample.</span></span>
* <span data-ttu-id="4de31-364">Führen Sie `dotnet run` das Beispiel mit `https://localhost:5001`der Standard-URL von aus.</span><span class="sxs-lookup"><span data-stu-id="4de31-364">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="4de31-365">Führen Sie das Beispiel aus Visual Studio aus, wobei der `https://localhost:44398`Port für eine URL von auf 44398 festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="4de31-365">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="4de31-366">Verwenden eines Browsers mit den F12-Tools:</span><span class="sxs-lookup"><span data-stu-id="4de31-366">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="4de31-367">Wählen Sie die Schaltfläche **Werte aus,** und überprüfen Sie die Kopfzeilen auf der Registerkarte **Netzwerk.**</span><span class="sxs-lookup"><span data-stu-id="4de31-367">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="4de31-368">Wählen Sie die **PUT-Testschaltfläche** aus.</span><span class="sxs-lookup"><span data-stu-id="4de31-368">Select the **PUT test** button.</span></span> <span data-ttu-id="4de31-369">Siehe Anzeigen von [OPTIONS-Anforderungen](#options) für Anweisungen zum Anzeigen der OPTIONS-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="4de31-369">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="4de31-370">Der **PUT-Test** erstellt zwei Anforderungen, eine OPTIONS-Preflight-Anforderung und die PUT-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="4de31-370">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="4de31-371">Wählen **`GetValues2 [DisableCors]`** Sie die Schaltfläche aus, um eine fehlgeschlagene CORS-Anforderung auszulösen.</span><span class="sxs-lookup"><span data-stu-id="4de31-371">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="4de31-372">Wie im Dokument erwähnt, gibt die Antwort 200 Erfolge zurück, aber die CORS-Anforderung wird nicht gestellt.</span><span class="sxs-lookup"><span data-stu-id="4de31-372">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="4de31-373">Wählen Sie die Registerkarte **Konsole** aus, um den CORS-Fehler anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="4de31-373">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="4de31-374">Je nach Browser wird ein ähnlicher Fehler wie folgt angezeigt:</span><span class="sxs-lookup"><span data-stu-id="4de31-374">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="4de31-375">Der Zugriff `'https://cors1.azurewebsites.net/api/values/GetValues2'` auf `'https://cors3.azurewebsites.net'` abrufen vom Ursprung wurde durch die CORS-Richtlinie blockiert: In der angeforderten Ressource ist kein 'Access-Control-Allow-Origin'-Header vorhanden.</span><span class="sxs-lookup"><span data-stu-id="4de31-375">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="4de31-376">Falls eine opake Antwort Ihre Anforderungen erfüllt, legen Sie den Modus der Anforderung auf 'no-cors' fest, um CORS für den Ressourcenabruf zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="4de31-376">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="4de31-377">CORS-fähige Endpunkte können mit einem Tool getestet werden, z. B. [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler)oder [Postman](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="4de31-377">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="4de31-378">Bei Verwendung eines Tools muss der Ursprung `Origin` der vom Header angegebenen Anforderung vom Host, der die Anforderung empfängt, abweichen.</span><span class="sxs-lookup"><span data-stu-id="4de31-378">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="4de31-379">Wenn die Anforderung nicht *ursprungsübergreifend* ist, basiert `Origin` sie auf dem Wert des Headers:</span><span class="sxs-lookup"><span data-stu-id="4de31-379">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="4de31-380">CORS Middleware muss die Anforderung nicht verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="4de31-380">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="4de31-381">CORS-Header werden in der Antwort nicht zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="4de31-381">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="4de31-382">Der folgende `curl` Befehl verwendet, um eine OPTIONS-Anforderung mit Informationen ausstellen:</span><span class="sxs-lookup"><span data-stu-id="4de31-382">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="4de31-383">Testen von CORS mit Endpunktrouting und [HttpOptions]</span><span class="sxs-lookup"><span data-stu-id="4de31-383">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="4de31-384">Das Aktivieren von CORS pro `RequireCors` Endpunkt mithilfe der aktuellen Verwendung unterstützt ***keine*** [automatischen Preflight-Anforderungen](#apf).</span><span class="sxs-lookup"><span data-stu-id="4de31-384">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="4de31-385">Berücksichtigen Sie den folgenden Code, der [Endpunktrouting verwendet, um CORS zu aktivieren:](#ecors)</span><span class="sxs-lookup"><span data-stu-id="4de31-385">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="4de31-386">Im `TodoItems1Controller` Folgenden finden Sie Endpunkte zum Testen:</span><span class="sxs-lookup"><span data-stu-id="4de31-386">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="4de31-387">Testen Sie den vorherigen Code auf der [Testseite](https://cors1.azurewebsites.net/test?number=1) des bereitgestellten [Beispiels](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span><span class="sxs-lookup"><span data-stu-id="4de31-387">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="4de31-388">Die Schaltflächen **[EnableCors]** und **GET [EnableCors]** sind `[EnableCors]` erfolgreich, da die Endpunkte preflight-Anforderungen haben und darauf reagieren.</span><span class="sxs-lookup"><span data-stu-id="4de31-388">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="4de31-389">Die anderen Endpunkte schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="4de31-389">The other endpoints fails.</span></span> <span data-ttu-id="4de31-390">Die **GET-Schaltfläche** schlägt fehl, da [JavaScript:](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js)</span><span class="sxs-lookup"><span data-stu-id="4de31-390">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="4de31-391">Das `TodoItems2Controller` Folgende stellt ähnliche Endpunkte bereit, enthält jedoch expliziten Code, um auf OPTIONS-Anforderungen zu reagieren:</span><span class="sxs-lookup"><span data-stu-id="4de31-391">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="4de31-392">Testen Sie den vorherigen Code auf der [Testseite](https://cors1.azurewebsites.net/test?number=2) des bereitgestellten Beispiels.</span><span class="sxs-lookup"><span data-stu-id="4de31-392">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="4de31-393">Wählen Sie in der **Dropdown-Liste Controller** **Preflight** und set **Controller**aus.</span><span class="sxs-lookup"><span data-stu-id="4de31-393">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="4de31-394">Alle CORS-Aufrufe `TodoItems2Controller` an die Endpunkte sind erfolgreich.</span><span class="sxs-lookup"><span data-stu-id="4de31-394">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4de31-395">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="4de31-395">Additional resources</span></span>

* [<span data-ttu-id="4de31-396">Cross-Origin Resource Sharing (CORS)</span><span class="sxs-lookup"><span data-stu-id="4de31-396">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="4de31-397">Erste Schritte mit dem IIS CORS-Modul</span><span class="sxs-lookup"><span data-stu-id="4de31-397">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4de31-398">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4de31-398">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4de31-399">Dieser Artikel zeigt, wie CORS in einer ASP.NET Core-App aktiviert wird.</span><span class="sxs-lookup"><span data-stu-id="4de31-399">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="4de31-400">Die Browsersicherheit verhindert, dass eine Webseite Anforderungen an eine andere Domäne als die, die die Webseite bereitgestellt hat, stellt.</span><span class="sxs-lookup"><span data-stu-id="4de31-400">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="4de31-401">Diese Einschränkung wird als *Richtlinie mit gleichem Ursprung*bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="4de31-401">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="4de31-402">Die Richtlinie mit demselben Ursprung verhindert, dass eine böswillige Website vertrauliche Daten von einer anderen Website liest.</span><span class="sxs-lookup"><span data-stu-id="4de31-402">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="4de31-403">Manchmal möchten Sie möglicherweise zulassen, dass andere Websites ursprungsübergreifende Anforderungen an Ihre App stellen.</span><span class="sxs-lookup"><span data-stu-id="4de31-403">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="4de31-404">Weitere Informationen finden Sie im [Mozilla CORS-Artikel](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="4de31-404">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="4de31-405">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="4de31-405">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="4de31-406">Ein W3C-Standard, der es einem Server ermöglicht, die Richtlinie mit demselben Ursprung zu entspannen.</span><span class="sxs-lookup"><span data-stu-id="4de31-406">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="4de31-407">Ist **kein** Sicherheitsmerkmal, CORS entspannt die Sicherheit.</span><span class="sxs-lookup"><span data-stu-id="4de31-407">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="4de31-408">Eine API ist nicht sicherer, wenn CORS zugelassen wird.</span><span class="sxs-lookup"><span data-stu-id="4de31-408">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="4de31-409">Weitere Informationen finden Sie unter Funktionsweise von [CORS](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="4de31-409">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="4de31-410">Ermöglicht einem Server, einige ursprungsübergreifende Anforderungen explizit zuzulassen, während andere abgelehnt werden.</span><span class="sxs-lookup"><span data-stu-id="4de31-410">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="4de31-411">Ist sicherer und flexibler als frühere Techniken, wie z. B. [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="4de31-411">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="4de31-412">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4de31-412">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="4de31-413">Gleicher Ursprung</span><span class="sxs-lookup"><span data-stu-id="4de31-413">Same origin</span></span>

<span data-ttu-id="4de31-414">Zwei URLs haben denselben Ursprung, wenn sie identische Schemata, Hosts und Ports haben ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="4de31-414">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="4de31-415">Diese beiden URLs haben den gleichen Ursprung:</span><span class="sxs-lookup"><span data-stu-id="4de31-415">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="4de31-416">Diese URLs haben andere Ursprünge als die beiden vorherigen URLs:</span><span class="sxs-lookup"><span data-stu-id="4de31-416">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="4de31-417">`https://example.net`&ndash; Verschiedene Domäne</span><span class="sxs-lookup"><span data-stu-id="4de31-417">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="4de31-418">`https://www.example.com/foo.html`&ndash; Verschiedene Subdomain</span><span class="sxs-lookup"><span data-stu-id="4de31-418">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="4de31-419">`http://example.com/foo.html`&ndash; Unterschiedlicheregelung</span><span class="sxs-lookup"><span data-stu-id="4de31-419">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="4de31-420">`https://example.com:9000/foo.html`&ndash; Verschiedene randder Hafen</span><span class="sxs-lookup"><span data-stu-id="4de31-420">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="4de31-421">Internet Explorer berücksichtigt den Port nicht beim Vergleich der Ursprünge.</span><span class="sxs-lookup"><span data-stu-id="4de31-421">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="4de31-422">CORS mit benannter Richtlinie und Middleware</span><span class="sxs-lookup"><span data-stu-id="4de31-422">CORS with named policy and middleware</span></span>

<span data-ttu-id="4de31-423">CORS Middleware verarbeitet ursprungsübergreifende Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="4de31-423">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="4de31-424">Der folgende Code aktiviert CORS für die gesamte App mit dem angegebenen Ursprung:</span><span class="sxs-lookup"><span data-stu-id="4de31-424">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="4de31-425">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="4de31-425">The preceding code:</span></span>

* <span data-ttu-id="4de31-426">Legt den Richtliniennamen\_auf "myAllowSpecificOrigins" fest.</span><span class="sxs-lookup"><span data-stu-id="4de31-426">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="4de31-427">Der Richtlinienname ist willkürlich.</span><span class="sxs-lookup"><span data-stu-id="4de31-427">The policy name is arbitrary.</span></span>
* <span data-ttu-id="4de31-428">Ruft <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> die Erweiterungsmethode auf, die CORS aktiviert.</span><span class="sxs-lookup"><span data-stu-id="4de31-428">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="4de31-429">Aufrufe <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> mit einem [Lambda-Ausdruck](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="4de31-429">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="4de31-430">Der Lambda <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> nimmt ein Objekt.</span><span class="sxs-lookup"><span data-stu-id="4de31-430">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="4de31-431">[Konfigurationsoptionen](#cors-policy-options), `WithOrigins`z. B. , werden weiter unten in diesem Artikel beschrieben.</span><span class="sxs-lookup"><span data-stu-id="4de31-431">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="4de31-432">Der <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> Methodenaufruf fügt DEM Dienstcontainer der App CORS-Dienste hinzu:</span><span class="sxs-lookup"><span data-stu-id="4de31-432">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="4de31-433">Weitere Informationen finden Sie unter [CORS-Richtlinienoptionen](#cpo) in diesem Dokument .</span><span class="sxs-lookup"><span data-stu-id="4de31-433">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="4de31-434">Die <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> Methode kann Methoden verketten, wie im folgenden Code gezeigt:</span><span class="sxs-lookup"><span data-stu-id="4de31-434">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="4de31-435">Hinweis: Die URL darf **keinen** nachgestellten Schrägstrich (`/`) enthalten.</span><span class="sxs-lookup"><span data-stu-id="4de31-435">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="4de31-436">Wenn die URL `/`mit beendet `false` wird, wird der Vergleich zurückgegeben, und es wird kein Header zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="4de31-436">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="4de31-437">Der folgende Code wendet CORS-Richtlinien auf alle App-Endpunkte über CORS Middleware an:</span><span class="sxs-lookup"><span data-stu-id="4de31-437">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
<span data-ttu-id="4de31-438">Hinweis: `UseCors` muss vor `UseMvc`aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="4de31-438">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="4de31-439">Weitere Informationen finden Sie unter Aktivieren von [CORS in Razor Pages, Controllern und Aktionsmethoden](#ecors) zum Anwenden der CORS-Richtlinie auf Seiten-/Controller-/Aktionsebene.</span><span class="sxs-lookup"><span data-stu-id="4de31-439">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="4de31-440">Anweisungen zum Testen von Code ähnlich dem vorherigen Code finden Sie unter [Test-CORS.](#test)</span><span class="sxs-lookup"><span data-stu-id="4de31-440">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="4de31-441">CORS mit Attributen aktivieren</span><span class="sxs-lookup"><span data-stu-id="4de31-441">Enable CORS with attributes</span></span>

<span data-ttu-id="4de31-442">Das [ &lbrack;&rbrack; EnableCors-Attribut](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) bietet eine Alternative zum globalen Anwenden von CORS.</span><span class="sxs-lookup"><span data-stu-id="4de31-442">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="4de31-443">Das `[EnableCors]` Attribut aktiviert CORS für ausgewählte Endpunkte und nicht für alle Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="4de31-443">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="4de31-444">Verwenden `[EnableCors]` Sie diese Option, um die Standardrichtlinie und `[EnableCors("{Policy String}")]` eine Richtlinie anzugeben.</span><span class="sxs-lookup"><span data-stu-id="4de31-444">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="4de31-445">Das `[EnableCors]` Attribut kann angewendet werden auf:</span><span class="sxs-lookup"><span data-stu-id="4de31-445">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="4de31-446">Razor Page`PageModel`</span><span class="sxs-lookup"><span data-stu-id="4de31-446">Razor Page `PageModel`</span></span>
* <span data-ttu-id="4de31-447">Controller</span><span class="sxs-lookup"><span data-stu-id="4de31-447">Controller</span></span>
* <span data-ttu-id="4de31-448">Controller-Aktionsmethode</span><span class="sxs-lookup"><span data-stu-id="4de31-448">Controller action method</span></span>

<span data-ttu-id="4de31-449">Sie können mit dem `[EnableCors]` Attribut unterschiedliche Richtlinien auf Controller/Seitenmodell/Aktion anwenden.</span><span class="sxs-lookup"><span data-stu-id="4de31-449">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="4de31-450">Wenn `[EnableCors]` das Attribut auf eine Controller-/Seitenmodell-/Aktionsmethode angewendet wird und CORS in Middleware aktiviert ist, werden ***beide*** Richtlinien angewendet.</span><span class="sxs-lookup"><span data-stu-id="4de31-450">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="4de31-451">Es wird empfohlen, richtlinien ***nicht*** zu kombinieren.</span><span class="sxs-lookup"><span data-stu-id="4de31-451">We recommend ***not*** combining policies.</span></span> <span data-ttu-id="4de31-452">Verwenden `[EnableCors]` Sie das Attribut oder die Middleware, \***nicht beides**.</span><span class="sxs-lookup"><span data-stu-id="4de31-452">Use the `[EnableCors]` attribute or middleware, \***not both**.</span></span> <span data-ttu-id="4de31-453">Bei `[EnableCors]`Verwendung von definieren Sie **keine** Standardrichtlinie.</span><span class="sxs-lookup"><span data-stu-id="4de31-453">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="4de31-454">Der folgende Code wendet für jede Methode eine andere Richtlinie an:</span><span class="sxs-lookup"><span data-stu-id="4de31-454">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="4de31-455">Der folgende Code erstellt eine CORS-Standardrichtlinie und eine Richtlinie mit dem Namen: `"AnotherPolicy"`</span><span class="sxs-lookup"><span data-stu-id="4de31-455">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="4de31-456">DEAKTIVIEREN von CORS</span><span class="sxs-lookup"><span data-stu-id="4de31-456">Disable CORS</span></span>

<span data-ttu-id="4de31-457">Das [ &lbrack;&rbrack; DisableCors-Attribut](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) deaktiviert CORS für den Controller/Seitenmodell/die Aktion.</span><span class="sxs-lookup"><span data-stu-id="4de31-457">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="4de31-458">CORS-Richtlinienoptionen</span><span class="sxs-lookup"><span data-stu-id="4de31-458">CORS policy options</span></span>

<span data-ttu-id="4de31-459">In diesem Abschnitt werden die verschiedenen Optionen beschrieben, die in einer CORS-Richtlinie festgelegt werden können:</span><span class="sxs-lookup"><span data-stu-id="4de31-459">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="4de31-460">Festlegen der zulässigen Ursprünge</span><span class="sxs-lookup"><span data-stu-id="4de31-460">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="4de31-461">Festlegen der zulässigen HTTP-Methoden</span><span class="sxs-lookup"><span data-stu-id="4de31-461">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="4de31-462">Festlegen der zulässigen Anforderungsheader</span><span class="sxs-lookup"><span data-stu-id="4de31-462">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="4de31-463">Festlegen der freiliegenden Antwortheader</span><span class="sxs-lookup"><span data-stu-id="4de31-463">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="4de31-464">Anmeldeinformationen in ursprungsübergreifenden Anforderungen</span><span class="sxs-lookup"><span data-stu-id="4de31-464">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="4de31-465">Festlegen der Ablaufzeit vor dem Flug</span><span class="sxs-lookup"><span data-stu-id="4de31-465">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="4de31-466"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>wird in `Startup.ConfigureServices`aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="4de31-466"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="4de31-467">Bei einigen Optionen kann es hilfreich sein, zuerst den Abschnitt ["Funktionsweise von CORS"](#how-cors) zu lesen.</span><span class="sxs-lookup"><span data-stu-id="4de31-467">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="4de31-468">Festlegen der zulässigen Ursprünge</span><span class="sxs-lookup"><span data-stu-id="4de31-468">Set the allowed origins</span></span>

<span data-ttu-id="4de31-469"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Ermöglicht CORS-Anforderungen aller Herkunftsherkunft mit einem beliebigen Schema (`http` oder `https`).</span><span class="sxs-lookup"><span data-stu-id="4de31-469"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="4de31-470">`AllowAnyOrigin`ist unsicher, da *jede Website* ursprungsübergreifende Anforderungen an die App stellen kann.</span><span class="sxs-lookup"><span data-stu-id="4de31-470">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="4de31-471">Angeben `AllowAnyOrigin` und `AllowCredentials` ist eine unsichere Konfiguration und kann zu standortübergreifenden Anforderungsfälschungen führen.</span><span class="sxs-lookup"><span data-stu-id="4de31-471">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="4de31-472">Geben Sie für eine sichere App eine genaue Liste der Ursprünge an, wenn der Client sich selbst für den Zugriff auf Serverressourcen autorisieren muss.</span><span class="sxs-lookup"><span data-stu-id="4de31-472">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="4de31-473">`AllowAnyOrigin`wirkt sich auf `Access-Control-Allow-Origin` Preflight-Anforderungen und den Header aus.</span><span class="sxs-lookup"><span data-stu-id="4de31-473">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="4de31-474">Weitere Informationen finden Sie im Abschnitt [Preflight-Anforderungen.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="4de31-474">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="4de31-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Legt <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> die Eigenschaft der Richtlinie als funktion fest, die es dem Ursprung ermöglicht, eine konfigurierte Platzhalterdomäne abzugleichen, wenn ausgewertet wird, ob der Ursprung zulässig ist.</span><span class="sxs-lookup"><span data-stu-id="4de31-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="4de31-476">Festlegen der zulässigen HTTP-Methoden</span><span class="sxs-lookup"><span data-stu-id="4de31-476">Set the allowed HTTP methods</span></span>

<span data-ttu-id="4de31-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="4de31-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="4de31-478">Erlaubt jede HTTP-Methode:</span><span class="sxs-lookup"><span data-stu-id="4de31-478">Allows any HTTP method:</span></span>
* <span data-ttu-id="4de31-479">Beeinflusst Preflight-Anforderungen `Access-Control-Allow-Methods` und den Header.</span><span class="sxs-lookup"><span data-stu-id="4de31-479">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="4de31-480">Weitere Informationen finden Sie im Abschnitt [Preflight-Anforderungen.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="4de31-480">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="4de31-481">Festlegen der zulässigen Anforderungsheader</span><span class="sxs-lookup"><span data-stu-id="4de31-481">Set the allowed request headers</span></span>

<span data-ttu-id="4de31-482">Um das Senden bestimmter Header in einer CORS-Anforderung zuzulassen, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> rufen Sie die *zulässigen*Header an, um das Senden bestimmter Header in einer CORS-Anforderung zu ermöglichen:</span><span class="sxs-lookup"><span data-stu-id="4de31-482">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="4de31-483">Um alle Autorenanforderungsheader <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>zuzulassen, rufen Sie :</span><span class="sxs-lookup"><span data-stu-id="4de31-483">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="4de31-484">Diese Einstellung wirkt sich `Access-Control-Request-Headers` auf Preflight-Anforderungen und den Header aus.</span><span class="sxs-lookup"><span data-stu-id="4de31-484">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="4de31-485">Weitere Informationen finden Sie im Abschnitt [Preflight-Anforderungen.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="4de31-485">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="4de31-486">CORS Middleware ermöglicht immer, `Access-Control-Request-Headers` dass vier Header in der gesendet werden, unabhängig von den in CorsPolicy.Headers konfigurierten Werten.</span><span class="sxs-lookup"><span data-stu-id="4de31-486">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="4de31-487">Diese Liste der Header enthält:</span><span class="sxs-lookup"><span data-stu-id="4de31-487">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="4de31-488">Betrachten Sie beispielsweise eine App, die wie folgt konfiguriert ist:</span><span class="sxs-lookup"><span data-stu-id="4de31-488">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="4de31-489">CORS Middleware reagiert erfolgreich auf eine Preflight-Anforderung `Content-Language` mit dem folgenden Anforderungsheader, da immer auf der Whitelist steht:</span><span class="sxs-lookup"><span data-stu-id="4de31-489">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="4de31-490">Festlegen der freiliegenden Antwortheader</span><span class="sxs-lookup"><span data-stu-id="4de31-490">Set the exposed response headers</span></span>

<span data-ttu-id="4de31-491">Standardmäßig macht der Browser nicht alle Antwortheader für die App verfügbar.</span><span class="sxs-lookup"><span data-stu-id="4de31-491">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="4de31-492">Weitere Informationen finden Sie unter [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="4de31-492">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="4de31-493">Die standardmäßig verfügbaren Antwortheader sind:</span><span class="sxs-lookup"><span data-stu-id="4de31-493">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="4de31-494">Die CORS-Spezifikation ruft diese Header *auf einfache Antwortheader*.</span><span class="sxs-lookup"><span data-stu-id="4de31-494">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="4de31-495">Um andere Header für die App <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>verfügbar zu machen, rufen Sie :</span><span class="sxs-lookup"><span data-stu-id="4de31-495">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="4de31-496">Anmeldeinformationen in ursprungsübergreifenden Anforderungen</span><span class="sxs-lookup"><span data-stu-id="4de31-496">Credentials in cross-origin requests</span></span>

<span data-ttu-id="4de31-497">Anmeldeinformationen erfordern eine spezielle Behandlung in einer CORS-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="4de31-497">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="4de31-498">Standardmäßig sendet der Browser keine Anmeldeinformationen mit einer ursprungsübergreifenden Anforderung.</span><span class="sxs-lookup"><span data-stu-id="4de31-498">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="4de31-499">Anmeldeinformationen umfassen Cookies und HTTP-Authentifizierungsschemata.</span><span class="sxs-lookup"><span data-stu-id="4de31-499">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="4de31-500">Um Anmeldeinformationen mit einer ursprungsübergreifenden Anforderung `XMLHttpRequest.withCredentials` zu `true`senden, muss der Client auf festlegen.</span><span class="sxs-lookup"><span data-stu-id="4de31-500">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="4de31-501">Direkt `XMLHttpRequest` verwenden:</span><span class="sxs-lookup"><span data-stu-id="4de31-501">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="4de31-502">Verwenden von jQuery:</span><span class="sxs-lookup"><span data-stu-id="4de31-502">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="4de31-503">Verwenden der [Abruf-API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="4de31-503">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="4de31-504">Der Server muss die Anmeldeinformationen zulassen.</span><span class="sxs-lookup"><span data-stu-id="4de31-504">The server must allow the credentials.</span></span> <span data-ttu-id="4de31-505">Um ursprungsübergreifende Anmeldeinformationen zuzulassen, rufen Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span><span class="sxs-lookup"><span data-stu-id="4de31-505">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="4de31-506">Die HTTP-Antwort `Access-Control-Allow-Credentials` enthält einen Header, der dem Browser mitteilt, dass der Server Anmeldeinformationen für eine ursprungsübergreifende Anforderung zulässt.</span><span class="sxs-lookup"><span data-stu-id="4de31-506">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="4de31-507">Wenn der Browser Anmeldeinformationen sendet, die Antwort `Access-Control-Allow-Credentials` jedoch keinen gültigen Header enthält, macht der Browser die Antwort für die App nicht verfügbar, und die ursprungsübergreifende Anforderung schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="4de31-507">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="4de31-508">Das Zulassen ursprungsübergreifender Anmeldeinformationen stellt ein Sicherheitsrisiko dar.</span><span class="sxs-lookup"><span data-stu-id="4de31-508">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="4de31-509">Eine Website in einer anderen Domäne kann die Anmeldeinformationen eines angemeldeten Benutzers im Namen des Benutzers ohne Wissen des Benutzers an die App senden.</span><span class="sxs-lookup"><span data-stu-id="4de31-509">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="4de31-510">Die CORS-Spezifikation gibt auch `"*"` an, dass das Festlegen `Access-Control-Allow-Credentials` von Ursprüngen auf (alle Ursprünge) ungültig ist, wenn der Header vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="4de31-510">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="4de31-511">Preflight-Anfragen</span><span class="sxs-lookup"><span data-stu-id="4de31-511">Preflight requests</span></span>

<span data-ttu-id="4de31-512">Bei einigen CORS-Anforderungen sendet der Browser eine zusätzliche Anforderung, bevor er die eigentliche Anforderung stellt.</span><span class="sxs-lookup"><span data-stu-id="4de31-512">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="4de31-513">Diese Anforderung wird als *Preflight-Anforderung*bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="4de31-513">This request is called a *preflight request*.</span></span> <span data-ttu-id="4de31-514">Der Browser kann die Preflight-Anforderung überspringen, wenn die folgenden Bedingungen zutreffen:</span><span class="sxs-lookup"><span data-stu-id="4de31-514">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="4de31-515">Die Anforderungsmethode ist GET, HEAD oder POST.</span><span class="sxs-lookup"><span data-stu-id="4de31-515">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="4de31-516">Die App stellt keine anderen Anforderungsheader `Content-Language` `Content-Type`als `Accept`, `Accept-Language`, , oder `Last-Event-ID`fest.</span><span class="sxs-lookup"><span data-stu-id="4de31-516">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="4de31-517">Der `Content-Type` Header, sofern festgelegt, hat einen der folgenden Werte:</span><span class="sxs-lookup"><span data-stu-id="4de31-517">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="4de31-518">Die Regel für Anforderungsheader, die für die Clientanforderung festgelegt `setRequestHeader` wurden, gilt für Header, die die App durch Aufrufen des `XMLHttpRequest` Objekts festlegt.</span><span class="sxs-lookup"><span data-stu-id="4de31-518">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="4de31-519">Die CORS-Spezifikation ruft diese *Header-Author-Anforderungsheader*auf.</span><span class="sxs-lookup"><span data-stu-id="4de31-519">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="4de31-520">Die Regel gilt nicht für Header, die der `User-Agent` `Host`Browser `Content-Length`festlegen kann, z. B. , oder .</span><span class="sxs-lookup"><span data-stu-id="4de31-520">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="4de31-521">Im Folgenden finden Sie ein Beispiel für eine Preflight-Anforderung:</span><span class="sxs-lookup"><span data-stu-id="4de31-521">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="4de31-522">Die Pre-Flight-Anforderung verwendet die HTTP OPTIONS-Methode.</span><span class="sxs-lookup"><span data-stu-id="4de31-522">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="4de31-523">Es enthält zwei spezielle Header:</span><span class="sxs-lookup"><span data-stu-id="4de31-523">It includes two special headers:</span></span>

* <span data-ttu-id="4de31-524">`Access-Control-Request-Method`: Die HTTP-Methode, die für die eigentliche Anforderung verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="4de31-524">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="4de31-525">`Access-Control-Request-Headers`: Eine Liste der Anforderungsheader, die die App für die eigentliche Anforderung festlegt.</span><span class="sxs-lookup"><span data-stu-id="4de31-525">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="4de31-526">Wie bereits erwähnt, enthält dies keine Header, die `User-Agent`der Browser festlegt, z. B. .</span><span class="sxs-lookup"><span data-stu-id="4de31-526">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="4de31-527">Wenn CORS mit der entsprechenden Richtlinie aktiviert ist, reagiert ASP.NET Core in der Regel automatisch auf CORS-Preflight-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="4de31-527">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="4de31-528">Siehe [[HttpOptions]-Attribut für Preflight-Anforderungen](#pro).</span><span class="sxs-lookup"><span data-stu-id="4de31-528">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="4de31-529">Eine CORS-Preflight-Anforderung `Access-Control-Request-Headers` kann einen Header enthalten, der dem Server die Header angibt, die mit der eigentlichen Anforderung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="4de31-529">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="4de31-530">Um bestimmte Header zuzulassen, rufen Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span><span class="sxs-lookup"><span data-stu-id="4de31-530">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="4de31-531">Um alle Autorenanforderungsheader <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>zuzulassen, rufen Sie :</span><span class="sxs-lookup"><span data-stu-id="4de31-531">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="4de31-532">Browser sind nicht vollständig konsistent in `Access-Control-Request-Headers`der Art und Weise, wie sie festlegen.</span><span class="sxs-lookup"><span data-stu-id="4de31-532">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="4de31-533">Wenn Sie Header auf etwas `"*"` anderes <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>als (oder verwenden `Accept` `Content-Type`) `Origin`festlegen, sollten Sie mindestens , und , sowie alle benutzerdefinierten Header einschließen, die Sie unterstützen möchten.</span><span class="sxs-lookup"><span data-stu-id="4de31-533">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="4de31-534">Im Folgenden finden Sie eine Beispielantwort auf die Preflight-Anforderung (vorausgesetzt, der Server lässt die Anforderung zu):</span><span class="sxs-lookup"><span data-stu-id="4de31-534">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="4de31-535">Die Antwort `Access-Control-Allow-Methods` enthält einen Header, der die `Access-Control-Allow-Headers` zulässigen Methoden auflistet, und optional einen Header, der die zulässigen Header auflistet.</span><span class="sxs-lookup"><span data-stu-id="4de31-535">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="4de31-536">Wenn die Preflight-Anforderung erfolgreich ist, sendet der Browser die eigentliche Anforderung.</span><span class="sxs-lookup"><span data-stu-id="4de31-536">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="4de31-537">Wenn die Preflight-Anforderung abgelehnt wird, gibt die App eine *200 OK-Antwort* zurück, sendet die CORS-Header jedoch nicht zurück.</span><span class="sxs-lookup"><span data-stu-id="4de31-537">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="4de31-538">Daher versucht der Browser nicht, die ursprungsübergreifende Anforderung zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="4de31-538">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="4de31-539">Festlegen der Ablaufzeit vor dem Flug</span><span class="sxs-lookup"><span data-stu-id="4de31-539">Set the preflight expiration time</span></span>

<span data-ttu-id="4de31-540">Der `Access-Control-Max-Age` Header gibt an, wie lange die Antwort auf die Preflight-Anforderung zwischengespeichert werden kann.</span><span class="sxs-lookup"><span data-stu-id="4de31-540">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="4de31-541">Um diesen Header <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>festzulegen, rufen Sie :</span><span class="sxs-lookup"><span data-stu-id="4de31-541">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="4de31-542">Funktionsweise von CORS</span><span class="sxs-lookup"><span data-stu-id="4de31-542">How CORS works</span></span>

<span data-ttu-id="4de31-543">In diesem Abschnitt wird beschrieben, was in einer [CORS-Anforderung](https://developer.mozilla.org/docs/Web/HTTP/CORS) auf der Ebene der HTTP-Nachrichten geschieht.</span><span class="sxs-lookup"><span data-stu-id="4de31-543">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="4de31-544">CORS ist **kein** Sicherheitsfeature.</span><span class="sxs-lookup"><span data-stu-id="4de31-544">CORS is **not** a security feature.</span></span> <span data-ttu-id="4de31-545">CORS ist ein W3C-Standard, der es einem Server ermöglicht, die Richtlinie mit demselben Ursprung zu entspannen.</span><span class="sxs-lookup"><span data-stu-id="4de31-545">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="4de31-546">Beispielsweise könnte ein böswilliger Akteur [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) für Ihre Website verhindern und eine websiteübergreifende Anforderung an seine CORS-fähige Website ausführen, um Informationen zu stehlen.</span><span class="sxs-lookup"><span data-stu-id="4de31-546">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="4de31-547">Ihre API ist nicht sicherer, wenn CORS zugelassen wird.</span><span class="sxs-lookup"><span data-stu-id="4de31-547">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="4de31-548">Es liegt am Client (Browser), CORS zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="4de31-548">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="4de31-549">Der Server führt die Anforderung aus und gibt die Antwort zurück, es ist der Client, der einen Fehler zurückgibt und die Antwort blockiert.</span><span class="sxs-lookup"><span data-stu-id="4de31-549">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="4de31-550">Eines der folgenden Tools zeigt z. B. die Serverantwort an:</span><span class="sxs-lookup"><span data-stu-id="4de31-550">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="4de31-551">Fiddler</span><span class="sxs-lookup"><span data-stu-id="4de31-551">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="4de31-552">Postman</span><span class="sxs-lookup"><span data-stu-id="4de31-552">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="4de31-553">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="4de31-553">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="4de31-554">Ein Webbrowser, indem er die URL in die Adressleiste eingibt.</span><span class="sxs-lookup"><span data-stu-id="4de31-554">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="4de31-555">Es ist eine Möglichkeit für einen Server, Browsern die Ausführung einer [ursprungsübergreifenden XHR-](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) oder [Fetch-API-Anforderung](https://developer.mozilla.org/docs/Web/API/Fetch_API) zu ermöglichen, die andernfalls verboten wäre.</span><span class="sxs-lookup"><span data-stu-id="4de31-555">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="4de31-556">Browser (ohne CORS) können keine ursprungsübergreifenden Anforderungen erfüllen.</span><span class="sxs-lookup"><span data-stu-id="4de31-556">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="4de31-557">Vor CORS wurde [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) verwendet, um diese Einschränkung zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="4de31-557">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="4de31-558">JSONP verwendet XHR nicht, es `<script>` verwendet das Tag, um die Antwort zu empfangen.</span><span class="sxs-lookup"><span data-stu-id="4de31-558">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="4de31-559">Skripts dürfen ursprungsübergreifend geladen werden.</span><span class="sxs-lookup"><span data-stu-id="4de31-559">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="4de31-560">Die [CORS-Spezifikation](https://www.w3.org/TR/cors/) führte mehrere neue HTTP-Header ein, die ursprungsübergreifende Anforderungen ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="4de31-560">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="4de31-561">Wenn ein Browser CORS unterstützt, werden diese Header automatisch für ursprungsübergreifende Anforderungen festgelegt.</span><span class="sxs-lookup"><span data-stu-id="4de31-561">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="4de31-562">Benutzerdefinierter JavaScript-Code ist zum Aktivieren von CORS nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="4de31-562">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="4de31-563">Im Folgenden finden Sie ein Beispiel für eine ursprungsübergreifende Anforderung.</span><span class="sxs-lookup"><span data-stu-id="4de31-563">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="4de31-564">Der `Origin` Header stellt die Domäne der Site bereit, die die Anforderung stellt.</span><span class="sxs-lookup"><span data-stu-id="4de31-564">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="4de31-565">Der `Origin` Header ist erforderlich und muss sich vom Host unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="4de31-565">The `Origin` header is required and must be different from the host.</span></span>

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

<span data-ttu-id="4de31-566">Wenn der Server die Anforderung `Access-Control-Allow-Origin` zulässt, legt er den Header in der Antwort fest.</span><span class="sxs-lookup"><span data-stu-id="4de31-566">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="4de31-567">Der Wert dieses Headers `Origin` entspricht entweder dem Header `"*"`aus der Anforderung oder ist der Platzhalterwert , was bedeutet, dass jeder Ursprung zulässig ist:</span><span class="sxs-lookup"><span data-stu-id="4de31-567">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="4de31-568">Wenn die Antwort den `Access-Control-Allow-Origin` Header nicht enthält, schlägt die ursprungsübergreifende Anforderung fehl.</span><span class="sxs-lookup"><span data-stu-id="4de31-568">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="4de31-569">Insbesondere lässt der Browser die Anforderung nicht zu.</span><span class="sxs-lookup"><span data-stu-id="4de31-569">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="4de31-570">Selbst wenn der Server eine erfolgreiche Antwort zurückgibt, stellt der Browser die Antwort nicht für die Client-App bereit.</span><span class="sxs-lookup"><span data-stu-id="4de31-570">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="4de31-571">Testen von CORS</span><span class="sxs-lookup"><span data-stu-id="4de31-571">Test CORS</span></span>

<span data-ttu-id="4de31-572">So testen Sie CORS:</span><span class="sxs-lookup"><span data-stu-id="4de31-572">To test CORS:</span></span>

1. <span data-ttu-id="4de31-573">[Erstellen Sie ein API-Projekt](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="4de31-573">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="4de31-574">Alternativ können Sie [das Beispiel herunterladen.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors)</span><span class="sxs-lookup"><span data-stu-id="4de31-574">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="4de31-575">Aktivieren Sie CORS mithilfe eines der Ansätze in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="4de31-575">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="4de31-576">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="4de31-576">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="4de31-577">`WithOrigins("https://localhost:<port>");`sollte nur zum Testen einer Beispiel-App verwendet werden, die dem [Download-Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)ähnelt.</span><span class="sxs-lookup"><span data-stu-id="4de31-577">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="4de31-578">Erstellen Sie ein Web-App-Projekt (Razor Pages oder MVC).</span><span class="sxs-lookup"><span data-stu-id="4de31-578">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="4de31-579">Im Beispiel werden Razor-Seiten verwendet.</span><span class="sxs-lookup"><span data-stu-id="4de31-579">The sample uses Razor Pages.</span></span> <span data-ttu-id="4de31-580">Sie können die Web-App in derselben Lösung wie das API-Projekt erstellen.</span><span class="sxs-lookup"><span data-stu-id="4de31-580">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="4de31-581">Fügen Sie der Datei *Index.cshtml* den folgenden hervorgehobenen Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="4de31-581">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="4de31-582">Ersetzen Sie im `url: 'https://<web app>.azurewebsites.net/api/values/1',` vorherigen Code durch die URL zur bereitgestellten App.</span><span class="sxs-lookup"><span data-stu-id="4de31-582">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="4de31-583">Stellen Sie das API-Projekt bereit.</span><span class="sxs-lookup"><span data-stu-id="4de31-583">Deploy the API project.</span></span> <span data-ttu-id="4de31-584">Stellen Sie z. [B. in Azure bereit.](xref:host-and-deploy/azure-apps/index)</span><span class="sxs-lookup"><span data-stu-id="4de31-584">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="4de31-585">Führen Sie die Razor Pages oder MVC-App vom Desktop aus aus und klicken Sie auf die Schaltfläche **Testen.**</span><span class="sxs-lookup"><span data-stu-id="4de31-585">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="4de31-586">Verwenden Sie die F12-Tools, um Fehlermeldungen zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="4de31-586">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="4de31-587">Entfernen Sie den `WithOrigins` Ursprung des lokalen Hosts aus, und stellen Sie die App bereit.</span><span class="sxs-lookup"><span data-stu-id="4de31-587">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="4de31-588">Alternativ können Sie die Client-App mit einem anderen Port ausführen.</span><span class="sxs-lookup"><span data-stu-id="4de31-588">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="4de31-589">Führen Sie z. B. von Visual Studio aus.</span><span class="sxs-lookup"><span data-stu-id="4de31-589">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="4de31-590">Testen Sie mit der Client-App.</span><span class="sxs-lookup"><span data-stu-id="4de31-590">Test with the client app.</span></span> <span data-ttu-id="4de31-591">CORS-Fehler geben einen Fehler zurück, aber die Fehlermeldung ist für JavaScript nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="4de31-591">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="4de31-592">Verwenden Sie die Registerkarte Konsole in den F12-Tools, um den Fehler anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="4de31-592">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="4de31-593">Je nach Browser erhalten Sie einen Fehler (in der F12-Tools-Konsole), der den folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="4de31-593">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="4de31-594">Verwenden von Microsoft Edge:</span><span class="sxs-lookup"><span data-stu-id="4de31-594">Using Microsoft Edge:</span></span>

     <span data-ttu-id="4de31-595">**SEC7120: [CORS] `https://localhost:44375` Der Ursprung `https://localhost:44375` wurde im Access-Control-Allow-Origin-Antwortheader für ursprungsübergreifende Ressource unter`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="4de31-595">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="4de31-596">Verwenden von Chrome:</span><span class="sxs-lookup"><span data-stu-id="4de31-596">Using Chrome:</span></span>

     <span data-ttu-id="4de31-597">**Der Zugriff auf `https://webapi.azurewebsites.net/api/values/1` XMLHttpRequest vom Ursprung `https://localhost:44375` wurde durch die CORS-Richtlinie blockiert: In der angeforderten Ressource ist kein 'Access-Control-Allow-Origin'-Header vorhanden.**</span><span class="sxs-lookup"><span data-stu-id="4de31-597">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="4de31-598">CORS-fähige Endpunkte können mit einem Tool getestet werden, z. B. [Fiddler](https://www.telerik.com/fiddler) oder [Postman](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="4de31-598">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="4de31-599">Bei Verwendung eines Tools muss der Ursprung `Origin` der vom Header angegebenen Anforderung vom Host, der die Anforderung empfängt, abweichen.</span><span class="sxs-lookup"><span data-stu-id="4de31-599">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="4de31-600">Wenn die Anforderung nicht *ursprungsübergreifend* ist, basiert `Origin` sie auf dem Wert des Headers:</span><span class="sxs-lookup"><span data-stu-id="4de31-600">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="4de31-601">CORS Middleware muss die Anforderung nicht verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="4de31-601">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="4de31-602">CORS-Header werden in der Antwort nicht zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="4de31-602">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="4de31-603">CORS in IIS</span><span class="sxs-lookup"><span data-stu-id="4de31-603">CORS in IIS</span></span>

<span data-ttu-id="4de31-604">Bei der Bereitstellung in IIS muss CORS vor der Windows-Authentifizierung ausgeführt werden, wenn der Server nicht für den anonymen Zugriff konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="4de31-604">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="4de31-605">Um dieses Szenario zu unterstützen, muss das [IIS CORS-Modul](https://www.iis.net/downloads/microsoft/iis-cors-module) für die App installiert und konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="4de31-605">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4de31-606">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="4de31-606">Additional resources</span></span>

* [<span data-ttu-id="4de31-607">Cross-Origin Resource Sharing (CORS)</span><span class="sxs-lookup"><span data-stu-id="4de31-607">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="4de31-608">Erste Schritte mit dem IIS CORS-Modul</span><span class="sxs-lookup"><span data-stu-id="4de31-608">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

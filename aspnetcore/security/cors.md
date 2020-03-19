---
title: Aktivieren von Cross-Origin-Anforderungen (cors) in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie cors als Standard zum Zulassen oder ablehnen von Ursprungs übergreifenden Anforderungen in einer ASP.net Core-app.
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
uid: security/cors
ms.openlocfilehash: 09cc296ebf605907371619124cac00883beb6abb
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511573"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="7f739-103">Aktivieren von Cross-Origin-Anforderungen (cors) in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="7f739-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7f739-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7f739-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="7f739-105">In diesem Artikel wird gezeigt, wie Sie cors in einer ASP.net Core-App aktivieren.</span><span class="sxs-lookup"><span data-stu-id="7f739-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="7f739-106">Die Browser Sicherheit verhindert, dass eine Webseite Anforderungen an eine andere Domäne sendet, als die, die die Webseite bereitgestellt hat.</span><span class="sxs-lookup"><span data-stu-id="7f739-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="7f739-107">Diese Einschränkung wird als *Richtlinie für denselben Ursprung*bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="7f739-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="7f739-108">Die Richtlinie für denselben Ursprung verhindert, dass ein böswilliger Standort vertrauliche Daten von einem anderen Standort liest.</span><span class="sxs-lookup"><span data-stu-id="7f739-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="7f739-109">Manchmal möchten Sie möglicherweise, dass andere Websites Ursprungs übergreifende Anforderungen an Ihre APP stellen.</span><span class="sxs-lookup"><span data-stu-id="7f739-109">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="7f739-110">Weitere Informationen finden Sie im [Artikel zu Mozilla cors](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="7f739-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="7f739-111">[Cross-Origin Resource Sharing](https://www.w3.org/TR/cors/) (cors):</span><span class="sxs-lookup"><span data-stu-id="7f739-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="7f739-112">Ist ein W3C-Standard, der es einem Server ermöglicht, die Richtlinie für denselben Ursprung zu lockern.</span><span class="sxs-lookup"><span data-stu-id="7f739-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="7f739-113">Bei handelt es sich **nicht** um ein Sicherheits Feature, sondern um die Sicherheit von cors.</span><span class="sxs-lookup"><span data-stu-id="7f739-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="7f739-114">Eine API wird durch das Zulassen von cors nicht sicherer.</span><span class="sxs-lookup"><span data-stu-id="7f739-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="7f739-115">Weitere Informationen finden Sie unter [Funktionsweise von cors](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="7f739-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="7f739-116">Ermöglicht einem Server das explizite zulassen einiger Ursprungs übergreifender Anforderungen, während andere abgelehnt werden.</span><span class="sxs-lookup"><span data-stu-id="7f739-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="7f739-117">Ist sicherer und flexibler als frühere Techniken, z. b. [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="7f739-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="7f739-118">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7f739-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="7f739-119">Gleicher Ursprung</span><span class="sxs-lookup"><span data-stu-id="7f739-119">Same origin</span></span>

<span data-ttu-id="7f739-120">Zwei URLs haben denselben Ursprung, wenn Sie identische Schemas, Hosts und Ports aufweisen ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="7f739-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="7f739-121">Diese beiden URLs haben denselben Ursprung:</span><span class="sxs-lookup"><span data-stu-id="7f739-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="7f739-122">Diese URLs haben andere Ursprünge als die beiden vorherigen URLs:</span><span class="sxs-lookup"><span data-stu-id="7f739-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="7f739-123">`https://example.net` &ndash; andere Domäne</span><span class="sxs-lookup"><span data-stu-id="7f739-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="7f739-124">`https://www.example.com/foo.html` &ndash; andere Unterdomäne</span><span class="sxs-lookup"><span data-stu-id="7f739-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="7f739-125">`http://example.com/foo.html` &ndash; anderes Schema</span><span class="sxs-lookup"><span data-stu-id="7f739-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="7f739-126">`https://example.com:9000/foo.html` &ndash; anderer Port</span><span class="sxs-lookup"><span data-stu-id="7f739-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="7f739-127">Internet Explorer berücksichtigt den Port nicht, wenn Ursprünge verglichen werden.</span><span class="sxs-lookup"><span data-stu-id="7f739-127">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="7f739-128">Cors mit benannten Richtlinie und Middleware</span><span class="sxs-lookup"><span data-stu-id="7f739-128">CORS with named policy and middleware</span></span>

<span data-ttu-id="7f739-129">Cors-Middleware verarbeitet Ursprungs übergreifende Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="7f739-129">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="7f739-130">Der folgende Code aktiviert cors für die gesamte App mit dem angegebenen Ursprung:</span><span class="sxs-lookup"><span data-stu-id="7f739-130">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="7f739-131">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="7f739-131">The preceding code:</span></span>

* <span data-ttu-id="7f739-132">Legt den Richtlinien Namen auf "\_myallowspecificorigins" fest.</span><span class="sxs-lookup"><span data-stu-id="7f739-132">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="7f739-133">Der Richtlinien Name ist willkürlich.</span><span class="sxs-lookup"><span data-stu-id="7f739-133">The policy name is arbitrary.</span></span>
* <span data-ttu-id="7f739-134">Ruft die <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*>-Erweiterungsmethode auf, die cors aktiviert.</span><span class="sxs-lookup"><span data-stu-id="7f739-134">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="7f739-135">Ruft <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> mit einem [Lambda-Ausdruck](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)auf.</span><span class="sxs-lookup"><span data-stu-id="7f739-135">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="7f739-136">Der Lambda-Ausdruck nimmt ein <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> Objekt an.</span><span class="sxs-lookup"><span data-stu-id="7f739-136">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="7f739-137">[Konfigurationsoptionen](#cors-policy-options), wie z. b. `WithOrigins`, werden weiter unten in diesem Artikel beschrieben.</span><span class="sxs-lookup"><span data-stu-id="7f739-137">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="7f739-138">Der <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>-Methodenaufrufe fügt cors-Dienste zum Dienst Container der APP hinzu:</span><span class="sxs-lookup"><span data-stu-id="7f739-138">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="7f739-139">Weitere Informationen finden Sie unter [cors-Richtlinien Optionen](#cpo) in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="7f739-139">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="7f739-140">Die <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>-Methode kann Methoden verketten, wie im folgenden Code gezeigt:</span><span class="sxs-lookup"><span data-stu-id="7f739-140">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="7f739-141">Hinweis: die URL darf **keinen** nachgestellten Schrägstrich (`/`) enthalten.</span><span class="sxs-lookup"><span data-stu-id="7f739-141">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="7f739-142">Wenn die URL mit `/`beendet wird, gibt der Vergleich `false` zurück, und es wird kein-Header zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="7f739-142">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="acpall"></a>

### <a name="apply-cors-policies-to-all-endpoints"></a><span data-ttu-id="7f739-143">Anwenden von cors-Richtlinien auf alle Endpunkte</span><span class="sxs-lookup"><span data-stu-id="7f739-143">Apply CORS policies to all endpoints</span></span>

<span data-ttu-id="7f739-144">Mit dem folgenden Code werden cors-Richtlinien auf alle App-Endpunkte über cors-Middleware angewendet:</span><span class="sxs-lookup"><span data-stu-id="7f739-144">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // Preceding code omitted.
    app.UseRouting();

    app.UseCors();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });

    // Following code omitted.
}
```

> [!WARNING]
> <span data-ttu-id="7f739-145">Beim Endpunkt Routing muss die cors-Middleware für die Ausführung zwischen den Aufrufen von `UseRouting` und `UseEndpoints`konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="7f739-145">With endpoint routing, the CORS middleware must be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="7f739-146">Eine falsche Konfiguration führt dazu, dass die Middleware nicht mehr ordnungsgemäß funktioniert.</span><span class="sxs-lookup"><span data-stu-id="7f739-146">Incorrect configuration will cause the middleware to stop functioning correctly.</span></span>

<span data-ttu-id="7f739-147">Weitere Informationen finden Sie unter [Aktivieren von cors in Razor Pages, Controllern und Aktionsmethoden](#ecors) zum Anwenden der cors-Richtlinie auf Seiten-/Controller-/Aktions</span><span class="sxs-lookup"><span data-stu-id="7f739-147">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="7f739-148">Anweisungen zum Testen des vorangehenden Codes finden Sie unter [Test-cors](#test) .</span><span class="sxs-lookup"><span data-stu-id="7f739-148">See [Test CORS](#test) for instructions on testing the preceding code.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="7f739-149">Aktivieren von cors mit Endpunkt Routing</span><span class="sxs-lookup"><span data-stu-id="7f739-149">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="7f739-150">Mit dem Endpunkt Routing kann cors pro Endpunkt mithilfe des `RequireCors` Satzes von Erweiterungs Methoden aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="7f739-150">With endpoint routing, CORS can be enabled on a per-endpoint basis using the `RequireCors` set of extension methods.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
  endpoints.MapGet("/echo", async context => context.Response.WriteAsync("echo"))
    .RequireCors("policy-name");
});

```

<span data-ttu-id="7f739-151">Ebenso kann cors auch für alle Controller aktiviert werden:</span><span class="sxs-lookup"><span data-stu-id="7f739-151">Similarly, CORS can also be enabled for all controllers:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
  endpoints.MapControllers().RequireCors("policy-name");
});
```

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="7f739-152">Aktivieren von cors mit Attributen</span><span class="sxs-lookup"><span data-stu-id="7f739-152">Enable CORS with attributes</span></span>

<span data-ttu-id="7f739-153">Das [&lbrack;enablecors-&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) Attribut bietet eine Alternative zum globalen Anwenden von cors.</span><span class="sxs-lookup"><span data-stu-id="7f739-153">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="7f739-154">Das `[EnableCors]`-Attribut aktiviert cors für ausgewählte Endpunkte anstelle aller Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="7f739-154">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="7f739-155">Verwenden Sie `[EnableCors]`, um die Standard Richtlinie anzugeben, und `[EnableCors("{Policy String}")]`, um eine Richtlinie anzugeben.</span><span class="sxs-lookup"><span data-stu-id="7f739-155">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="7f739-156">Das `[EnableCors]`-Attribut kann auf Folgendes angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="7f739-156">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="7f739-157">Razor Page `PageModel`</span><span class="sxs-lookup"><span data-stu-id="7f739-157">Razor Page `PageModel`</span></span>
* <span data-ttu-id="7f739-158">Controller</span><span class="sxs-lookup"><span data-stu-id="7f739-158">Controller</span></span>
* <span data-ttu-id="7f739-159">Controller-Aktionsmethode</span><span class="sxs-lookup"><span data-stu-id="7f739-159">Controller action method</span></span>

<span data-ttu-id="7f739-160">Mit dem `[EnableCors]`-Attribut können Sie verschiedene Richtlinien auf Controller/Seiten Modell/Aktion anwenden.</span><span class="sxs-lookup"><span data-stu-id="7f739-160">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="7f739-161">Wenn das `[EnableCors]`-Attribut auf eine Controller-/Seiten-/Aktionsmethode angewendet wird und cors in der Middleware aktiviert ist, werden beide Richtlinien angewendet.</span><span class="sxs-lookup"><span data-stu-id="7f739-161">When the `[EnableCors]` attribute is applied to a controllers/page-model/action method, and CORS is enabled in middleware, both policies are applied.</span></span> <span data-ttu-id="7f739-162">Es wird empfohlen, Richtlinien zu kombinieren.</span><span class="sxs-lookup"><span data-stu-id="7f739-162">We recommend against combining policies.</span></span> <span data-ttu-id="7f739-163">Verwenden Sie das `[EnableCors]` Attribut oder die Middleware, nicht beide in der gleichen app.</span><span class="sxs-lookup"><span data-stu-id="7f739-163">Use the `[EnableCors]` attribute or middleware, not both in the same app.</span></span>

<span data-ttu-id="7f739-164">Der folgende Code wendet eine andere Richtlinie auf jede Methode an:</span><span class="sxs-lookup"><span data-stu-id="7f739-164">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="7f739-165">Der folgende Code erstellt eine cors-Standard Richtlinie und eine Richtlinie mit dem Namen `"AnotherPolicy"`:</span><span class="sxs-lookup"><span data-stu-id="7f739-165">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="7f739-166">Cors deaktivieren</span><span class="sxs-lookup"><span data-stu-id="7f739-166">Disable CORS</span></span>

<span data-ttu-id="7f739-167">Das [&lbrack;disablecors-&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) Attribut deaktiviert cors für Controller/Seiten Modell/Aktion.</span><span class="sxs-lookup"><span data-stu-id="7f739-167">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="7f739-168">Cors-Richtlinien Optionen</span><span class="sxs-lookup"><span data-stu-id="7f739-168">CORS policy options</span></span>

<span data-ttu-id="7f739-169">In diesem Abschnitt werden die verschiedenen Optionen beschrieben, die in einer cors-Richtlinie festgelegt werden können:</span><span class="sxs-lookup"><span data-stu-id="7f739-169">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="7f739-170">Festlegen der zulässigen Ursprünge</span><span class="sxs-lookup"><span data-stu-id="7f739-170">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="7f739-171">Festlegen der zulässigen HTTP-Methoden</span><span class="sxs-lookup"><span data-stu-id="7f739-171">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="7f739-172">Festlegen der zulässigen Anforderungs Header</span><span class="sxs-lookup"><span data-stu-id="7f739-172">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="7f739-173">Festlegen der verfügbar gemachten Antwortheader</span><span class="sxs-lookup"><span data-stu-id="7f739-173">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="7f739-174">Anmelde Informationen in Ursprungs übergreifenden Anforderungen</span><span class="sxs-lookup"><span data-stu-id="7f739-174">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="7f739-175">Festlegen der Preflight-Ablaufzeit</span><span class="sxs-lookup"><span data-stu-id="7f739-175">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="7f739-176"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> wird in `Startup.ConfigureServices`aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="7f739-176"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7f739-177">Für einige Optionen kann es hilfreich sein, zuerst den Abschnitt [wie cors Works](#how-cors) zu lesen.</span><span class="sxs-lookup"><span data-stu-id="7f739-177">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="7f739-178">Festlegen der zulässigen Ursprünge</span><span class="sxs-lookup"><span data-stu-id="7f739-178">Set the allowed origins</span></span>

<span data-ttu-id="7f739-179"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; ermöglicht die Verwendung von cors-Anforderungen von allen Ursprüngen mit einem beliebigen Schema (`http` oder `https`).</span><span class="sxs-lookup"><span data-stu-id="7f739-179"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="7f739-180">`AllowAnyOrigin` ist unsicher, weil *jede Website* Ursprungs übergreifende Anforderungen an die APP stellen kann.</span><span class="sxs-lookup"><span data-stu-id="7f739-180">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="7f739-181">Das Angeben von `AllowAnyOrigin` und `AllowCredentials` ist eine unsichere Konfiguration und kann zu einer Website übergreifenden Anforderungs Fälschung führen.</span><span class="sxs-lookup"><span data-stu-id="7f739-181">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="7f739-182">Der cors-Dienst gibt eine ungültige cors-Antwort zurück, wenn eine APP mit beiden Methoden konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="7f739-182">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="7f739-183">`AllowAnyOrigin` wirkt sich auf Preflight-Anforderungen und den `Access-Control-Allow-Origin`-Header aus.</span><span class="sxs-lookup"><span data-stu-id="7f739-183">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="7f739-184">Weitere Informationen finden Sie im Abschnitt [Preflight Requests (Preflight-Anforderungen](#preflight-requests) ).</span><span class="sxs-lookup"><span data-stu-id="7f739-184">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="7f739-185"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; legt die Eigenschaft <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> der Richtlinie auf eine Funktion fest, mit der Ursprünge einer konfigurierten Platzhalter Domäne zugeordnet werden können, wenn ausgewertet wird, ob der Ursprung zulässig ist.</span><span class="sxs-lookup"><span data-stu-id="7f739-185"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="7f739-186">Festlegen der zulässigen HTTP-Methoden</span><span class="sxs-lookup"><span data-stu-id="7f739-186">Set the allowed HTTP methods</span></span>

<span data-ttu-id="7f739-187"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="7f739-187"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="7f739-188">Lässt jede HTTP-Methode zu:</span><span class="sxs-lookup"><span data-stu-id="7f739-188">Allows any HTTP method:</span></span>
* <span data-ttu-id="7f739-189">Wirkt sich auf Preflight-Anforderungen und den `Access-Control-Allow-Methods`-Header aus.</span><span class="sxs-lookup"><span data-stu-id="7f739-189">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="7f739-190">Weitere Informationen finden Sie im Abschnitt [Preflight Requests (Preflight-Anforderungen](#preflight-requests) ).</span><span class="sxs-lookup"><span data-stu-id="7f739-190">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="7f739-191">Festlegen der zulässigen Anforderungs Header</span><span class="sxs-lookup"><span data-stu-id="7f739-191">Set the allowed request headers</span></span>

<span data-ttu-id="7f739-192">Um zu ermöglichen, dass bestimmte Header in einer cors-Anforderung gesendet werden, die als *Autor Request Headers*bezeichnet wird, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> aufrufen, und geben Sie die zulässigen Header an:</span><span class="sxs-lookup"><span data-stu-id="7f739-192">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="7f739-193">Um alle Autoren Anforderungs Header zuzulassen, wenden Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>an:</span><span class="sxs-lookup"><span data-stu-id="7f739-193">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="7f739-194">Diese Einstellung wirkt sich auf Preflight-Anforderungen und den `Access-Control-Request-Headers`-Header aus.</span><span class="sxs-lookup"><span data-stu-id="7f739-194">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="7f739-195">Weitere Informationen finden Sie im Abschnitt [Preflight Requests (Preflight-Anforderungen](#preflight-requests) ).</span><span class="sxs-lookup"><span data-stu-id="7f739-195">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>


<span data-ttu-id="7f739-196">Eine cors-Middleware-Richtlinie ist mit bestimmten Headern übereinstimmen, die in `WithHeaders` angegeben sind, ist nur möglich, wenn die in `Access-Control-Request-Headers` gesendeten Header exakt mit den in `WithHeaders`angegebenen</span><span class="sxs-lookup"><span data-stu-id="7f739-196">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="7f739-197">Nehmen Sie beispielsweise an, dass eine APP wie folgt konfiguriert ist:</span><span class="sxs-lookup"><span data-stu-id="7f739-197">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="7f739-198">Cors-Middleware lehnt eine Preflight-Anforderung mit dem folgenden Anforderungs Header ab, weil `Content-Language` ([headernames. contentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) nicht in `WithHeaders`aufgeführt ist:</span><span class="sxs-lookup"><span data-stu-id="7f739-198">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="7f739-199">Die APP gibt eine *200 OK* -Antwort zurück, sendet jedoch keine cors-Header zurück.</span><span class="sxs-lookup"><span data-stu-id="7f739-199">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="7f739-200">Der Browser versucht daher nicht, die Ursprungs übergreifende Anforderung zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="7f739-200">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="7f739-201">Festlegen der verfügbar gemachten Antwortheader</span><span class="sxs-lookup"><span data-stu-id="7f739-201">Set the exposed response headers</span></span>

<span data-ttu-id="7f739-202">Standardmäßig macht der Browser nicht alle Antwortheader für die app verfügbar.</span><span class="sxs-lookup"><span data-stu-id="7f739-202">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="7f739-203">Weitere Informationen finden Sie unter [W3C Cross-Origin Resource Sharing (Terminologie): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="7f739-203">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="7f739-204">Die standardmäßig verfügbaren Antwortheader lauten wie folgt:</span><span class="sxs-lookup"><span data-stu-id="7f739-204">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="7f739-205">Mit der cors-Spezifikation werden die Header für *einfache Antworten*aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="7f739-205">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="7f739-206">Um andere Header für die app verfügbar zu machen, wenden Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>an:</span><span class="sxs-lookup"><span data-stu-id="7f739-206">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="7f739-207">Anmelde Informationen in Ursprungs übergreifenden Anforderungen</span><span class="sxs-lookup"><span data-stu-id="7f739-207">Credentials in cross-origin requests</span></span>

<span data-ttu-id="7f739-208">Anmelde Informationen erfordern eine spezielle Behandlung in einer cors-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="7f739-208">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="7f739-209">Standardmäßig sendet der Browser keine Anmelde Informationen mit einer Ursprungs übergreifenden Anforderung.</span><span class="sxs-lookup"><span data-stu-id="7f739-209">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="7f739-210">Anmelde Informationen umfassen Cookies und http-Authentifizierungs Schemas.</span><span class="sxs-lookup"><span data-stu-id="7f739-210">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="7f739-211">Um Anmelde Informationen mit einer Ursprungs übergreifenden Anforderung zu senden, muss der Client `XMLHttpRequest.withCredentials` auf `true`festlegen.</span><span class="sxs-lookup"><span data-stu-id="7f739-211">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="7f739-212">Direkte Verwendung von `XMLHttpRequest`:</span><span class="sxs-lookup"><span data-stu-id="7f739-212">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="7f739-213">Verwenden von jQuery:</span><span class="sxs-lookup"><span data-stu-id="7f739-213">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="7f739-214">Verwenden der [Fetch-API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="7f739-214">Using the [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="7f739-215">Der Server muss die Anmelde Informationen zulassen.</span><span class="sxs-lookup"><span data-stu-id="7f739-215">The server must allow the credentials.</span></span> <span data-ttu-id="7f739-216">Um Ursprungs übergreifende Anmelde Informationen zuzulassen, wenden Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>an:</span><span class="sxs-lookup"><span data-stu-id="7f739-216">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="7f739-217">Die HTTP-Antwort enthält einen `Access-Control-Allow-Credentials`-Header, der dem Browser mitteilt, dass der Server Anmelde Informationen für eine Ursprungs übergreifende Anforderung zulässt.</span><span class="sxs-lookup"><span data-stu-id="7f739-217">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="7f739-218">Wenn der Browser Anmelde Informationen sendet, die Antwort jedoch keine gültige `Access-Control-Allow-Credentials` Kopfzeile enthält, macht der Browser die Antwort nicht an die app verfügbar, und die Ursprungs übergreifende Anforderung schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="7f739-218">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="7f739-219">Das Zulassen von Ursprungs übergreifenden Anmelde Informationen stellt ein Sicherheitsrisiko dar.</span><span class="sxs-lookup"><span data-stu-id="7f739-219">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="7f739-220">Eine Website in einer anderen Domäne kann die Anmelde Informationen des angemeldeten Benutzers ohne das Wissen des Benutzers an die APP im Auftrag des Benutzers senden.</span><span class="sxs-lookup"><span data-stu-id="7f739-220">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="7f739-221">In der cors-Spezifikation wird auch festgelegt, dass das Festlegen von Ursprüngen auf `"*"` (alle Ursprünge) ungültig ist, wenn der `Access-Control-Allow-Credentials` Header vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="7f739-221">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="7f739-222">Preflight-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="7f739-222">Preflight requests</span></span>

<span data-ttu-id="7f739-223">Bei einigen cors-Anforderungen sendet der Browser eine zusätzliche Anforderung, bevor die tatsächliche Anforderung gesendet wird.</span><span class="sxs-lookup"><span data-stu-id="7f739-223">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="7f739-224">Diese Anforderung wird als *Preflight-Anforderung*bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="7f739-224">This request is called a *preflight request*.</span></span> <span data-ttu-id="7f739-225">Der Browser kann die Preflight-Anforderung überspringen, wenn die folgenden Bedingungen zutreffen:</span><span class="sxs-lookup"><span data-stu-id="7f739-225">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="7f739-226">Die Anforderungs Methode ist Get, Head oder Post.</span><span class="sxs-lookup"><span data-stu-id="7f739-226">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="7f739-227">Von der App werden keine anderen Anforderungs Header als `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`oder `Last-Event-ID`festgelegt.</span><span class="sxs-lookup"><span data-stu-id="7f739-227">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="7f739-228">Wenn festgelegt, hat der `Content-Type`-Header einen der folgenden Werte:</span><span class="sxs-lookup"><span data-stu-id="7f739-228">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="7f739-229">Die Regel für Anforderungs Header, die für die Client Anforderung festgelegt wurde, gilt für Header, die die APP festlegt, indem Sie `setRequestHeader` für das `XMLHttpRequest`-Objekt aufrufen.</span><span class="sxs-lookup"><span data-stu-id="7f739-229">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="7f739-230">In der cors-Spezifikation werden diese Header *Anforderungs Header für Autoren*aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="7f739-230">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="7f739-231">Die Regel gilt nicht für Header, die vom Browser festgelegt werden können, z. b. `User-Agent`, `Host`oder `Content-Length`.</span><span class="sxs-lookup"><span data-stu-id="7f739-231">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="7f739-232">Im folgenden finden Sie ein Beispiel für eine Preflight-Anforderung:</span><span class="sxs-lookup"><span data-stu-id="7f739-232">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="7f739-233">Die Pre-Flight-Anforderung verwendet die HTTP OPTIONS-Methode.</span><span class="sxs-lookup"><span data-stu-id="7f739-233">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="7f739-234">Es enthält zwei spezielle Header:</span><span class="sxs-lookup"><span data-stu-id="7f739-234">It includes two special headers:</span></span>

* <span data-ttu-id="7f739-235">`Access-Control-Request-Method`: die HTTP-Methode, die für die tatsächliche Anforderung verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="7f739-235">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="7f739-236">`Access-Control-Request-Headers`: eine Liste mit Anforderungs Headern, die von der APP für die tatsächliche Anforderung festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="7f739-236">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="7f739-237">Wie bereits erwähnt, enthält dies keine Header, die vom Browser festgelegt werden, z. b. `User-Agent`.</span><span class="sxs-lookup"><span data-stu-id="7f739-237">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<span data-ttu-id="7f739-238">Eine cors-Preflight-Anforderung kann einen `Access-Control-Request-Headers`-Header enthalten, der dem Server die Header angibt, die mit der tatsächlichen Anforderung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="7f739-238">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="7f739-239">Um bestimmte Header zuzulassen, wenden Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>an:</span><span class="sxs-lookup"><span data-stu-id="7f739-239">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="7f739-240">Um alle Autoren Anforderungs Header zuzulassen, wenden Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>an:</span><span class="sxs-lookup"><span data-stu-id="7f739-240">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="7f739-241">Browser sind nicht vollständig konsistent, wenn Sie `Access-Control-Request-Headers`festlegen.</span><span class="sxs-lookup"><span data-stu-id="7f739-241">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="7f739-242">Wenn Sie Header auf einen anderen Wert als `"*"` festlegen (oder <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>verwenden), sollten Sie mindestens `Accept`, `Content-Type`und `Origin`sowie alle benutzerdefinierten Header einschließen, die Sie unterstützen möchten.</span><span class="sxs-lookup"><span data-stu-id="7f739-242">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="7f739-243">Im folgenden finden Sie ein Beispiel für eine Antwort auf die Preflight-Anforderung (vorausgesetzt, der Server lässt die Anforderung zu):</span><span class="sxs-lookup"><span data-stu-id="7f739-243">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="7f739-244">Die Antwort enthält einen `Access-Control-Allow-Methods`-Header, der die zulässigen Methoden auflistet, und optional einen `Access-Control-Allow-Headers`-Header, der die zulässigen Header auflistet.</span><span class="sxs-lookup"><span data-stu-id="7f739-244">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="7f739-245">Wenn die Preflight-Anforderung erfolgreich ist, sendet der Browser die tatsächliche Anforderung.</span><span class="sxs-lookup"><span data-stu-id="7f739-245">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="7f739-246">Wenn die Preflight-Anforderung verweigert wird, gibt die APP eine *200 OK* -Antwort zurück, sendet jedoch keine cors-Header zurück.</span><span class="sxs-lookup"><span data-stu-id="7f739-246">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="7f739-247">Der Browser versucht daher nicht, die Ursprungs übergreifende Anforderung zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="7f739-247">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="7f739-248">Festlegen der Preflight-Ablaufzeit</span><span class="sxs-lookup"><span data-stu-id="7f739-248">Set the preflight expiration time</span></span>

<span data-ttu-id="7f739-249">Der `Access-Control-Max-Age`-Header gibt an, wie lange die Antwort auf die Preflight-Anforderung zwischengespeichert werden kann.</span><span class="sxs-lookup"><span data-stu-id="7f739-249">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="7f739-250">Um diesen Header festzulegen, wenden Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>an:</span><span class="sxs-lookup"><span data-stu-id="7f739-250">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="7f739-251">Funktionsweise von cors</span><span class="sxs-lookup"><span data-stu-id="7f739-251">How CORS works</span></span>

<span data-ttu-id="7f739-252">In diesem Abschnitt wird beschrieben, was in einer [cors](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) -Anforderung auf der Ebene der HTTP-Nachrichten geschieht.</span><span class="sxs-lookup"><span data-stu-id="7f739-252">This section describes what happens in a [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="7f739-253">Cors ist **kein** Sicherheits Feature.</span><span class="sxs-lookup"><span data-stu-id="7f739-253">CORS is **not** a security feature.</span></span> <span data-ttu-id="7f739-254">Cors ist ein W3C-Standard, der es einem Server ermöglicht, die Richtlinie für denselben Ursprung zu lockern.</span><span class="sxs-lookup"><span data-stu-id="7f739-254">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="7f739-255">Beispielsweise könnte ein böswilliger Akteur die Verwendung von [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) für Ihre Website verhindern und eine Website übergreifende Anforderung an Ihre cors-aktivierte Website ausführen, um Informationen zu stehlen.</span><span class="sxs-lookup"><span data-stu-id="7f739-255">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="7f739-256">Ihre API ist nicht sicherer, da Sie cors zulässt.</span><span class="sxs-lookup"><span data-stu-id="7f739-256">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="7f739-257">Es liegt an dem Client (Browser), cors zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="7f739-257">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="7f739-258">Der Server führt die Anforderung aus und gibt die Antwort zurück. dabei handelt es sich um den Client, der einen Fehler zurückgibt und die Antwort blockiert.</span><span class="sxs-lookup"><span data-stu-id="7f739-258">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="7f739-259">Eines der folgenden Tools zeigt z. b. die Serverantwort an:</span><span class="sxs-lookup"><span data-stu-id="7f739-259">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="7f739-260">Fiddler</span><span class="sxs-lookup"><span data-stu-id="7f739-260">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="7f739-261">Postman</span><span class="sxs-lookup"><span data-stu-id="7f739-261">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="7f739-262">.Net httpclient</span><span class="sxs-lookup"><span data-stu-id="7f739-262">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="7f739-263">Einen Webbrowser, indem Sie die URL in die Adressleiste eingeben.</span><span class="sxs-lookup"><span data-stu-id="7f739-263">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="7f739-264">Es ist eine Möglichkeit für einen Server, Browser das Ausführen einer Ursprungs übergreifenden [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) -oder [Fetch-API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) -Anforderung zu gestatten, die andernfalls unzulässig wäre.</span><span class="sxs-lookup"><span data-stu-id="7f739-264">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="7f739-265">Browser (ohne cors) können keine Ursprungs übergreifenden Anforderungen ausführen.</span><span class="sxs-lookup"><span data-stu-id="7f739-265">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="7f739-266">Vor cors wurde [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) verwendet, um diese Einschränkung zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="7f739-266">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="7f739-267">JSONP verwendet nicht XHR, sondern das `<script>`-Tag, um die Antwort zu empfangen.</span><span class="sxs-lookup"><span data-stu-id="7f739-267">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="7f739-268">Skripts können Ursprungs übergreifend geladen werden.</span><span class="sxs-lookup"><span data-stu-id="7f739-268">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="7f739-269">In der [cors-Spezifikation](https://www.w3.org/TR/cors/) wurden mehrere neue HTTP-Header eingeführt, die Ursprungs übergreifende Anforderungen ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="7f739-269">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="7f739-270">Wenn ein Browser cors unterstützt, werden diese Header automatisch für Ursprungs übergreifende Anforderungen festgelegt.</span><span class="sxs-lookup"><span data-stu-id="7f739-270">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="7f739-271">Benutzerdefinierter JavaScript-Code ist nicht erforderlich, um cors zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="7f739-271">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="7f739-272">Im folgenden finden Sie ein Beispiel für eine Ursprungs übergreifende Anforderung.</span><span class="sxs-lookup"><span data-stu-id="7f739-272">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="7f739-273">Der `Origin`-Header stellt die Domäne der Site bereit, von der die Anforderung stammt.</span><span class="sxs-lookup"><span data-stu-id="7f739-273">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="7f739-274">Der `Origin`-Header ist erforderlich und muss sich vom Host unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="7f739-274">The `Origin` header is required and must be different from the host.</span></span>

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

<span data-ttu-id="7f739-275">Wenn der Server die Anforderung zulässt, wird der `Access-Control-Allow-Origin`-Header in der Antwort festgelegt.</span><span class="sxs-lookup"><span data-stu-id="7f739-275">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="7f739-276">Der Wert dieses Headers stimmt entweder mit dem `Origin`-Header aus der Anforderung überein, oder ist der Platzhalter Wert `"*"`. Dies bedeutet, dass ein beliebiger Ursprung zulässig ist:</span><span class="sxs-lookup"><span data-stu-id="7f739-276">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="7f739-277">Wenn die Antwort nicht den `Access-Control-Allow-Origin`-Header enthält, schlägt die Ursprungs übergreifende Anforderung fehl.</span><span class="sxs-lookup"><span data-stu-id="7f739-277">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="7f739-278">Der Browser lässt die Anforderung insbesondere nicht zu.</span><span class="sxs-lookup"><span data-stu-id="7f739-278">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="7f739-279">Auch wenn der Server eine erfolgreiche Antwort zurückgibt, stellt der Browser die Antwort nicht für die Client-App zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="7f739-279">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="7f739-280">Testen von CORS</span><span class="sxs-lookup"><span data-stu-id="7f739-280">Test CORS</span></span>

<span data-ttu-id="7f739-281">So testen Sie cors:</span><span class="sxs-lookup"><span data-stu-id="7f739-281">To test CORS:</span></span>

1. <span data-ttu-id="7f739-282">[Erstellen Sie ein API-Projekt](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="7f739-282">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="7f739-283">Alternativ können Sie [das Beispiel herunterladen](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="7f739-283">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="7f739-284">Aktivieren Sie cors mithilfe eines der Vorgehensweisen in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="7f739-284">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="7f739-285">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="7f739-285">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="7f739-286">`WithOrigins("https://localhost:<port>");` sollten nur zum Testen einer Beispiel-App verwendet werden, die dem [Download Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)ähnelt.</span><span class="sxs-lookup"><span data-stu-id="7f739-286">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="7f739-287">Erstellen Sie ein Web-App-Projekt (Razor Pages oder MVC).</span><span class="sxs-lookup"><span data-stu-id="7f739-287">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="7f739-288">Im Beispiel wird Razor Pages verwendet.</span><span class="sxs-lookup"><span data-stu-id="7f739-288">The sample uses Razor Pages.</span></span> <span data-ttu-id="7f739-289">Sie können die Web-App in der gleichen Projekt Mappe wie das API-Projekt erstellen.</span><span class="sxs-lookup"><span data-stu-id="7f739-289">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="7f739-290">Fügen Sie der Datei " *Index. cshtml* " den folgenden hervorgehobenen Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="7f739-290">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="7f739-291">Ersetzen Sie im vorangehenden Code `url: 'https://<web app>.azurewebsites.net/api/values/1',` durch die URL zur bereitgestellten app.</span><span class="sxs-lookup"><span data-stu-id="7f739-291">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="7f739-292">Stellen Sie das API-Projekt bereit.</span><span class="sxs-lookup"><span data-stu-id="7f739-292">Deploy the API project.</span></span> <span data-ttu-id="7f739-293">Nehmen Sie beispielsweise die Bereitstellung [in Azure vor](xref:host-and-deploy/azure-apps/index).</span><span class="sxs-lookup"><span data-stu-id="7f739-293">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="7f739-294">Führen Sie die Razor Pages-oder MVC-App über den Desktop aus, und klicken Sie auf die Schaltfläche **Testen** .</span><span class="sxs-lookup"><span data-stu-id="7f739-294">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="7f739-295">Verwenden Sie die F12-Tools, um Fehlermeldungen zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="7f739-295">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="7f739-296">Entfernen Sie den localhost-Ursprung aus `WithOrigins`, und stellen Sie die APP bereit.</span><span class="sxs-lookup"><span data-stu-id="7f739-296">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="7f739-297">Alternativ können Sie die Client-App mit einem anderen Port ausführen.</span><span class="sxs-lookup"><span data-stu-id="7f739-297">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="7f739-298">Führen Sie z. b. in Visual Studio aus.</span><span class="sxs-lookup"><span data-stu-id="7f739-298">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="7f739-299">Testen Sie mit der Client-App.</span><span class="sxs-lookup"><span data-stu-id="7f739-299">Test with the client app.</span></span> <span data-ttu-id="7f739-300">Cors-Fehler geben einen Fehler zurück, aber die Fehlermeldung ist für JavaScript nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="7f739-300">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="7f739-301">Verwenden Sie die Registerkarte Konsole in den F12-Tools, um den Fehler anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="7f739-301">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="7f739-302">Abhängig vom Browser erhalten Sie eine Fehlermeldung (in der F12-toolkonsole) ähnlich der folgenden:</span><span class="sxs-lookup"><span data-stu-id="7f739-302">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="7f739-303">Verwenden von Microsoft Edge:</span><span class="sxs-lookup"><span data-stu-id="7f739-303">Using Microsoft Edge:</span></span>

     <span data-ttu-id="7f739-304">**SEC7120: [cors] der Ursprung `https://localhost:44375` `https://localhost:44375` im Antwortheader "Access-Control-Allow-Origin" für eine Ursprungs übergreifende Ressource in `https://webapi.azurewebsites.net/api/values/1` nicht gefunden wurde.**</span><span class="sxs-lookup"><span data-stu-id="7f739-304">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="7f739-305">Verwenden von Chrome:</span><span class="sxs-lookup"><span data-stu-id="7f739-305">Using Chrome:</span></span>

     <span data-ttu-id="7f739-306">**Der Zugriff auf XMLHttpRequest auf `https://webapi.azurewebsites.net/api/values/1` vom Ursprungs `https://localhost:44375` wurde durch cors-Richtlinie blockiert: Es ist kein "Access-Control-Allow-Origin"-Header für die angeforderte Ressource vorhanden.**</span><span class="sxs-lookup"><span data-stu-id="7f739-306">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="7f739-307">Cors-aktivierte Endpunkte können mit einem Tool wie z. b. " [fddler](https://www.telerik.com/fiddler) " oder [Postman](https://www.getpostman.com/)getestet werden.</span><span class="sxs-lookup"><span data-stu-id="7f739-307">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="7f739-308">Wenn Sie ein Tool verwenden, muss sich der Ursprung der vom `Origin`-Header angegebenen Anforderung von dem Host unterscheiden, der die Anforderung empfängt.</span><span class="sxs-lookup"><span data-stu-id="7f739-308">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="7f739-309">Wenn die Anforderung nicht *über einen Ursprungs* Standort basierend auf dem Wert der `Origin`-Kopfzeile ist:</span><span class="sxs-lookup"><span data-stu-id="7f739-309">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="7f739-310">Es ist nicht erforderlich, dass cors-Middleware die Anforderung verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="7f739-310">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="7f739-311">Cors-Header werden in der Antwort nicht zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="7f739-311">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="7f739-312">Cors in IIS</span><span class="sxs-lookup"><span data-stu-id="7f739-312">CORS in IIS</span></span>

<span data-ttu-id="7f739-313">Beim Bereitstellen in IIS muss cors vor der Windows-Authentifizierung ausgeführt werden, wenn der Server nicht für den anonymen Zugriff konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="7f739-313">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="7f739-314">Zur Unterstützung dieses Szenarios muss das [IIS cors-Modul](https://www.iis.net/downloads/microsoft/iis-cors-module) für die APP installiert und konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="7f739-314">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7f739-315">Weitere Ressourcen</span><span class="sxs-lookup"><span data-stu-id="7f739-315">Additional resources</span></span>

* [<span data-ttu-id="7f739-316">Cross-Origin Resource Sharing (cors)</span><span class="sxs-lookup"><span data-stu-id="7f739-316">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="7f739-317">Einführung in das IIS cors-Modul</span><span class="sxs-lookup"><span data-stu-id="7f739-317">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7f739-318">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7f739-318">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="7f739-319">In diesem Artikel wird gezeigt, wie Sie cors in einer ASP.net Core-App aktivieren.</span><span class="sxs-lookup"><span data-stu-id="7f739-319">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="7f739-320">Die Browser Sicherheit verhindert, dass eine Webseite Anforderungen an eine andere Domäne sendet, als die, die die Webseite bereitgestellt hat.</span><span class="sxs-lookup"><span data-stu-id="7f739-320">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="7f739-321">Diese Einschränkung wird als *Richtlinie für denselben Ursprung*bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="7f739-321">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="7f739-322">Die Richtlinie für denselben Ursprung verhindert, dass ein böswilliger Standort vertrauliche Daten von einem anderen Standort liest.</span><span class="sxs-lookup"><span data-stu-id="7f739-322">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="7f739-323">Manchmal möchten Sie möglicherweise, dass andere Websites Ursprungs übergreifende Anforderungen an Ihre APP stellen.</span><span class="sxs-lookup"><span data-stu-id="7f739-323">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="7f739-324">Weitere Informationen finden Sie im [Artikel zu Mozilla cors](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="7f739-324">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="7f739-325">[Cross-Origin Resource Sharing](https://www.w3.org/TR/cors/) (cors):</span><span class="sxs-lookup"><span data-stu-id="7f739-325">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="7f739-326">Ist ein W3C-Standard, der es einem Server ermöglicht, die Richtlinie für denselben Ursprung zu lockern.</span><span class="sxs-lookup"><span data-stu-id="7f739-326">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="7f739-327">Bei handelt es sich **nicht** um ein Sicherheits Feature, sondern um die Sicherheit von cors.</span><span class="sxs-lookup"><span data-stu-id="7f739-327">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="7f739-328">Eine API wird durch das Zulassen von cors nicht sicherer.</span><span class="sxs-lookup"><span data-stu-id="7f739-328">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="7f739-329">Weitere Informationen finden Sie unter [Funktionsweise von cors](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="7f739-329">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="7f739-330">Ermöglicht einem Server das explizite zulassen einiger Ursprungs übergreifender Anforderungen, während andere abgelehnt werden.</span><span class="sxs-lookup"><span data-stu-id="7f739-330">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="7f739-331">Ist sicherer und flexibler als frühere Techniken, z. b. [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="7f739-331">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="7f739-332">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7f739-332">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="7f739-333">Gleicher Ursprung</span><span class="sxs-lookup"><span data-stu-id="7f739-333">Same origin</span></span>

<span data-ttu-id="7f739-334">Zwei URLs haben denselben Ursprung, wenn Sie identische Schemas, Hosts und Ports aufweisen ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="7f739-334">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="7f739-335">Diese beiden URLs haben denselben Ursprung:</span><span class="sxs-lookup"><span data-stu-id="7f739-335">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="7f739-336">Diese URLs haben andere Ursprünge als die beiden vorherigen URLs:</span><span class="sxs-lookup"><span data-stu-id="7f739-336">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="7f739-337">`https://example.net` &ndash; andere Domäne</span><span class="sxs-lookup"><span data-stu-id="7f739-337">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="7f739-338">`https://www.example.com/foo.html` &ndash; andere Unterdomäne</span><span class="sxs-lookup"><span data-stu-id="7f739-338">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="7f739-339">`http://example.com/foo.html` &ndash; anderes Schema</span><span class="sxs-lookup"><span data-stu-id="7f739-339">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="7f739-340">`https://example.com:9000/foo.html` &ndash; anderer Port</span><span class="sxs-lookup"><span data-stu-id="7f739-340">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="7f739-341">Internet Explorer berücksichtigt den Port nicht, wenn Ursprünge verglichen werden.</span><span class="sxs-lookup"><span data-stu-id="7f739-341">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="7f739-342">Cors mit benannten Richtlinie und Middleware</span><span class="sxs-lookup"><span data-stu-id="7f739-342">CORS with named policy and middleware</span></span>

<span data-ttu-id="7f739-343">Cors-Middleware verarbeitet Ursprungs übergreifende Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="7f739-343">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="7f739-344">Der folgende Code aktiviert cors für die gesamte App mit dem angegebenen Ursprung:</span><span class="sxs-lookup"><span data-stu-id="7f739-344">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="7f739-345">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="7f739-345">The preceding code:</span></span>

* <span data-ttu-id="7f739-346">Legt den Richtlinien Namen auf "\_myallowspecificorigins" fest.</span><span class="sxs-lookup"><span data-stu-id="7f739-346">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="7f739-347">Der Richtlinien Name ist willkürlich.</span><span class="sxs-lookup"><span data-stu-id="7f739-347">The policy name is arbitrary.</span></span>
* <span data-ttu-id="7f739-348">Ruft die <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*>-Erweiterungsmethode auf, die cors aktiviert.</span><span class="sxs-lookup"><span data-stu-id="7f739-348">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="7f739-349">Ruft <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> mit einem [Lambda-Ausdruck](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)auf.</span><span class="sxs-lookup"><span data-stu-id="7f739-349">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="7f739-350">Der Lambda-Ausdruck nimmt ein <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> Objekt an.</span><span class="sxs-lookup"><span data-stu-id="7f739-350">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="7f739-351">[Konfigurationsoptionen](#cors-policy-options), wie z. b. `WithOrigins`, werden weiter unten in diesem Artikel beschrieben.</span><span class="sxs-lookup"><span data-stu-id="7f739-351">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="7f739-352">Der <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>-Methodenaufrufe fügt cors-Dienste zum Dienst Container der APP hinzu:</span><span class="sxs-lookup"><span data-stu-id="7f739-352">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="7f739-353">Weitere Informationen finden Sie unter [cors-Richtlinien Optionen](#cpo) in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="7f739-353">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="7f739-354">Die <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>-Methode kann Methoden verketten, wie im folgenden Code gezeigt:</span><span class="sxs-lookup"><span data-stu-id="7f739-354">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="7f739-355">Hinweis: die URL darf **keinen** nachgestellten Schrägstrich (`/`) enthalten.</span><span class="sxs-lookup"><span data-stu-id="7f739-355">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="7f739-356">Wenn die URL mit `/`beendet wird, gibt der Vergleich `false` zurück, und es wird kein-Header zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="7f739-356">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="7f739-357">Mit dem folgenden Code werden cors-Richtlinien auf alle App-Endpunkte über cors-Middleware angewendet:</span><span class="sxs-lookup"><span data-stu-id="7f739-357">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
<span data-ttu-id="7f739-358">Hinweis: `UseCors` muss vor `UseMvc`aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="7f739-358">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="7f739-359">Weitere Informationen finden Sie unter [Aktivieren von cors in Razor Pages, Controllern und Aktionsmethoden](#ecors) zum Anwenden der cors-Richtlinie auf Seiten-/Controller-/Aktions</span><span class="sxs-lookup"><span data-stu-id="7f739-359">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="7f739-360">Anweisungen zum Testen des vorangehenden Codes finden Sie unter [Test-cors](#test) .</span><span class="sxs-lookup"><span data-stu-id="7f739-360">See [Test CORS](#test) for instructions on testing the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="7f739-361">Aktivieren von cors mit Attributen</span><span class="sxs-lookup"><span data-stu-id="7f739-361">Enable CORS with attributes</span></span>

<span data-ttu-id="7f739-362">Das [&lbrack;enablecors-&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) Attribut bietet eine Alternative zum globalen Anwenden von cors.</span><span class="sxs-lookup"><span data-stu-id="7f739-362">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="7f739-363">Das `[EnableCors]`-Attribut aktiviert cors für ausgewählte Endpunkte anstelle aller Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="7f739-363">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="7f739-364">Verwenden Sie `[EnableCors]`, um die Standard Richtlinie anzugeben, und `[EnableCors("{Policy String}")]`, um eine Richtlinie anzugeben.</span><span class="sxs-lookup"><span data-stu-id="7f739-364">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="7f739-365">Das `[EnableCors]`-Attribut kann auf Folgendes angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="7f739-365">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="7f739-366">Razor Page `PageModel`</span><span class="sxs-lookup"><span data-stu-id="7f739-366">Razor Page `PageModel`</span></span>
* <span data-ttu-id="7f739-367">Controller</span><span class="sxs-lookup"><span data-stu-id="7f739-367">Controller</span></span>
* <span data-ttu-id="7f739-368">Controller-Aktionsmethode</span><span class="sxs-lookup"><span data-stu-id="7f739-368">Controller action method</span></span>

<span data-ttu-id="7f739-369">Mit dem `[EnableCors]`-Attribut können Sie verschiedene Richtlinien auf Controller/Seiten Modell/Aktion anwenden.</span><span class="sxs-lookup"><span data-stu-id="7f739-369">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="7f739-370">Wenn das `[EnableCors]`-Attribut auf eine Controller-/Seiten-/Aktionsmethode angewendet wird und cors in der Middleware aktiviert ist, werden beide Richtlinien angewendet.</span><span class="sxs-lookup"><span data-stu-id="7f739-370">When the `[EnableCors]` attribute is applied to a controllers/page-model/action method, and CORS is enabled in middleware, both policies are applied.</span></span> <span data-ttu-id="7f739-371">Es wird empfohlen, Richtlinien zu kombinieren.</span><span class="sxs-lookup"><span data-stu-id="7f739-371">We recommend against combining policies.</span></span> <span data-ttu-id="7f739-372">Verwenden Sie das `[EnableCors]` Attribut oder die Middleware, nicht beide in der gleichen app.</span><span class="sxs-lookup"><span data-stu-id="7f739-372">Use the `[EnableCors]` attribute or middleware, not both in the same app.</span></span>

<span data-ttu-id="7f739-373">Der folgende Code wendet eine andere Richtlinie auf jede Methode an:</span><span class="sxs-lookup"><span data-stu-id="7f739-373">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="7f739-374">Der folgende Code erstellt eine cors-Standard Richtlinie und eine Richtlinie mit dem Namen `"AnotherPolicy"`:</span><span class="sxs-lookup"><span data-stu-id="7f739-374">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="7f739-375">Cors deaktivieren</span><span class="sxs-lookup"><span data-stu-id="7f739-375">Disable CORS</span></span>

<span data-ttu-id="7f739-376">Das [&lbrack;disablecors-&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) Attribut deaktiviert cors für Controller/Seiten Modell/Aktion.</span><span class="sxs-lookup"><span data-stu-id="7f739-376">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="7f739-377">Cors-Richtlinien Optionen</span><span class="sxs-lookup"><span data-stu-id="7f739-377">CORS policy options</span></span>

<span data-ttu-id="7f739-378">In diesem Abschnitt werden die verschiedenen Optionen beschrieben, die in einer cors-Richtlinie festgelegt werden können:</span><span class="sxs-lookup"><span data-stu-id="7f739-378">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="7f739-379">Festlegen der zulässigen Ursprünge</span><span class="sxs-lookup"><span data-stu-id="7f739-379">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="7f739-380">Festlegen der zulässigen HTTP-Methoden</span><span class="sxs-lookup"><span data-stu-id="7f739-380">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="7f739-381">Festlegen der zulässigen Anforderungs Header</span><span class="sxs-lookup"><span data-stu-id="7f739-381">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="7f739-382">Festlegen der verfügbar gemachten Antwortheader</span><span class="sxs-lookup"><span data-stu-id="7f739-382">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="7f739-383">Anmelde Informationen in Ursprungs übergreifenden Anforderungen</span><span class="sxs-lookup"><span data-stu-id="7f739-383">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="7f739-384">Festlegen der Preflight-Ablaufzeit</span><span class="sxs-lookup"><span data-stu-id="7f739-384">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="7f739-385"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> wird in `Startup.ConfigureServices`aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="7f739-385"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7f739-386">Für einige Optionen kann es hilfreich sein, zuerst den Abschnitt [wie cors Works](#how-cors) zu lesen.</span><span class="sxs-lookup"><span data-stu-id="7f739-386">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="7f739-387">Festlegen der zulässigen Ursprünge</span><span class="sxs-lookup"><span data-stu-id="7f739-387">Set the allowed origins</span></span>

<span data-ttu-id="7f739-388"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; ermöglicht die Verwendung von cors-Anforderungen von allen Ursprüngen mit einem beliebigen Schema (`http` oder `https`).</span><span class="sxs-lookup"><span data-stu-id="7f739-388"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="7f739-389">`AllowAnyOrigin` ist unsicher, weil *jede Website* Ursprungs übergreifende Anforderungen an die APP stellen kann.</span><span class="sxs-lookup"><span data-stu-id="7f739-389">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="7f739-390">Das Angeben von `AllowAnyOrigin` und `AllowCredentials` ist eine unsichere Konfiguration und kann zu einer Website übergreifenden Anforderungs Fälschung führen.</span><span class="sxs-lookup"><span data-stu-id="7f739-390">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="7f739-391">Geben Sie für eine sichere App eine exakte Liste von Ursprüngen an, wenn sich der Client für den Zugriff auf Server Ressourcen selbst autorisieren muss.</span><span class="sxs-lookup"><span data-stu-id="7f739-391">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="7f739-392">`AllowAnyOrigin` wirkt sich auf Preflight-Anforderungen und den `Access-Control-Allow-Origin`-Header aus.</span><span class="sxs-lookup"><span data-stu-id="7f739-392">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="7f739-393">Weitere Informationen finden Sie im Abschnitt [Preflight Requests (Preflight-Anforderungen](#preflight-requests) ).</span><span class="sxs-lookup"><span data-stu-id="7f739-393">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="7f739-394"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; legt die Eigenschaft <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> der Richtlinie auf eine Funktion fest, mit der Ursprünge einer konfigurierten Platzhalter Domäne zugeordnet werden können, wenn ausgewertet wird, ob der Ursprung zulässig ist.</span><span class="sxs-lookup"><span data-stu-id="7f739-394"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="7f739-395">Festlegen der zulässigen HTTP-Methoden</span><span class="sxs-lookup"><span data-stu-id="7f739-395">Set the allowed HTTP methods</span></span>

<span data-ttu-id="7f739-396"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="7f739-396"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="7f739-397">Lässt jede HTTP-Methode zu:</span><span class="sxs-lookup"><span data-stu-id="7f739-397">Allows any HTTP method:</span></span>
* <span data-ttu-id="7f739-398">Wirkt sich auf Preflight-Anforderungen und den `Access-Control-Allow-Methods`-Header aus.</span><span class="sxs-lookup"><span data-stu-id="7f739-398">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="7f739-399">Weitere Informationen finden Sie im Abschnitt [Preflight Requests (Preflight-Anforderungen](#preflight-requests) ).</span><span class="sxs-lookup"><span data-stu-id="7f739-399">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="7f739-400">Festlegen der zulässigen Anforderungs Header</span><span class="sxs-lookup"><span data-stu-id="7f739-400">Set the allowed request headers</span></span>

<span data-ttu-id="7f739-401">Um zu ermöglichen, dass bestimmte Header in einer cors-Anforderung gesendet werden, die als *Autor Request Headers*bezeichnet wird, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> aufrufen, und geben Sie die zulässigen Header an:</span><span class="sxs-lookup"><span data-stu-id="7f739-401">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="7f739-402">Um alle Autoren Anforderungs Header zuzulassen, wenden Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>an:</span><span class="sxs-lookup"><span data-stu-id="7f739-402">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="7f739-403">Diese Einstellung wirkt sich auf Preflight-Anforderungen und den `Access-Control-Request-Headers`-Header aus.</span><span class="sxs-lookup"><span data-stu-id="7f739-403">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="7f739-404">Weitere Informationen finden Sie im Abschnitt [Preflight Requests (Preflight-Anforderungen](#preflight-requests) ).</span><span class="sxs-lookup"><span data-stu-id="7f739-404">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="7f739-405">Cors-Middleware lässt immer zu, dass vier Header im `Access-Control-Request-Headers` unabhängig von den in corspolicy. Headers konfigurierten Werten gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="7f739-405">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="7f739-406">Diese Liste von Headern umfasst Folgendes:</span><span class="sxs-lookup"><span data-stu-id="7f739-406">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="7f739-407">Nehmen Sie beispielsweise an, dass eine APP wie folgt konfiguriert ist:</span><span class="sxs-lookup"><span data-stu-id="7f739-407">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="7f739-408">Cors-Middleware antwortet erfolgreich auf eine Preflight-Anforderung mit dem folgenden Anforderungs Header, weil `Content-Language` immer in der Whitelist enthalten ist:</span><span class="sxs-lookup"><span data-stu-id="7f739-408">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="7f739-409">Festlegen der verfügbar gemachten Antwortheader</span><span class="sxs-lookup"><span data-stu-id="7f739-409">Set the exposed response headers</span></span>

<span data-ttu-id="7f739-410">Standardmäßig macht der Browser nicht alle Antwortheader für die app verfügbar.</span><span class="sxs-lookup"><span data-stu-id="7f739-410">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="7f739-411">Weitere Informationen finden Sie unter [W3C Cross-Origin Resource Sharing (Terminologie): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="7f739-411">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="7f739-412">Die standardmäßig verfügbaren Antwortheader lauten wie folgt:</span><span class="sxs-lookup"><span data-stu-id="7f739-412">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="7f739-413">Mit der cors-Spezifikation werden die Header für *einfache Antworten*aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="7f739-413">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="7f739-414">Um andere Header für die app verfügbar zu machen, wenden Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>an:</span><span class="sxs-lookup"><span data-stu-id="7f739-414">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="7f739-415">Anmelde Informationen in Ursprungs übergreifenden Anforderungen</span><span class="sxs-lookup"><span data-stu-id="7f739-415">Credentials in cross-origin requests</span></span>

<span data-ttu-id="7f739-416">Anmelde Informationen erfordern eine spezielle Behandlung in einer cors-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="7f739-416">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="7f739-417">Standardmäßig sendet der Browser keine Anmelde Informationen mit einer Ursprungs übergreifenden Anforderung.</span><span class="sxs-lookup"><span data-stu-id="7f739-417">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="7f739-418">Anmelde Informationen umfassen Cookies und http-Authentifizierungs Schemas.</span><span class="sxs-lookup"><span data-stu-id="7f739-418">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="7f739-419">Um Anmelde Informationen mit einer Ursprungs übergreifenden Anforderung zu senden, muss der Client `XMLHttpRequest.withCredentials` auf `true`festlegen.</span><span class="sxs-lookup"><span data-stu-id="7f739-419">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="7f739-420">Direkte Verwendung von `XMLHttpRequest`:</span><span class="sxs-lookup"><span data-stu-id="7f739-420">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="7f739-421">Verwenden von jQuery:</span><span class="sxs-lookup"><span data-stu-id="7f739-421">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="7f739-422">Verwenden der [Fetch-API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="7f739-422">Using the [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="7f739-423">Der Server muss die Anmelde Informationen zulassen.</span><span class="sxs-lookup"><span data-stu-id="7f739-423">The server must allow the credentials.</span></span> <span data-ttu-id="7f739-424">Um Ursprungs übergreifende Anmelde Informationen zuzulassen, wenden Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>an:</span><span class="sxs-lookup"><span data-stu-id="7f739-424">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="7f739-425">Die HTTP-Antwort enthält einen `Access-Control-Allow-Credentials`-Header, der dem Browser mitteilt, dass der Server Anmelde Informationen für eine Ursprungs übergreifende Anforderung zulässt.</span><span class="sxs-lookup"><span data-stu-id="7f739-425">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="7f739-426">Wenn der Browser Anmelde Informationen sendet, die Antwort jedoch keine gültige `Access-Control-Allow-Credentials` Kopfzeile enthält, macht der Browser die Antwort nicht an die app verfügbar, und die Ursprungs übergreifende Anforderung schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="7f739-426">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="7f739-427">Das Zulassen von Ursprungs übergreifenden Anmelde Informationen stellt ein Sicherheitsrisiko dar.</span><span class="sxs-lookup"><span data-stu-id="7f739-427">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="7f739-428">Eine Website in einer anderen Domäne kann die Anmelde Informationen des angemeldeten Benutzers ohne das Wissen des Benutzers an die APP im Auftrag des Benutzers senden.</span><span class="sxs-lookup"><span data-stu-id="7f739-428">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="7f739-429">In der cors-Spezifikation wird auch festgelegt, dass das Festlegen von Ursprüngen auf `"*"` (alle Ursprünge) ungültig ist, wenn der `Access-Control-Allow-Credentials` Header vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="7f739-429">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="7f739-430">Preflight-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="7f739-430">Preflight requests</span></span>

<span data-ttu-id="7f739-431">Bei einigen cors-Anforderungen sendet der Browser eine zusätzliche Anforderung, bevor die tatsächliche Anforderung gesendet wird.</span><span class="sxs-lookup"><span data-stu-id="7f739-431">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="7f739-432">Diese Anforderung wird als *Preflight-Anforderung*bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="7f739-432">This request is called a *preflight request*.</span></span> <span data-ttu-id="7f739-433">Der Browser kann die Preflight-Anforderung überspringen, wenn die folgenden Bedingungen zutreffen:</span><span class="sxs-lookup"><span data-stu-id="7f739-433">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="7f739-434">Die Anforderungs Methode ist Get, Head oder Post.</span><span class="sxs-lookup"><span data-stu-id="7f739-434">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="7f739-435">Von der App werden keine anderen Anforderungs Header als `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`oder `Last-Event-ID`festgelegt.</span><span class="sxs-lookup"><span data-stu-id="7f739-435">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="7f739-436">Wenn festgelegt, hat der `Content-Type`-Header einen der folgenden Werte:</span><span class="sxs-lookup"><span data-stu-id="7f739-436">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="7f739-437">Die Regel für Anforderungs Header, die für die Client Anforderung festgelegt wurde, gilt für Header, die die APP festlegt, indem Sie `setRequestHeader` für das `XMLHttpRequest`-Objekt aufrufen.</span><span class="sxs-lookup"><span data-stu-id="7f739-437">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="7f739-438">In der cors-Spezifikation werden diese Header *Anforderungs Header für Autoren*aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="7f739-438">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="7f739-439">Die Regel gilt nicht für Header, die vom Browser festgelegt werden können, z. b. `User-Agent`, `Host`oder `Content-Length`.</span><span class="sxs-lookup"><span data-stu-id="7f739-439">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="7f739-440">Im folgenden finden Sie ein Beispiel für eine Preflight-Anforderung:</span><span class="sxs-lookup"><span data-stu-id="7f739-440">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="7f739-441">Die Pre-Flight-Anforderung verwendet die HTTP OPTIONS-Methode.</span><span class="sxs-lookup"><span data-stu-id="7f739-441">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="7f739-442">Es enthält zwei spezielle Header:</span><span class="sxs-lookup"><span data-stu-id="7f739-442">It includes two special headers:</span></span>

* <span data-ttu-id="7f739-443">`Access-Control-Request-Method`: die HTTP-Methode, die für die tatsächliche Anforderung verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="7f739-443">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="7f739-444">`Access-Control-Request-Headers`: eine Liste mit Anforderungs Headern, die von der APP für die tatsächliche Anforderung festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="7f739-444">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="7f739-445">Wie bereits erwähnt, enthält dies keine Header, die vom Browser festgelegt werden, z. b. `User-Agent`.</span><span class="sxs-lookup"><span data-stu-id="7f739-445">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<span data-ttu-id="7f739-446">Eine cors-Preflight-Anforderung kann einen `Access-Control-Request-Headers`-Header enthalten, der dem Server die Header angibt, die mit der tatsächlichen Anforderung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="7f739-446">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="7f739-447">Um bestimmte Header zuzulassen, wenden Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>an:</span><span class="sxs-lookup"><span data-stu-id="7f739-447">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="7f739-448">Um alle Autoren Anforderungs Header zuzulassen, wenden Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>an:</span><span class="sxs-lookup"><span data-stu-id="7f739-448">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="7f739-449">Browser sind nicht vollständig konsistent, wenn Sie `Access-Control-Request-Headers`festlegen.</span><span class="sxs-lookup"><span data-stu-id="7f739-449">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="7f739-450">Wenn Sie Header auf einen anderen Wert als `"*"` festlegen (oder <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>verwenden), sollten Sie mindestens `Accept`, `Content-Type`und `Origin`sowie alle benutzerdefinierten Header einschließen, die Sie unterstützen möchten.</span><span class="sxs-lookup"><span data-stu-id="7f739-450">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="7f739-451">Im folgenden finden Sie ein Beispiel für eine Antwort auf die Preflight-Anforderung (vorausgesetzt, der Server lässt die Anforderung zu):</span><span class="sxs-lookup"><span data-stu-id="7f739-451">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="7f739-452">Die Antwort enthält einen `Access-Control-Allow-Methods`-Header, der die zulässigen Methoden auflistet, und optional einen `Access-Control-Allow-Headers`-Header, der die zulässigen Header auflistet.</span><span class="sxs-lookup"><span data-stu-id="7f739-452">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="7f739-453">Wenn die Preflight-Anforderung erfolgreich ist, sendet der Browser die tatsächliche Anforderung.</span><span class="sxs-lookup"><span data-stu-id="7f739-453">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="7f739-454">Wenn die Preflight-Anforderung verweigert wird, gibt die APP eine *200 OK* -Antwort zurück, sendet jedoch keine cors-Header zurück.</span><span class="sxs-lookup"><span data-stu-id="7f739-454">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="7f739-455">Der Browser versucht daher nicht, die Ursprungs übergreifende Anforderung zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="7f739-455">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="7f739-456">Festlegen der Preflight-Ablaufzeit</span><span class="sxs-lookup"><span data-stu-id="7f739-456">Set the preflight expiration time</span></span>

<span data-ttu-id="7f739-457">Der `Access-Control-Max-Age`-Header gibt an, wie lange die Antwort auf die Preflight-Anforderung zwischengespeichert werden kann.</span><span class="sxs-lookup"><span data-stu-id="7f739-457">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="7f739-458">Um diesen Header festzulegen, wenden Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>an:</span><span class="sxs-lookup"><span data-stu-id="7f739-458">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="7f739-459">Funktionsweise von cors</span><span class="sxs-lookup"><span data-stu-id="7f739-459">How CORS works</span></span>

<span data-ttu-id="7f739-460">In diesem Abschnitt wird beschrieben, was in einer [cors](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) -Anforderung auf der Ebene der HTTP-Nachrichten geschieht.</span><span class="sxs-lookup"><span data-stu-id="7f739-460">This section describes what happens in a [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="7f739-461">Cors ist **kein** Sicherheits Feature.</span><span class="sxs-lookup"><span data-stu-id="7f739-461">CORS is **not** a security feature.</span></span> <span data-ttu-id="7f739-462">Cors ist ein W3C-Standard, der es einem Server ermöglicht, die Richtlinie für denselben Ursprung zu lockern.</span><span class="sxs-lookup"><span data-stu-id="7f739-462">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="7f739-463">Beispielsweise könnte ein böswilliger Akteur die Verwendung von [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) für Ihre Website verhindern und eine Website übergreifende Anforderung an Ihre cors-aktivierte Website ausführen, um Informationen zu stehlen.</span><span class="sxs-lookup"><span data-stu-id="7f739-463">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="7f739-464">Ihre API ist nicht sicherer, da Sie cors zulässt.</span><span class="sxs-lookup"><span data-stu-id="7f739-464">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="7f739-465">Es liegt an dem Client (Browser), cors zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="7f739-465">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="7f739-466">Der Server führt die Anforderung aus und gibt die Antwort zurück. dabei handelt es sich um den Client, der einen Fehler zurückgibt und die Antwort blockiert.</span><span class="sxs-lookup"><span data-stu-id="7f739-466">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="7f739-467">Eines der folgenden Tools zeigt z. b. die Serverantwort an:</span><span class="sxs-lookup"><span data-stu-id="7f739-467">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="7f739-468">Fiddler</span><span class="sxs-lookup"><span data-stu-id="7f739-468">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="7f739-469">Postman</span><span class="sxs-lookup"><span data-stu-id="7f739-469">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="7f739-470">.Net httpclient</span><span class="sxs-lookup"><span data-stu-id="7f739-470">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="7f739-471">Einen Webbrowser, indem Sie die URL in die Adressleiste eingeben.</span><span class="sxs-lookup"><span data-stu-id="7f739-471">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="7f739-472">Es ist eine Möglichkeit für einen Server, Browser das Ausführen einer Ursprungs übergreifenden [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) -oder [Fetch-API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) -Anforderung zu gestatten, die andernfalls unzulässig wäre.</span><span class="sxs-lookup"><span data-stu-id="7f739-472">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="7f739-473">Browser (ohne cors) können keine Ursprungs übergreifenden Anforderungen ausführen.</span><span class="sxs-lookup"><span data-stu-id="7f739-473">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="7f739-474">Vor cors wurde [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) verwendet, um diese Einschränkung zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="7f739-474">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="7f739-475">JSONP verwendet nicht XHR, sondern das `<script>`-Tag, um die Antwort zu empfangen.</span><span class="sxs-lookup"><span data-stu-id="7f739-475">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="7f739-476">Skripts können Ursprungs übergreifend geladen werden.</span><span class="sxs-lookup"><span data-stu-id="7f739-476">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="7f739-477">In der [cors-Spezifikation](https://www.w3.org/TR/cors/) wurden mehrere neue HTTP-Header eingeführt, die Ursprungs übergreifende Anforderungen ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="7f739-477">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="7f739-478">Wenn ein Browser cors unterstützt, werden diese Header automatisch für Ursprungs übergreifende Anforderungen festgelegt.</span><span class="sxs-lookup"><span data-stu-id="7f739-478">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="7f739-479">Benutzerdefinierter JavaScript-Code ist nicht erforderlich, um cors zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="7f739-479">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="7f739-480">Im folgenden finden Sie ein Beispiel für eine Ursprungs übergreifende Anforderung.</span><span class="sxs-lookup"><span data-stu-id="7f739-480">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="7f739-481">Der `Origin`-Header stellt die Domäne der Site bereit, von der die Anforderung stammt.</span><span class="sxs-lookup"><span data-stu-id="7f739-481">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="7f739-482">Der `Origin`-Header ist erforderlich und muss sich vom Host unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="7f739-482">The `Origin` header is required and must be different from the host.</span></span>

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

<span data-ttu-id="7f739-483">Wenn der Server die Anforderung zulässt, wird der `Access-Control-Allow-Origin`-Header in der Antwort festgelegt.</span><span class="sxs-lookup"><span data-stu-id="7f739-483">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="7f739-484">Der Wert dieses Headers stimmt entweder mit dem `Origin`-Header aus der Anforderung überein, oder ist der Platzhalter Wert `"*"`. Dies bedeutet, dass ein beliebiger Ursprung zulässig ist:</span><span class="sxs-lookup"><span data-stu-id="7f739-484">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="7f739-485">Wenn die Antwort nicht den `Access-Control-Allow-Origin`-Header enthält, schlägt die Ursprungs übergreifende Anforderung fehl.</span><span class="sxs-lookup"><span data-stu-id="7f739-485">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="7f739-486">Der Browser lässt die Anforderung insbesondere nicht zu.</span><span class="sxs-lookup"><span data-stu-id="7f739-486">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="7f739-487">Auch wenn der Server eine erfolgreiche Antwort zurückgibt, stellt der Browser die Antwort nicht für die Client-App zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="7f739-487">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="7f739-488">Testen von CORS</span><span class="sxs-lookup"><span data-stu-id="7f739-488">Test CORS</span></span>

<span data-ttu-id="7f739-489">So testen Sie cors:</span><span class="sxs-lookup"><span data-stu-id="7f739-489">To test CORS:</span></span>

1. <span data-ttu-id="7f739-490">[Erstellen Sie ein API-Projekt](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="7f739-490">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="7f739-491">Alternativ können Sie [das Beispiel herunterladen](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="7f739-491">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="7f739-492">Aktivieren Sie cors mithilfe eines der Vorgehensweisen in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="7f739-492">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="7f739-493">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="7f739-493">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="7f739-494">`WithOrigins("https://localhost:<port>");` sollten nur zum Testen einer Beispiel-App verwendet werden, die dem [Download Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)ähnelt.</span><span class="sxs-lookup"><span data-stu-id="7f739-494">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="7f739-495">Erstellen Sie ein Web-App-Projekt (Razor Pages oder MVC).</span><span class="sxs-lookup"><span data-stu-id="7f739-495">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="7f739-496">Im Beispiel wird Razor Pages verwendet.</span><span class="sxs-lookup"><span data-stu-id="7f739-496">The sample uses Razor Pages.</span></span> <span data-ttu-id="7f739-497">Sie können die Web-App in der gleichen Projekt Mappe wie das API-Projekt erstellen.</span><span class="sxs-lookup"><span data-stu-id="7f739-497">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="7f739-498">Fügen Sie der Datei " *Index. cshtml* " den folgenden hervorgehobenen Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="7f739-498">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="7f739-499">Ersetzen Sie im vorangehenden Code `url: 'https://<web app>.azurewebsites.net/api/values/1',` durch die URL zur bereitgestellten app.</span><span class="sxs-lookup"><span data-stu-id="7f739-499">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="7f739-500">Stellen Sie das API-Projekt bereit.</span><span class="sxs-lookup"><span data-stu-id="7f739-500">Deploy the API project.</span></span> <span data-ttu-id="7f739-501">Nehmen Sie beispielsweise die Bereitstellung [in Azure vor](xref:host-and-deploy/azure-apps/index).</span><span class="sxs-lookup"><span data-stu-id="7f739-501">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="7f739-502">Führen Sie die Razor Pages-oder MVC-App über den Desktop aus, und klicken Sie auf die Schaltfläche **Testen** .</span><span class="sxs-lookup"><span data-stu-id="7f739-502">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="7f739-503">Verwenden Sie die F12-Tools, um Fehlermeldungen zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="7f739-503">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="7f739-504">Entfernen Sie den localhost-Ursprung aus `WithOrigins`, und stellen Sie die APP bereit.</span><span class="sxs-lookup"><span data-stu-id="7f739-504">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="7f739-505">Alternativ können Sie die Client-App mit einem anderen Port ausführen.</span><span class="sxs-lookup"><span data-stu-id="7f739-505">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="7f739-506">Führen Sie z. b. in Visual Studio aus.</span><span class="sxs-lookup"><span data-stu-id="7f739-506">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="7f739-507">Testen Sie mit der Client-App.</span><span class="sxs-lookup"><span data-stu-id="7f739-507">Test with the client app.</span></span> <span data-ttu-id="7f739-508">Cors-Fehler geben einen Fehler zurück, aber die Fehlermeldung ist für JavaScript nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="7f739-508">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="7f739-509">Verwenden Sie die Registerkarte Konsole in den F12-Tools, um den Fehler anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="7f739-509">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="7f739-510">Abhängig vom Browser erhalten Sie eine Fehlermeldung (in der F12-toolkonsole) ähnlich der folgenden:</span><span class="sxs-lookup"><span data-stu-id="7f739-510">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="7f739-511">Verwenden von Microsoft Edge:</span><span class="sxs-lookup"><span data-stu-id="7f739-511">Using Microsoft Edge:</span></span>

     <span data-ttu-id="7f739-512">**SEC7120: [cors] der Ursprung `https://localhost:44375` `https://localhost:44375` im Antwortheader "Access-Control-Allow-Origin" für eine Ursprungs übergreifende Ressource in `https://webapi.azurewebsites.net/api/values/1` nicht gefunden wurde.**</span><span class="sxs-lookup"><span data-stu-id="7f739-512">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="7f739-513">Verwenden von Chrome:</span><span class="sxs-lookup"><span data-stu-id="7f739-513">Using Chrome:</span></span>

     <span data-ttu-id="7f739-514">**Der Zugriff auf XMLHttpRequest auf `https://webapi.azurewebsites.net/api/values/1` vom Ursprungs `https://localhost:44375` wurde durch cors-Richtlinie blockiert: Es ist kein "Access-Control-Allow-Origin"-Header für die angeforderte Ressource vorhanden.**</span><span class="sxs-lookup"><span data-stu-id="7f739-514">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="7f739-515">Cors-aktivierte Endpunkte können mit einem Tool wie z. b. " [fddler](https://www.telerik.com/fiddler) " oder [Postman](https://www.getpostman.com/)getestet werden.</span><span class="sxs-lookup"><span data-stu-id="7f739-515">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="7f739-516">Wenn Sie ein Tool verwenden, muss sich der Ursprung der vom `Origin`-Header angegebenen Anforderung von dem Host unterscheiden, der die Anforderung empfängt.</span><span class="sxs-lookup"><span data-stu-id="7f739-516">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="7f739-517">Wenn die Anforderung nicht *über einen Ursprungs* Standort basierend auf dem Wert der `Origin`-Kopfzeile ist:</span><span class="sxs-lookup"><span data-stu-id="7f739-517">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="7f739-518">Es ist nicht erforderlich, dass cors-Middleware die Anforderung verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="7f739-518">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="7f739-519">Cors-Header werden in der Antwort nicht zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="7f739-519">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="7f739-520">Cors in IIS</span><span class="sxs-lookup"><span data-stu-id="7f739-520">CORS in IIS</span></span>

<span data-ttu-id="7f739-521">Beim Bereitstellen in IIS muss cors vor der Windows-Authentifizierung ausgeführt werden, wenn der Server nicht für den anonymen Zugriff konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="7f739-521">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="7f739-522">Zur Unterstützung dieses Szenarios muss das [IIS cors-Modul](https://www.iis.net/downloads/microsoft/iis-cors-module) für die APP installiert und konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="7f739-522">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7f739-523">Weitere Ressourcen</span><span class="sxs-lookup"><span data-stu-id="7f739-523">Additional resources</span></span>

* [<span data-ttu-id="7f739-524">Cross-Origin Resource Sharing (cors)</span><span class="sxs-lookup"><span data-stu-id="7f739-524">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="7f739-525">Einführung in das IIS cors-Modul</span><span class="sxs-lookup"><span data-stu-id="7f739-525">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
---
title: Aktivieren von Cross-Origin-Anforderungen (cors) in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie cors als Standard zum Zulassen oder ablehnen von Ursprungs übergreifenden Anforderungen in einer ASP.net Core-app.
ms.author: riande
ms.custom: mvc
ms.date: 10/13/2019
uid: security/cors
ms.openlocfilehash: 3a51d365626c858ad48298a1108e37eba9050fe7
ms.sourcegitcommit: 35a86ce48041caaf6396b1e88b0472578ba24483
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72391296"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="bdd72-103">Aktivieren von Cross-Origin-Anforderungen (cors) in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="bdd72-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

<span data-ttu-id="bdd72-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bdd72-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="bdd72-105">In diesem Artikel wird gezeigt, wie Sie cors in einer ASP.net Core-App aktivieren.</span><span class="sxs-lookup"><span data-stu-id="bdd72-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="bdd72-106">Die Browser Sicherheit verhindert, dass eine Webseite Anforderungen an eine andere Domäne sendet, als die, die die Webseite bereitgestellt hat.</span><span class="sxs-lookup"><span data-stu-id="bdd72-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="bdd72-107">Diese Einschränkung wird als *Richtlinie für denselben Ursprung*bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="bdd72-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="bdd72-108">Die Richtlinie für denselben Ursprung verhindert, dass ein böswilliger Standort vertrauliche Daten von einem anderen Standort liest.</span><span class="sxs-lookup"><span data-stu-id="bdd72-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="bdd72-109">Manchmal möchten Sie möglicherweise, dass andere Websites Ursprungs übergreifende Anforderungen an Ihre APP stellen.</span><span class="sxs-lookup"><span data-stu-id="bdd72-109">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="bdd72-110">Weitere Informationen finden Sie im [Artikel zu Mozilla cors](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="bdd72-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="bdd72-111">[Cross-Origin Resource Sharing](https://www.w3.org/TR/cors/) (cors):</span><span class="sxs-lookup"><span data-stu-id="bdd72-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="bdd72-112">Ist ein W3C-Standard, der es einem Server ermöglicht, die Richtlinie für denselben Ursprung zu lockern.</span><span class="sxs-lookup"><span data-stu-id="bdd72-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="bdd72-113">Bei handelt es sich **nicht** um ein Sicherheits Feature, sondern um die Sicherheit von cors.</span><span class="sxs-lookup"><span data-stu-id="bdd72-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="bdd72-114">Eine API wird durch das Zulassen von cors nicht sicherer.</span><span class="sxs-lookup"><span data-stu-id="bdd72-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="bdd72-115">Weitere Informationen finden Sie unter [Funktionsweise von cors](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="bdd72-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="bdd72-116">Ermöglicht einem Server das explizite zulassen einiger Ursprungs übergreifender Anforderungen, während andere abgelehnt werden.</span><span class="sxs-lookup"><span data-stu-id="bdd72-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="bdd72-117">Ist sicherer und flexibler als frühere Techniken, z. b. [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="bdd72-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="bdd72-118">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bdd72-118">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="bdd72-119">Gleicher Ursprung</span><span class="sxs-lookup"><span data-stu-id="bdd72-119">Same origin</span></span>

<span data-ttu-id="bdd72-120">Zwei URLs haben denselben Ursprung, wenn Sie identische Schemas, Hosts und Ports aufweisen ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="bdd72-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="bdd72-121">Diese beiden URLs haben denselben Ursprung:</span><span class="sxs-lookup"><span data-stu-id="bdd72-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="bdd72-122">Diese URLs haben andere Ursprünge als die beiden vorherigen URLs:</span><span class="sxs-lookup"><span data-stu-id="bdd72-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="bdd72-123">`https://example.net` &ndash; andere Domäne</span><span class="sxs-lookup"><span data-stu-id="bdd72-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="bdd72-124">`https://www.example.com/foo.html` &ndash; andere Unterdomäne</span><span class="sxs-lookup"><span data-stu-id="bdd72-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="bdd72-125">`http://example.com/foo.html` &ndash; anderes Schema</span><span class="sxs-lookup"><span data-stu-id="bdd72-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="bdd72-126">`https://example.com:9000/foo.html` &ndash; anderer Port</span><span class="sxs-lookup"><span data-stu-id="bdd72-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="bdd72-127">Internet Explorer berücksichtigt den Port nicht, wenn Ursprünge verglichen werden.</span><span class="sxs-lookup"><span data-stu-id="bdd72-127">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="bdd72-128">Cors mit benannten Richtlinie und Middleware</span><span class="sxs-lookup"><span data-stu-id="bdd72-128">CORS with named policy and middleware</span></span>

<span data-ttu-id="bdd72-129">Cors-Middleware verarbeitet Ursprungs übergreifende Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="bdd72-129">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="bdd72-130">Der folgende Code aktiviert cors für die gesamte App mit dem angegebenen Ursprung:</span><span class="sxs-lookup"><span data-stu-id="bdd72-130">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="bdd72-131">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="bdd72-131">The preceding code:</span></span>

* <span data-ttu-id="bdd72-132">Legt den Richtlinien Namen auf "\_myallowspecificorigins" fest.</span><span class="sxs-lookup"><span data-stu-id="bdd72-132">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="bdd72-133">Der Richtlinien Name ist willkürlich.</span><span class="sxs-lookup"><span data-stu-id="bdd72-133">The policy name is arbitrary.</span></span>
* <span data-ttu-id="bdd72-134">Ruft die <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*>-Erweiterungsmethode auf, die cors aktiviert.</span><span class="sxs-lookup"><span data-stu-id="bdd72-134">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="bdd72-135">Ruft <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> mit einem [Lambda-Ausdruck](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)auf.</span><span class="sxs-lookup"><span data-stu-id="bdd72-135">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="bdd72-136">Der Lambda-Ausdruck nimmt ein <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> Objekt an.</span><span class="sxs-lookup"><span data-stu-id="bdd72-136">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="bdd72-137">[Konfigurationsoptionen](#cors-policy-options), wie z. b. `WithOrigins`, werden weiter unten in diesem Artikel beschrieben.</span><span class="sxs-lookup"><span data-stu-id="bdd72-137">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="bdd72-138">Der <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>-Methodenaufrufe fügt cors-Dienste zum Dienst Container der APP hinzu:</span><span class="sxs-lookup"><span data-stu-id="bdd72-138">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="bdd72-139">Weitere Informationen finden Sie unter [cors-Richtlinien Optionen](#cpo) in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="bdd72-139">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="bdd72-140">Die <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>-Methode kann Methoden verketten, wie im folgenden Code gezeigt:</span><span class="sxs-lookup"><span data-stu-id="bdd72-140">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="bdd72-141">Hinweis: die URL darf **keinen** nachgestellten Schrägstrich (`/`) enthalten.</span><span class="sxs-lookup"><span data-stu-id="bdd72-141">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="bdd72-142">Wenn die URL mit `/`beendet wird, gibt der Vergleich `false` zurück, und es wird kein-Header zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="bdd72-142">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

::: moniker range=">= aspnetcore-3.0"

<a name="acpall"></a>

### <a name="apply-cors-policies-to-all-endpoints"></a><span data-ttu-id="bdd72-143">Anwenden von cors-Richtlinien auf alle Endpunkte</span><span class="sxs-lookup"><span data-stu-id="bdd72-143">Apply CORS policies to all endpoints</span></span>

<span data-ttu-id="bdd72-144">Mit dem folgenden Code werden cors-Richtlinien auf alle App-Endpunkte über cors-Middleware angewendet:</span><span class="sxs-lookup"><span data-stu-id="bdd72-144">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // Preceding code ommitted.
    app.UseRouting();

    app.UseCors();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });

    // Following code ommited.
}
```

> [!WARNING]
> <span data-ttu-id="bdd72-145">Beim Endpunkt Routing muss die cors-Middleware für die Ausführung zwischen den Aufrufen von `UseRouting` und `UseEndpoints`konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="bdd72-145">With endpoint routing, the CORS middleware must be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="bdd72-146">Eine falsche Konfiguration führt dazu, dass die Middleware nicht mehr ordnungsgemäß funktioniert.</span><span class="sxs-lookup"><span data-stu-id="bdd72-146">Incorrect configuration will cause the middleware to stop functioning correctly.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"
<span data-ttu-id="bdd72-147">Mit dem folgenden Code werden cors-Richtlinien auf alle App-Endpunkte über cors-Middleware angewendet:</span><span class="sxs-lookup"><span data-stu-id="bdd72-147">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
<span data-ttu-id="bdd72-148">Hinweis: `UseCors` muss vor `UseMvc`aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="bdd72-148">Note: `UseCors` must be called before `UseMvc`.</span></span>

::: moniker-end

<span data-ttu-id="bdd72-149">Weitere Informationen finden Sie unter [Aktivieren von cors in Razor Pages, Controllern und Aktionsmethoden](#ecors) zum Anwenden der cors-Richtlinie auf Seiten-/Controller-/Aktions</span><span class="sxs-lookup"><span data-stu-id="bdd72-149">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="bdd72-150">Anweisungen zum Testen des vorangehenden Codes finden Sie unter [Test-cors](#test) .</span><span class="sxs-lookup"><span data-stu-id="bdd72-150">See [Test CORS](#test) for instructions on testing the preceding code.</span></span>

<a name="ecors"></a>

::: moniker range=">= aspnetcore-3.0"

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="bdd72-151">Aktivieren von cors mit Endpunkt Routing</span><span class="sxs-lookup"><span data-stu-id="bdd72-151">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="bdd72-152">Mit dem Endpunkt Routing kann cors pro Endpunkt mithilfe des `RequireCors` Satzes von Erweiterungs Methoden aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="bdd72-152">With endpoint routing, CORS can be enabled on a per-endpoint basis using the `RequireCors` set of extension methods.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
  endpoints.MapGet("/echo", async context => context.Response.WriteAsync("echo"))
    .RequireCors("policy-name");
});

```

<span data-ttu-id="bdd72-153">Ebenso kann cors auch für alle Controller aktiviert werden:</span><span class="sxs-lookup"><span data-stu-id="bdd72-153">Similarly, CORS can also be enabled for all controllers:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
  endpoints.MapControllers().RequireCors("policy-name");
});
```
::: moniker-end

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="bdd72-154">Aktivieren von cors mit Attributen</span><span class="sxs-lookup"><span data-stu-id="bdd72-154">Enable CORS with attributes</span></span>

<span data-ttu-id="bdd72-155">Das [&lbrack;enablecors-&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) Attribut bietet eine Alternative zum globalen Anwenden von cors.</span><span class="sxs-lookup"><span data-stu-id="bdd72-155">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="bdd72-156">Das `[EnableCors]`-Attribut aktiviert cors für ausgewählte Endpunkte anstelle aller Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="bdd72-156">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="bdd72-157">Verwenden Sie `[EnableCors]`, um die Standard Richtlinie anzugeben, und `[EnableCors("{Policy String}")]`, um eine Richtlinie anzugeben.</span><span class="sxs-lookup"><span data-stu-id="bdd72-157">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="bdd72-158">Das `[EnableCors]`-Attribut kann auf Folgendes angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="bdd72-158">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="bdd72-159">Razor Page `PageModel`</span><span class="sxs-lookup"><span data-stu-id="bdd72-159">Razor Page `PageModel`</span></span>
* <span data-ttu-id="bdd72-160">Controller</span><span class="sxs-lookup"><span data-stu-id="bdd72-160">Controller</span></span>
* <span data-ttu-id="bdd72-161">Controller-Aktionsmethode</span><span class="sxs-lookup"><span data-stu-id="bdd72-161">Controller action method</span></span>

<span data-ttu-id="bdd72-162">Mit dem `[EnableCors]`-Attribut können Sie verschiedene Richtlinien auf Controller/Seiten Modell/Aktion anwenden.</span><span class="sxs-lookup"><span data-stu-id="bdd72-162">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="bdd72-163">Wenn das `[EnableCors]`-Attribut auf eine Controller-/Seiten-/Aktionsmethode angewendet wird und cors in der Middleware aktiviert ist, werden beide Richtlinien angewendet.</span><span class="sxs-lookup"><span data-stu-id="bdd72-163">When the `[EnableCors]` attribute is applied to a controllers/page-model/action method, and CORS is enabled in middleware, both policies are applied.</span></span> <span data-ttu-id="bdd72-164">Es wird empfohlen, Richtlinien zu kombinieren.</span><span class="sxs-lookup"><span data-stu-id="bdd72-164">We recommend against combining policies.</span></span> <span data-ttu-id="bdd72-165">Verwenden Sie das `[EnableCors]` Attribut oder die Middleware, nicht beide in der gleichen app.</span><span class="sxs-lookup"><span data-stu-id="bdd72-165">Use the `[EnableCors]` attribute or middleware, not both in the same app.</span></span>

<span data-ttu-id="bdd72-166">Der folgende Code wendet eine andere Richtlinie auf jede Methode an:</span><span class="sxs-lookup"><span data-stu-id="bdd72-166">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="bdd72-167">Der folgende Code erstellt eine cors-Standard Richtlinie und eine Richtlinie mit dem Namen `"AnotherPolicy"`:</span><span class="sxs-lookup"><span data-stu-id="bdd72-167">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="bdd72-168">Cors deaktivieren</span><span class="sxs-lookup"><span data-stu-id="bdd72-168">Disable CORS</span></span>

<span data-ttu-id="bdd72-169">Das [&lbrack;disablecors-&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) Attribut deaktiviert cors für Controller/Seiten Modell/Aktion.</span><span class="sxs-lookup"><span data-stu-id="bdd72-169">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="bdd72-170">Cors-Richtlinien Optionen</span><span class="sxs-lookup"><span data-stu-id="bdd72-170">CORS policy options</span></span>

<span data-ttu-id="bdd72-171">In diesem Abschnitt werden die verschiedenen Optionen beschrieben, die in einer cors-Richtlinie festgelegt werden können:</span><span class="sxs-lookup"><span data-stu-id="bdd72-171">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="bdd72-172">Festlegen der zulässigen Ursprünge</span><span class="sxs-lookup"><span data-stu-id="bdd72-172">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="bdd72-173">Festlegen der zulässigen HTTP-Methoden</span><span class="sxs-lookup"><span data-stu-id="bdd72-173">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="bdd72-174">Festlegen der zulässigen Anforderungs Header</span><span class="sxs-lookup"><span data-stu-id="bdd72-174">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="bdd72-175">Festlegen der verfügbar gemachten Antwortheader</span><span class="sxs-lookup"><span data-stu-id="bdd72-175">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="bdd72-176">Anmelde Informationen in Ursprungs übergreifenden Anforderungen</span><span class="sxs-lookup"><span data-stu-id="bdd72-176">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="bdd72-177">Festlegen der Preflight-Ablaufzeit</span><span class="sxs-lookup"><span data-stu-id="bdd72-177">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="bdd72-178"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> wird in `Startup.ConfigureServices`aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="bdd72-178"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="bdd72-179">Für einige Optionen kann es hilfreich sein, zuerst den Abschnitt [wie cors Works](#how-cors) zu lesen.</span><span class="sxs-lookup"><span data-stu-id="bdd72-179">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="bdd72-180">Festlegen der zulässigen Ursprünge</span><span class="sxs-lookup"><span data-stu-id="bdd72-180">Set the allowed origins</span></span>

<span data-ttu-id="bdd72-181"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; ermöglicht die Verwendung von cors-Anforderungen von allen Ursprüngen mit einem beliebigen Schema (`http` oder `https`).</span><span class="sxs-lookup"><span data-stu-id="bdd72-181"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="bdd72-182">`AllowAnyOrigin` ist unsicher, weil *jede Website* Ursprungs übergreifende Anforderungen an die APP stellen kann.</span><span class="sxs-lookup"><span data-stu-id="bdd72-182">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

::: moniker range=">= aspnetcore-2.2"

> [!NOTE]
> <span data-ttu-id="bdd72-183">Das Angeben von `AllowAnyOrigin` und `AllowCredentials` ist eine unsichere Konfiguration und kann zu einer Website übergreifenden Anforderungs Fälschung führen.</span><span class="sxs-lookup"><span data-stu-id="bdd72-183">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="bdd72-184">Der cors-Dienst gibt eine ungültige cors-Antwort zurück, wenn eine APP mit beiden Methoden konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="bdd72-184">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

> [!NOTE]
> <span data-ttu-id="bdd72-185">Das Angeben von `AllowAnyOrigin` und `AllowCredentials` ist eine unsichere Konfiguration und kann zu einer Website übergreifenden Anforderungs Fälschung führen.</span><span class="sxs-lookup"><span data-stu-id="bdd72-185">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="bdd72-186">Geben Sie für eine sichere App eine exakte Liste von Ursprüngen an, wenn sich der Client für den Zugriff auf Server Ressourcen selbst autorisieren muss.</span><span class="sxs-lookup"><span data-stu-id="bdd72-186">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

::: moniker-end

<span data-ttu-id="bdd72-187">`AllowAnyOrigin` wirkt sich auf Preflight-Anforderungen und den `Access-Control-Allow-Origin`-Header aus.</span><span class="sxs-lookup"><span data-stu-id="bdd72-187">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="bdd72-188">Weitere Informationen finden Sie im Abschnitt [Preflight Requests (Preflight-Anforderungen](#preflight-requests) ).</span><span class="sxs-lookup"><span data-stu-id="bdd72-188">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="bdd72-189"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; legt die Eigenschaft <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> der Richtlinie auf eine Funktion fest, mit der Ursprünge einer konfigurierten Platzhalter Domäne zugeordnet werden können, wenn ausgewertet wird, ob der Ursprung zulässig ist.</span><span class="sxs-lookup"><span data-stu-id="bdd72-189"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

::: moniker-end

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="bdd72-190">Festlegen der zulässigen HTTP-Methoden</span><span class="sxs-lookup"><span data-stu-id="bdd72-190">Set the allowed HTTP methods</span></span>

<span data-ttu-id="bdd72-191"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="bdd72-191"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="bdd72-192">Lässt jede HTTP-Methode zu:</span><span class="sxs-lookup"><span data-stu-id="bdd72-192">Allows any HTTP method:</span></span>
* <span data-ttu-id="bdd72-193">Wirkt sich auf Preflight-Anforderungen und den `Access-Control-Allow-Methods`-Header aus.</span><span class="sxs-lookup"><span data-stu-id="bdd72-193">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="bdd72-194">Weitere Informationen finden Sie im Abschnitt [Preflight Requests (Preflight-Anforderungen](#preflight-requests) ).</span><span class="sxs-lookup"><span data-stu-id="bdd72-194">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="bdd72-195">Festlegen der zulässigen Anforderungs Header</span><span class="sxs-lookup"><span data-stu-id="bdd72-195">Set the allowed request headers</span></span>

<span data-ttu-id="bdd72-196">Um zu ermöglichen, dass bestimmte Header in einer cors-Anforderung gesendet werden, die als *Autor Request Headers*bezeichnet wird, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> aufrufen, und geben Sie die zulässigen Header an:</span><span class="sxs-lookup"><span data-stu-id="bdd72-196">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="bdd72-197">Um alle Autoren Anforderungs Header zuzulassen, wenden Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>an:</span><span class="sxs-lookup"><span data-stu-id="bdd72-197">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="bdd72-198">Diese Einstellung wirkt sich auf Preflight-Anforderungen und den `Access-Control-Request-Headers`-Header aus.</span><span class="sxs-lookup"><span data-stu-id="bdd72-198">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="bdd72-199">Weitere Informationen finden Sie im Abschnitt [Preflight Requests (Preflight-Anforderungen](#preflight-requests) ).</span><span class="sxs-lookup"><span data-stu-id="bdd72-199">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="bdd72-200">Eine cors-Middleware-Richtlinie ist mit bestimmten Headern übereinstimmen, die in `WithHeaders` angegeben sind, ist nur möglich, wenn die in `Access-Control-Request-Headers` gesendeten Header exakt mit den in `WithHeaders`angegebenen</span><span class="sxs-lookup"><span data-stu-id="bdd72-200">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="bdd72-201">Nehmen Sie beispielsweise an, dass eine APP wie folgt konfiguriert ist:</span><span class="sxs-lookup"><span data-stu-id="bdd72-201">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="bdd72-202">Cors-Middleware lehnt eine Preflight-Anforderung mit dem folgenden Anforderungs Header ab, weil `Content-Language` ([headernames. contentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) nicht in `WithHeaders`aufgeführt ist:</span><span class="sxs-lookup"><span data-stu-id="bdd72-202">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="bdd72-203">Die APP gibt eine *200 OK* -Antwort zurück, sendet jedoch keine cors-Header zurück.</span><span class="sxs-lookup"><span data-stu-id="bdd72-203">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="bdd72-204">Der Browser versucht daher nicht, die Ursprungs übergreifende Anforderung zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="bdd72-204">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="bdd72-205">Cors-Middleware lässt immer zu, dass vier Header im `Access-Control-Request-Headers` unabhängig von den in corspolicy. Headers konfigurierten Werten gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="bdd72-205">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="bdd72-206">Diese Liste von Headern umfasst Folgendes:</span><span class="sxs-lookup"><span data-stu-id="bdd72-206">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="bdd72-207">Nehmen Sie beispielsweise an, dass eine APP wie folgt konfiguriert ist:</span><span class="sxs-lookup"><span data-stu-id="bdd72-207">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="bdd72-208">Cors-Middleware antwortet erfolgreich auf eine Preflight-Anforderung mit dem folgenden Anforderungs Header, weil `Content-Language` immer in der Whitelist enthalten ist:</span><span class="sxs-lookup"><span data-stu-id="bdd72-208">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

::: moniker-end

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="bdd72-209">Festlegen der verfügbar gemachten Antwortheader</span><span class="sxs-lookup"><span data-stu-id="bdd72-209">Set the exposed response headers</span></span>

<span data-ttu-id="bdd72-210">Standardmäßig macht der Browser nicht alle Antwortheader für die app verfügbar.</span><span class="sxs-lookup"><span data-stu-id="bdd72-210">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="bdd72-211">Weitere Informationen finden Sie unter [W3C Cross-Origin Resource Sharing (Terminologie): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="bdd72-211">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="bdd72-212">Die standardmäßig verfügbaren Antwortheader lauten wie folgt:</span><span class="sxs-lookup"><span data-stu-id="bdd72-212">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="bdd72-213">Mit der cors-Spezifikation werden die Header für *einfache Antworten*aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="bdd72-213">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="bdd72-214">Um andere Header für die app verfügbar zu machen, wenden Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>an:</span><span class="sxs-lookup"><span data-stu-id="bdd72-214">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="bdd72-215">Anmelde Informationen in Ursprungs übergreifenden Anforderungen</span><span class="sxs-lookup"><span data-stu-id="bdd72-215">Credentials in cross-origin requests</span></span>

<span data-ttu-id="bdd72-216">Anmelde Informationen erfordern eine spezielle Behandlung in einer cors-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="bdd72-216">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="bdd72-217">Standardmäßig sendet der Browser keine Anmelde Informationen mit einer Ursprungs übergreifenden Anforderung.</span><span class="sxs-lookup"><span data-stu-id="bdd72-217">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="bdd72-218">Anmelde Informationen umfassen Cookies und http-Authentifizierungs Schemas.</span><span class="sxs-lookup"><span data-stu-id="bdd72-218">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="bdd72-219">Um Anmelde Informationen mit einer Ursprungs übergreifenden Anforderung zu senden, muss der Client `XMLHttpRequest.withCredentials` auf `true`festlegen.</span><span class="sxs-lookup"><span data-stu-id="bdd72-219">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="bdd72-220">Direkte Verwendung von `XMLHttpRequest`:</span><span class="sxs-lookup"><span data-stu-id="bdd72-220">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="bdd72-221">Verwenden von jQuery:</span><span class="sxs-lookup"><span data-stu-id="bdd72-221">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="bdd72-222">Verwenden der [Fetch-API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="bdd72-222">Using the [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="bdd72-223">Der Server muss die Anmelde Informationen zulassen.</span><span class="sxs-lookup"><span data-stu-id="bdd72-223">The server must allow the credentials.</span></span> <span data-ttu-id="bdd72-224">Um Ursprungs übergreifende Anmelde Informationen zuzulassen, wenden Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>an:</span><span class="sxs-lookup"><span data-stu-id="bdd72-224">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="bdd72-225">Die HTTP-Antwort enthält einen `Access-Control-Allow-Credentials`-Header, der dem Browser mitteilt, dass der Server Anmelde Informationen für eine Ursprungs übergreifende Anforderung zulässt.</span><span class="sxs-lookup"><span data-stu-id="bdd72-225">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="bdd72-226">Wenn der Browser Anmelde Informationen sendet, die Antwort jedoch keine gültige `Access-Control-Allow-Credentials` Kopfzeile enthält, macht der Browser die Antwort nicht an die app verfügbar, und die Ursprungs übergreifende Anforderung schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="bdd72-226">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="bdd72-227">Das Zulassen von Ursprungs übergreifenden Anmelde Informationen stellt ein Sicherheitsrisiko dar.</span><span class="sxs-lookup"><span data-stu-id="bdd72-227">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="bdd72-228">Eine Website in einer anderen Domäne kann die Anmelde Informationen des angemeldeten Benutzers ohne das Wissen des Benutzers an die APP im Auftrag des Benutzers senden.</span><span class="sxs-lookup"><span data-stu-id="bdd72-228">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="bdd72-229">In der cors-Spezifikation wird auch festgelegt, dass das Festlegen von Ursprüngen auf `"*"` (alle Ursprünge) ungültig ist, wenn der `Access-Control-Allow-Credentials` Header vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="bdd72-229">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="bdd72-230">Preflight-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="bdd72-230">Preflight requests</span></span>

<span data-ttu-id="bdd72-231">Bei einigen cors-Anforderungen sendet der Browser eine zusätzliche Anforderung, bevor die tatsächliche Anforderung gesendet wird.</span><span class="sxs-lookup"><span data-stu-id="bdd72-231">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="bdd72-232">Diese Anforderung wird als *Preflight-Anforderung*bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="bdd72-232">This request is called a *preflight request*.</span></span> <span data-ttu-id="bdd72-233">Der Browser kann die Preflight-Anforderung überspringen, wenn die folgenden Bedingungen zutreffen:</span><span class="sxs-lookup"><span data-stu-id="bdd72-233">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="bdd72-234">Die Anforderungs Methode ist Get, Head oder Post.</span><span class="sxs-lookup"><span data-stu-id="bdd72-234">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="bdd72-235">Von der App werden keine anderen Anforderungs Header als `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`oder `Last-Event-ID`festgelegt.</span><span class="sxs-lookup"><span data-stu-id="bdd72-235">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="bdd72-236">Wenn festgelegt, hat der `Content-Type`-Header einen der folgenden Werte:</span><span class="sxs-lookup"><span data-stu-id="bdd72-236">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="bdd72-237">Die Regel für Anforderungs Header, die für die Client Anforderung festgelegt wurde, gilt für Header, die die APP festlegt, indem Sie `setRequestHeader` für das `XMLHttpRequest`-Objekt aufrufen.</span><span class="sxs-lookup"><span data-stu-id="bdd72-237">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="bdd72-238">In der cors-Spezifikation werden diese Header *Anforderungs Header für Autoren*aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="bdd72-238">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="bdd72-239">Die Regel gilt nicht für Header, die vom Browser festgelegt werden können, z. b. `User-Agent`, `Host`oder `Content-Length`.</span><span class="sxs-lookup"><span data-stu-id="bdd72-239">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="bdd72-240">Im folgenden finden Sie ein Beispiel für eine Preflight-Anforderung:</span><span class="sxs-lookup"><span data-stu-id="bdd72-240">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="bdd72-241">Die Pre-Flight-Anforderung verwendet die HTTP OPTIONS-Methode.</span><span class="sxs-lookup"><span data-stu-id="bdd72-241">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="bdd72-242">Es enthält zwei spezielle Header:</span><span class="sxs-lookup"><span data-stu-id="bdd72-242">It includes two special headers:</span></span>

* <span data-ttu-id="bdd72-243">`Access-Control-Request-Method`: die HTTP-Methode, die für die tatsächliche Anforderung verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="bdd72-243">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="bdd72-244">`Access-Control-Request-Headers`: eine Liste mit Anforderungs Headern, die von der APP für die tatsächliche Anforderung festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="bdd72-244">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="bdd72-245">Wie bereits erwähnt, enthält dies keine Header, die vom Browser festgelegt werden, z. b. `User-Agent`.</span><span class="sxs-lookup"><span data-stu-id="bdd72-245">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<span data-ttu-id="bdd72-246">Eine cors-Preflight-Anforderung kann einen `Access-Control-Request-Headers`-Header enthalten, der dem Server die Header angibt, die mit der tatsächlichen Anforderung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="bdd72-246">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="bdd72-247">Um bestimmte Header zuzulassen, wenden Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>an:</span><span class="sxs-lookup"><span data-stu-id="bdd72-247">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="bdd72-248">Um alle Autoren Anforderungs Header zuzulassen, wenden Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>an:</span><span class="sxs-lookup"><span data-stu-id="bdd72-248">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="bdd72-249">Browser sind nicht vollständig konsistent, wenn Sie `Access-Control-Request-Headers`festlegen.</span><span class="sxs-lookup"><span data-stu-id="bdd72-249">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="bdd72-250">Wenn Sie Header auf einen anderen Wert als `"*"` festlegen (oder <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>verwenden), sollten Sie mindestens `Accept`, `Content-Type`und `Origin`sowie alle benutzerdefinierten Header einschließen, die Sie unterstützen möchten.</span><span class="sxs-lookup"><span data-stu-id="bdd72-250">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="bdd72-251">Im folgenden finden Sie ein Beispiel für eine Antwort auf die Preflight-Anforderung (vorausgesetzt, der Server lässt die Anforderung zu):</span><span class="sxs-lookup"><span data-stu-id="bdd72-251">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="bdd72-252">Die Antwort enthält einen `Access-Control-Allow-Methods`-Header, der die zulässigen Methoden auflistet, und optional einen `Access-Control-Allow-Headers`-Header, der die zulässigen Header auflistet.</span><span class="sxs-lookup"><span data-stu-id="bdd72-252">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="bdd72-253">Wenn die Preflight-Anforderung erfolgreich ist, sendet der Browser die tatsächliche Anforderung.</span><span class="sxs-lookup"><span data-stu-id="bdd72-253">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="bdd72-254">Wenn die Preflight-Anforderung verweigert wird, gibt die APP eine *200 OK* -Antwort zurück, sendet jedoch keine cors-Header zurück.</span><span class="sxs-lookup"><span data-stu-id="bdd72-254">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="bdd72-255">Der Browser versucht daher nicht, die Ursprungs übergreifende Anforderung zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="bdd72-255">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="bdd72-256">Festlegen der Preflight-Ablaufzeit</span><span class="sxs-lookup"><span data-stu-id="bdd72-256">Set the preflight expiration time</span></span>

<span data-ttu-id="bdd72-257">Der `Access-Control-Max-Age`-Header gibt an, wie lange die Antwort auf die Preflight-Anforderung zwischengespeichert werden kann.</span><span class="sxs-lookup"><span data-stu-id="bdd72-257">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="bdd72-258">Um diesen Header festzulegen, wenden Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>an:</span><span class="sxs-lookup"><span data-stu-id="bdd72-258">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="bdd72-259">Funktionsweise von cors</span><span class="sxs-lookup"><span data-stu-id="bdd72-259">How CORS works</span></span>

<span data-ttu-id="bdd72-260">In diesem Abschnitt wird beschrieben, was in einer [cors](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) -Anforderung auf der Ebene der HTTP-Nachrichten geschieht.</span><span class="sxs-lookup"><span data-stu-id="bdd72-260">This section describes what happens in a [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="bdd72-261">Cors ist **kein** Sicherheits Feature.</span><span class="sxs-lookup"><span data-stu-id="bdd72-261">CORS is **not** a security feature.</span></span> <span data-ttu-id="bdd72-262">Cors ist ein W3C-Standard, der es einem Server ermöglicht, die Richtlinie für denselben Ursprung zu lockern.</span><span class="sxs-lookup"><span data-stu-id="bdd72-262">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="bdd72-263">Beispielsweise könnte ein böswilliger Akteur die Verwendung von [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) für Ihre Website verhindern und eine Website übergreifende Anforderung an Ihre cors-aktivierte Website ausführen, um Informationen zu stehlen.</span><span class="sxs-lookup"><span data-stu-id="bdd72-263">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="bdd72-264">Ihre API ist nicht sicherer, da Sie cors zulässt.</span><span class="sxs-lookup"><span data-stu-id="bdd72-264">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="bdd72-265">Es liegt an dem Client (Browser), cors zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="bdd72-265">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="bdd72-266">Der Server führt die Anforderung aus und gibt die Antwort zurück. dabei handelt es sich um den Client, der einen Fehler zurückgibt und die Antwort blockiert.</span><span class="sxs-lookup"><span data-stu-id="bdd72-266">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="bdd72-267">Eines der folgenden Tools zeigt z. b. die Serverantwort an:</span><span class="sxs-lookup"><span data-stu-id="bdd72-267">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="bdd72-268">Fiddler</span><span class="sxs-lookup"><span data-stu-id="bdd72-268">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="bdd72-269">Postman</span><span class="sxs-lookup"><span data-stu-id="bdd72-269">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="bdd72-270">.Net httpclient</span><span class="sxs-lookup"><span data-stu-id="bdd72-270">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="bdd72-271">Einen Webbrowser, indem Sie die URL in die Adressleiste eingeben.</span><span class="sxs-lookup"><span data-stu-id="bdd72-271">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="bdd72-272">Es ist eine Möglichkeit für einen Server, Browser das Ausführen einer Ursprungs übergreifenden [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) -oder [Fetch-API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) -Anforderung zu gestatten, die andernfalls unzulässig wäre.</span><span class="sxs-lookup"><span data-stu-id="bdd72-272">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="bdd72-273">Browser (ohne cors) können keine Ursprungs übergreifenden Anforderungen ausführen.</span><span class="sxs-lookup"><span data-stu-id="bdd72-273">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="bdd72-274">Vor cors wurde [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) verwendet, um diese Einschränkung zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="bdd72-274">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="bdd72-275">JSONP verwendet nicht XHR, sondern das `<script>`-Tag, um die Antwort zu empfangen.</span><span class="sxs-lookup"><span data-stu-id="bdd72-275">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="bdd72-276">Skripts können Ursprungs übergreifend geladen werden.</span><span class="sxs-lookup"><span data-stu-id="bdd72-276">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="bdd72-277">In der [cors-Spezifikation](https://www.w3.org/TR/cors/) wurden mehrere neue HTTP-Header eingeführt, die Ursprungs übergreifende Anforderungen ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="bdd72-277">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="bdd72-278">Wenn ein Browser cors unterstützt, werden diese Header automatisch für Ursprungs übergreifende Anforderungen festgelegt.</span><span class="sxs-lookup"><span data-stu-id="bdd72-278">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="bdd72-279">Benutzerdefinierter JavaScript-Code ist nicht erforderlich, um cors zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="bdd72-279">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="bdd72-280">Im folgenden finden Sie ein Beispiel für eine Ursprungs übergreifende Anforderung.</span><span class="sxs-lookup"><span data-stu-id="bdd72-280">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="bdd72-281">Der `Origin`-Header stellt die Domäne der Site bereit, von der die Anforderung stammt.</span><span class="sxs-lookup"><span data-stu-id="bdd72-281">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="bdd72-282">Der `Origin`-Header ist erforderlich und muss sich vom Host unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="bdd72-282">The `Origin` header is required and must be different from the host.</span></span>

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

<span data-ttu-id="bdd72-283">Wenn der Server die Anforderung zulässt, wird der `Access-Control-Allow-Origin`-Header in der Antwort festgelegt.</span><span class="sxs-lookup"><span data-stu-id="bdd72-283">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="bdd72-284">Der Wert dieses Headers stimmt entweder mit dem `Origin`-Header aus der Anforderung überein, oder ist der Platzhalter Wert `"*"`. Dies bedeutet, dass ein beliebiger Ursprung zulässig ist:</span><span class="sxs-lookup"><span data-stu-id="bdd72-284">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="bdd72-285">Wenn die Antwort nicht den `Access-Control-Allow-Origin`-Header enthält, schlägt die Ursprungs übergreifende Anforderung fehl.</span><span class="sxs-lookup"><span data-stu-id="bdd72-285">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="bdd72-286">Der Browser lässt die Anforderung insbesondere nicht zu.</span><span class="sxs-lookup"><span data-stu-id="bdd72-286">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="bdd72-287">Auch wenn der Server eine erfolgreiche Antwort zurückgibt, stellt der Browser die Antwort nicht für die Client-App zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="bdd72-287">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="bdd72-288">Testen von cors</span><span class="sxs-lookup"><span data-stu-id="bdd72-288">Test CORS</span></span>

<span data-ttu-id="bdd72-289">So testen Sie cors:</span><span class="sxs-lookup"><span data-stu-id="bdd72-289">To test CORS:</span></span>

1. <span data-ttu-id="bdd72-290">[Erstellen Sie ein API-Projekt](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="bdd72-290">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="bdd72-291">Alternativ können Sie [das Beispiel herunterladen](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="bdd72-291">Alternatively, you can [download the sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="bdd72-292">Aktivieren Sie cors mithilfe eines der Vorgehensweisen in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="bdd72-292">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="bdd72-293">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="bdd72-293">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="bdd72-294">`WithOrigins("https://localhost:<port>");` sollten nur zum Testen einer Beispiel-App verwendet werden, die dem [Download Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)ähnelt.</span><span class="sxs-lookup"><span data-stu-id="bdd72-294">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="bdd72-295">Erstellen Sie ein Web-App-Projekt (Razor Pages oder MVC).</span><span class="sxs-lookup"><span data-stu-id="bdd72-295">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="bdd72-296">Im Beispiel wird Razor Pages verwendet.</span><span class="sxs-lookup"><span data-stu-id="bdd72-296">The sample uses Razor Pages.</span></span> <span data-ttu-id="bdd72-297">Sie können die Web-App in der gleichen Projekt Mappe wie das API-Projekt erstellen.</span><span class="sxs-lookup"><span data-stu-id="bdd72-297">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="bdd72-298">Fügen Sie der Datei " *Index. cshtml* " den folgenden hervorgehobenen Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="bdd72-298">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="bdd72-299">Ersetzen Sie im vorangehenden Code `url: 'https://<web app>.azurewebsites.net/api/values/1',` durch die URL zur bereitgestellten app.</span><span class="sxs-lookup"><span data-stu-id="bdd72-299">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="bdd72-300">Stellen Sie das API-Projekt bereit.</span><span class="sxs-lookup"><span data-stu-id="bdd72-300">Deploy the API project.</span></span> <span data-ttu-id="bdd72-301">Nehmen Sie beispielsweise die Bereitstellung [in Azure vor](xref:host-and-deploy/azure-apps/index).</span><span class="sxs-lookup"><span data-stu-id="bdd72-301">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="bdd72-302">Führen Sie die Razor Pages-oder MVC-App über den Desktop aus, und klicken Sie auf die Schaltfläche **Testen** .</span><span class="sxs-lookup"><span data-stu-id="bdd72-302">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="bdd72-303">Verwenden Sie die F12-Tools, um Fehlermeldungen zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="bdd72-303">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="bdd72-304">Entfernen Sie den localhost-Ursprung aus `WithOrigins`, und stellen Sie die APP bereit.</span><span class="sxs-lookup"><span data-stu-id="bdd72-304">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="bdd72-305">Alternativ können Sie die Client-App mit einem anderen Port ausführen.</span><span class="sxs-lookup"><span data-stu-id="bdd72-305">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="bdd72-306">Führen Sie z. b. in Visual Studio aus.</span><span class="sxs-lookup"><span data-stu-id="bdd72-306">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="bdd72-307">Testen Sie mit der Client-App.</span><span class="sxs-lookup"><span data-stu-id="bdd72-307">Test with the client app.</span></span> <span data-ttu-id="bdd72-308">Cors-Fehler geben einen Fehler zurück, aber die Fehlermeldung ist für JavaScript nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="bdd72-308">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="bdd72-309">Verwenden Sie die Registerkarte Konsole in den F12-Tools, um den Fehler anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="bdd72-309">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="bdd72-310">Abhängig vom Browser erhalten Sie eine Fehlermeldung (in der F12-toolkonsole) ähnlich der folgenden:</span><span class="sxs-lookup"><span data-stu-id="bdd72-310">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="bdd72-311">Verwenden von Microsoft Edge:</span><span class="sxs-lookup"><span data-stu-id="bdd72-311">Using Microsoft Edge:</span></span>

     <span data-ttu-id="bdd72-312">**SEC7120: [cors] der Ursprung `https://localhost:44375` `https://localhost:44375` im Antwortheader "Access-Control-Allow-Origin" für eine Ursprungs übergreifende Ressource in `https://webapi.azurewebsites.net/api/values/1` nicht gefunden wurde.**</span><span class="sxs-lookup"><span data-stu-id="bdd72-312">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="bdd72-313">Verwenden von Chrome:</span><span class="sxs-lookup"><span data-stu-id="bdd72-313">Using Chrome:</span></span>

     <span data-ttu-id="bdd72-314">**Der Zugriff auf XMLHttpRequest auf `https://webapi.azurewebsites.net/api/values/1` vom Ursprungs `https://localhost:44375` wurde durch cors-Richtlinie blockiert: Es ist kein "Access-Control-Allow-Origin"-Header für die angeforderte Ressource vorhanden.**</span><span class="sxs-lookup"><span data-stu-id="bdd72-314">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="bdd72-315">Cors-aktivierte Endpunkte können mit einem Tool wie z. b. " [fddler](https://www.telerik.com/fiddler) " oder [Postman](https://www.getpostman.com/)getestet werden.</span><span class="sxs-lookup"><span data-stu-id="bdd72-315">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="bdd72-316">Wenn Sie ein Tool verwenden, muss sich der Ursprung der vom `Origin`-Header angegebenen Anforderung von dem Host unterscheiden, der die Anforderung empfängt.</span><span class="sxs-lookup"><span data-stu-id="bdd72-316">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="bdd72-317">Wenn die Anforderung nicht *über einen Ursprungs* Standort basierend auf dem Wert der `Origin`-Kopfzeile ist:</span><span class="sxs-lookup"><span data-stu-id="bdd72-317">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="bdd72-318">Es ist nicht erforderlich, dass cors-Middleware die Anforderung verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="bdd72-318">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="bdd72-319">Cors-Header werden in der Antwort nicht zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="bdd72-319">CORS headers aren't returned in the response.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bdd72-320">Weitere Ressourcen</span><span class="sxs-lookup"><span data-stu-id="bdd72-320">Additional resources</span></span>

* [<span data-ttu-id="bdd72-321">Cross-Origin Resource Sharing (cors)</span><span class="sxs-lookup"><span data-stu-id="bdd72-321">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)

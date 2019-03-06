---
title: Aktivieren Sie Ursprungsübergreifender Anforderungen (CORS) in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie CORS als Standard zum Zulassen oder ablehnen von ursprungsübergreifenden Anforderungen in einer ASP.NET Core-app.
ms.author: riande
ms.custom: mvc
ms.date: 02/27/2019
uid: security/cors
ms.openlocfilehash: eb8dd3b1c96d9060b0164dcd4d0fbe004ed4af84
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57346371"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="eefee-103">Aktivieren Sie Ursprungsübergreifender Anforderungen (CORS) in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="eefee-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

<span data-ttu-id="eefee-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="eefee-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="eefee-105">In diesem Artikel wird das Aktivieren von CORS in einer ASP.NET Core-app veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="eefee-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="eefee-106">Browsersicherheit verhindert, dass eine Webseite auf Anfragen zu einer anderen Domäne als der, die die Webseite verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="eefee-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="eefee-107">Diese Einschränkung wird aufgerufen, die *Richtlinie desselben Ursprungs*.</span><span class="sxs-lookup"><span data-stu-id="eefee-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="eefee-108">Die Richtlinie desselben Ursprungs wird verhindert, dass eine schädliche Website sensible Daten von einem anderen Standort lesen.</span><span class="sxs-lookup"><span data-stu-id="eefee-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="eefee-109">In einigen Fällen empfiehlt es sich um zuzulassen, dass andere Standorte Cross-Origin-Anforderungen zu Ihrer app durchführen.</span><span class="sxs-lookup"><span data-stu-id="eefee-109">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="eefee-110">Weitere Informationen finden Sie unter den [Mozilla CORS Artikel](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="eefee-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="eefee-111">[Cross-Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="eefee-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="eefee-112">Ist ein W3C standard, die auf einem Server zu lockern die Richtlinie des gleichen Ursprungs ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="eefee-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="eefee-113">Ist **nicht** Sicherheitsfeature CORS lockert die Sicherheit.</span><span class="sxs-lookup"><span data-stu-id="eefee-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="eefee-114">Eine API ist nicht vom CORS zulassen sicherer.</span><span class="sxs-lookup"><span data-stu-id="eefee-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="eefee-115">Weitere Informationen finden Sie unter [funktioniert wie CORS](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="eefee-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="eefee-116">Kann einen Server explizit einige ursprungsübergreifende Anforderungen zulassen, während andere abgelehnt.</span><span class="sxs-lookup"><span data-stu-id="eefee-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="eefee-117">Ist sicherer und flexibler als frühere Techniken wie z. B. [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="eefee-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="eefee-118">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/2.2-stage-samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="eefee-118">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/2.2-stage-samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="eefee-119">Denselben Ursprung</span><span class="sxs-lookup"><span data-stu-id="eefee-119">Same origin</span></span>

<span data-ttu-id="eefee-120">Zwei URLs haben denselben Ursprung ggf. identische Schemas, Hosts und -Ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="eefee-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="eefee-121">Diese zwei URLs haben denselben Ursprung an:</span><span class="sxs-lookup"><span data-stu-id="eefee-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="eefee-122">Diese URLs haben verschiedene Ursprünge als die vorherigen beiden URLs:</span><span class="sxs-lookup"><span data-stu-id="eefee-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="eefee-123">`https://example.net` &ndash; Andere Domäne</span><span class="sxs-lookup"><span data-stu-id="eefee-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="eefee-124">`https://www.example.com/foo.html` &ndash; Andere Unterdomäne</span><span class="sxs-lookup"><span data-stu-id="eefee-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="eefee-125">`http://example.com/foo.html` &ndash; Anderes Schema</span><span class="sxs-lookup"><span data-stu-id="eefee-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="eefee-126">`https://example.com:9000/foo.html` &ndash; Portnummer</span><span class="sxs-lookup"><span data-stu-id="eefee-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="eefee-127">Internet Explorer berücksichtigen nicht den Port Ursprünge zu vergleichen.</span><span class="sxs-lookup"><span data-stu-id="eefee-127">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="eefee-128">CORS mithilfe der genannten Richtlinie und middleware</span><span class="sxs-lookup"><span data-stu-id="eefee-128">CORS with named policy and middleware</span></span>

<span data-ttu-id="eefee-129">CORS-Middleware verarbeitet Cross-Origin-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="eefee-129">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="eefee-130">Der folgende Code aktiviert CORS für die gesamte app mit dem angegebenen Ursprung an:</span><span class="sxs-lookup"><span data-stu-id="eefee-130">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="eefee-131">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="eefee-131">The preceding code:</span></span>

* <span data-ttu-id="eefee-132">Legt den Namen der Richtlinie auf "\_MyAllowSpecificOrigins".</span><span class="sxs-lookup"><span data-stu-id="eefee-132">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="eefee-133">Der Richtlinienname ist willkürlich.</span><span class="sxs-lookup"><span data-stu-id="eefee-133">The policy name is arbitrary.</span></span>
* <span data-ttu-id="eefee-134">Ruft die <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> Erweiterungsmethode, die Kerne ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="eefee-134">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables cores.</span></span>
* <span data-ttu-id="eefee-135">Aufrufe <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> mit einem [Lambda-Ausdruck](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="eefee-135">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="eefee-136">Das Lambda akzeptiert ein <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> Objekt.</span><span class="sxs-lookup"><span data-stu-id="eefee-136">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="eefee-137">[Optionen für die Konfiguration](#cors-policy-options), z. B. `WithOrigins`, werden weiter unten in diesem Artikel beschrieben.</span><span class="sxs-lookup"><span data-stu-id="eefee-137">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="eefee-138">Die <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> Methodenaufruf der app Service-Containers CORS-Dienste hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="eefee-138">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="eefee-139">Weitere Informationen finden Sie unter [CORS-Richtlinienoptionen](#cpo) in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="eefee-139">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="eefee-140">Die <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> Methode kann Methoden, verketten, wie im folgenden Code gezeigt:</span><span class="sxs-lookup"><span data-stu-id="eefee-140">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="eefee-141">Der folgende hervorgehobene Code gilt CORS-Richtlinien für alle apps-Endpunkte über CORS-Middleware:</span><span class="sxs-lookup"><span data-stu-id="eefee-141">The following highlighted code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>

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

<span data-ttu-id="eefee-142">Finden Sie unter [Aktivieren von CORS in Razor-Seiten, Controller und Aktionsmethoden](#ecors) anzuwendende CORS-Richtlinie auf der Seite/Controller/Aktion-Ebene.</span><span class="sxs-lookup"><span data-stu-id="eefee-142">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="eefee-143">Hinweis:</span><span class="sxs-lookup"><span data-stu-id="eefee-143">Note:</span></span>

* <span data-ttu-id="eefee-144">`UseCors` muss aufgerufen werden, bevor `UseMvc`.</span><span class="sxs-lookup"><span data-stu-id="eefee-144">`UseCors` must be called before `UseMvc`.</span></span>
* <span data-ttu-id="eefee-145">Die URL muss **nicht** keinen nachgestellten umgekehrten Schrägstrich enthalten (`/`).</span><span class="sxs-lookup"><span data-stu-id="eefee-145">The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="eefee-146">Wenn die URL mit beendet `/`, gibt der Vergleich `false` und es wird kein Header zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="eefee-146">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="eefee-147">Finden Sie unter [Test CORS](#test) Anleitungen zum Testen des obigen Codes.</span><span class="sxs-lookup"><span data-stu-id="eefee-147">See [Test CORS](#test) for instructions on testing the preceding code.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="eefee-148">Aktivieren von CORS mit Attributen</span><span class="sxs-lookup"><span data-stu-id="eefee-148">Enable CORS with attributes</span></span>

<span data-ttu-id="eefee-149">Die [ &lbrack;EnableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) Attribut bietet eine Alternative zum Anwenden von CORS Global.</span><span class="sxs-lookup"><span data-stu-id="eefee-149">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="eefee-150">Die `[EnableCors]` Attribut aktiviert CORS für den ausgewählten Endpunkt, anstatt alle Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="eefee-150">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="eefee-151">Verwendung `[EnableCors]` an die Standardrichtlinie und `[EnableCors("{Policy String}")]` eine Richtlinie angeben.</span><span class="sxs-lookup"><span data-stu-id="eefee-151">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="eefee-152">Die `[EnableCors]` Attribut kann auf angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="eefee-152">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="eefee-153">Razor-Seite `PageModel`</span><span class="sxs-lookup"><span data-stu-id="eefee-153">Razor Page `PageModel`</span></span>
* <span data-ttu-id="eefee-154">Controller</span><span class="sxs-lookup"><span data-stu-id="eefee-154">Controller</span></span>
* <span data-ttu-id="eefee-155">Aktionsmethode des Controllers</span><span class="sxs-lookup"><span data-stu-id="eefee-155">Controller action method</span></span>

<span data-ttu-id="eefee-156">Sie können unterschiedliche Richtlinien anwenden, auf der Seite "-Modell/Controlleraktion mit der `[EnableCors]` Attribut.</span><span class="sxs-lookup"><span data-stu-id="eefee-156">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="eefee-157">Wenn die `[EnableCors]` Attribut einer Controller/Seite / Modell/Aktion-Methode angewendet wird und CORS in Middleware aktiviert ist, werden beide Richtlinien angewendet.</span><span class="sxs-lookup"><span data-stu-id="eefee-157">When the `[EnableCors]` attribute is applied to a controllers/page-model/action method, and CORS is enabled in middleware, both policies are applied.</span></span> <span data-ttu-id="eefee-158">Es wird empfohlen, für das Kombinieren von Richtlinien.</span><span class="sxs-lookup"><span data-stu-id="eefee-158">We recommend against combining policies.</span></span> <span data-ttu-id="eefee-159">Verwenden der `[EnableCors]` Attribut oder nicht beide in der gleichen app-Middleware.</span><span class="sxs-lookup"><span data-stu-id="eefee-159">Use the `[EnableCors]` attribute or middleware, not both in the same app.</span></span>

<span data-ttu-id="eefee-160">Der folgende Code gilt eine andere Richtlinie für jede Methode an:</span><span class="sxs-lookup"><span data-stu-id="eefee-160">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="eefee-161">Der folgende Code erstellt eine Standardrichtlinie für CORS und eine Richtlinie, die mit dem Namen `"AnotherPolicy"`:</span><span class="sxs-lookup"><span data-stu-id="eefee-161">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="eefee-162">Deaktivieren von CORS</span><span class="sxs-lookup"><span data-stu-id="eefee-162">Disable CORS</span></span>

<span data-ttu-id="eefee-163">Die [ &lbrack;DisableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) Attribut deaktiviert CORS für die Seite "-Modell/Controlleraktion.</span><span class="sxs-lookup"><span data-stu-id="eefee-163">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="eefee-164">CORS-Richtlinie-Optionen</span><span class="sxs-lookup"><span data-stu-id="eefee-164">CORS policy options</span></span>

<span data-ttu-id="eefee-165">Dieser Abschnitt beschreibt die verschiedenen Optionen, die in einer CORS-Richtlinie festgelegt werden können:</span><span class="sxs-lookup"><span data-stu-id="eefee-165">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="eefee-166">Legen Sie die zulässigen Ursprünge</span><span class="sxs-lookup"><span data-stu-id="eefee-166">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="eefee-167">Legen Sie die zulässigen HTTP-Methoden</span><span class="sxs-lookup"><span data-stu-id="eefee-167">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="eefee-168">Legt die zulässigen Anforderungsheader fest.</span><span class="sxs-lookup"><span data-stu-id="eefee-168">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="eefee-169">Legen Sie die verfügbar gemachten Antwortheader</span><span class="sxs-lookup"><span data-stu-id="eefee-169">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="eefee-170">Anmeldeinformationen im Cross-Origin-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="eefee-170">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="eefee-171">Die Preflight-Ablaufzeit festlegen</span><span class="sxs-lookup"><span data-stu-id="eefee-171">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

 <span data-ttu-id="eefee-172"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> wird aufgerufen, `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="eefee-172"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="eefee-173">Für einige Optionen, es kann hilfreich sein, lesen Sie die [funktioniert wie CORS](#how-cors) im ersten Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="eefee-173">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="eefee-174">Legen Sie die zulässigen Ursprünge</span><span class="sxs-lookup"><span data-stu-id="eefee-174">Set the allowed origins</span></span>

<span data-ttu-id="eefee-175"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; CORS-Anforderungen von der alle Ursprünge mit einem beliebigen Schema zulässig (`http` oder `https`).</span><span class="sxs-lookup"><span data-stu-id="eefee-175"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="eefee-176">`AllowAnyOrigin` ist unsicher, da *eine beliebige Website* Cross-Origin-Anfragen an die app vornehmen können.</span><span class="sxs-lookup"><span data-stu-id="eefee-176">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

  ::: moniker range=">= aspnetcore-2.2"

  > [!NOTE]
  > <span data-ttu-id="eefee-177">Angeben von `AllowAnyOrigin` und `AllowCredentials` ist eine unsichere Konfiguration und kann dazu führen, siteübergreifende anforderungsfälschung.</span><span class="sxs-lookup"><span data-stu-id="eefee-177">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="eefee-178">Der CORS-Dienst gibt eine ungültige CORS-Antwort zurück, wenn eine app mit beiden Methoden konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="eefee-178">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

  ::: moniker-end

  ::: moniker range="< aspnetcore-2.2"

  > [!NOTE]
  > <span data-ttu-id="eefee-179">Angeben von `AllowAnyOrigin` und `AllowCredentials` ist eine unsichere Konfiguration und kann dazu führen, siteübergreifende anforderungsfälschung.</span><span class="sxs-lookup"><span data-stu-id="eefee-179">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="eefee-180">Geben Sie für eine sichere-app eine genaue Liste der Ursprünge, wenn der Client selbst auf Serverressourcen autorisieren muss.</span><span class="sxs-lookup"><span data-stu-id="eefee-180">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

  ::: moniker-end

<!-- REVIEW required
I changed from
Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration. **This** setting affects preflight requests and the ...
to
**`AllowAnyOrigin`** affects preflight requests and the

to remove the ambiguous **This**. 
-->

  <span data-ttu-id="eefee-181">`AllowAnyOrigin` wirkt sich auf preflight-Anforderungen und die `Access-Control-Allow-Origin` Header.</span><span class="sxs-lookup"><span data-stu-id="eefee-181">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="eefee-182">Weitere Informationen finden Sie unter den [Preflight-Anforderungen](#preflight-requests) Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="eefee-182">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="eefee-183"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Legt die <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> Eigenschaft der Richtlinie, die eine Funktion sein, ermöglicht Ursprünge mit eine konfigurierten Platzhalterdomäne übereinstimmen, bei der Auswertung, wenn der Ursprung zulässig ist.</span><span class="sxs-lookup"><span data-stu-id="eefee-183"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

  [!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-104&highlight=4)]

::: moniker-end

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="eefee-184">Legen Sie die zulässigen HTTP-Methoden</span><span class="sxs-lookup"><span data-stu-id="eefee-184">Set the allowed HTTP methods</span></span>

<span data-ttu-id="eefee-185"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="eefee-185"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="eefee-186">Können alle HTTP-Methode:</span><span class="sxs-lookup"><span data-stu-id="eefee-186">Allows any HTTP method:</span></span>
* <span data-ttu-id="eefee-187">Wirkt sich auf preflight-Anforderungen und die `Access-Control-Allow-Methods` Header.</span><span class="sxs-lookup"><span data-stu-id="eefee-187">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="eefee-188">Weitere Informationen finden Sie unter den [Preflight-Anforderungen](#preflight-requests) Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="eefee-188">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="eefee-189">Legt die zulässigen Anforderungsheader fest.</span><span class="sxs-lookup"><span data-stu-id="eefee-189">Set the allowed request headers</span></span>

<span data-ttu-id="eefee-190">Wird aufgerufen, um bestimmte Header in einer CORS-Anforderung gesendet werden können *erstellen Anforderungsheader*, rufen Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> , und geben Sie die erlaubten Header:</span><span class="sxs-lookup"><span data-stu-id="eefee-190">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="eefee-191">Um zuzulassen, die alle erstellen Anforderungsheader, rufen Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span><span class="sxs-lookup"><span data-stu-id="eefee-191">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="eefee-192">Diese Einstellung wirkt sich auf die preflight-Anforderungen und die `Access-Control-Request-Headers` Header.</span><span class="sxs-lookup"><span data-stu-id="eefee-192">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="eefee-193">Weitere Informationen finden Sie unter den [Preflight-Anforderungen](#preflight-requests) Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="eefee-193">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="eefee-194">Eine Übereinstimmung der CORS-Middleware-Richtlinien auf bestimmte Header gemäß `WithHeaders` ist nur möglich, wenn die Header gesendet wird, `Access-Control-Request-Headers` genau die Header in angegebenen `WithHeaders`.</span><span class="sxs-lookup"><span data-stu-id="eefee-194">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="eefee-195">Betrachten Sie beispielsweise eine app wie folgt konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="eefee-195">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="eefee-196">CORS-Middleware eine preflight-Anforderung mit der folgenden Anforderungsheader ablehnt, da `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) ist nicht aufgeführt, `WithHeaders`:</span><span class="sxs-lookup"><span data-stu-id="eefee-196">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="eefee-197">Die app-gibt ein *200 OK* Antwort jedoch nicht wieder die CORS-Header sendet.</span><span class="sxs-lookup"><span data-stu-id="eefee-197">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="eefee-198">Aus diesem Grund versucht der Browser nicht die cors-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="eefee-198">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="eefee-199">CORS-Middleware können immer vier Header in der `Access-Control-Request-Headers` unabhängig von den Werten in CorsPolicy.Headers konfiguriert gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="eefee-199">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="eefee-200">Diese Liste der Header enthält:</span><span class="sxs-lookup"><span data-stu-id="eefee-200">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="eefee-201">Betrachten Sie beispielsweise eine app wie folgt konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="eefee-201">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="eefee-202">CORS-Middleware erfolgreich beantwortet eine preflight-Anforderung mit der folgenden Anforderungsheader da `Content-Language` ist immer in der Whitelist enthalten:</span><span class="sxs-lookup"><span data-stu-id="eefee-202">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

::: moniker-end

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="eefee-203">Legen Sie die verfügbar gemachten Antwortheader</span><span class="sxs-lookup"><span data-stu-id="eefee-203">Set the exposed response headers</span></span>

<span data-ttu-id="eefee-204">Standardmäßig nicht im Browser alle Antwortheader für die app verfügbar machen.</span><span class="sxs-lookup"><span data-stu-id="eefee-204">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="eefee-205">Weitere Informationen finden Sie unter [W3C Cross-Origin Resource Sharing (Terminologie): Einfache Antwortheader](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="eefee-205">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="eefee-206">Die Antwortheader, die standardmäßig verfügbar sind:</span><span class="sxs-lookup"><span data-stu-id="eefee-206">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="eefee-207">CORS-Spezifikation ruft diese Header *Header für die einfache Antwort*.</span><span class="sxs-lookup"><span data-stu-id="eefee-207">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="eefee-208">Um weitere Header für die app verfügbar zu machen, rufen Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span><span class="sxs-lookup"><span data-stu-id="eefee-208">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="eefee-209">Anmeldeinformationen im Cross-Origin-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="eefee-209">Credentials in cross-origin requests</span></span>

<span data-ttu-id="eefee-210">Anmeldeinformationen erfordern besondere Behandlung in eine CORS-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="eefee-210">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="eefee-211">Standardmäßig sendet der Browser keine Anmeldeinformationen mit einer cors-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="eefee-211">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="eefee-212">Anmeldeinformationen enthalten, Cookies und HTTP-Authentifizierungsschemas.</span><span class="sxs-lookup"><span data-stu-id="eefee-212">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="eefee-213">Um die Anmeldeinformationen mit einer cors-Anforderung zu senden, muss der Client festgelegt `XMLHttpRequest.withCredentials` zu `true`.</span><span class="sxs-lookup"><span data-stu-id="eefee-213">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="eefee-214">Mithilfe von `XMLHttpRequest` direkt:</span><span class="sxs-lookup"><span data-stu-id="eefee-214">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="eefee-215">Unter Verwendung von jQuery:</span><span class="sxs-lookup"><span data-stu-id="eefee-215">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="eefee-216">Mithilfe der [API abrufen](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="eefee-216">Using the [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="eefee-217">Der Server muss es sich um die Anmeldeinformationen zulassen.</span><span class="sxs-lookup"><span data-stu-id="eefee-217">The server must allow the credentials.</span></span> <span data-ttu-id="eefee-218">Um ursprungsübergreifende Anmeldeinformationen ermöglichen möchten, rufen <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span><span class="sxs-lookup"><span data-stu-id="eefee-218">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="eefee-219">Die HTTP-Antwort enthält ein `Access-Control-Allow-Credentials` -Header, der dem Browser darüber informiert, dass der Server die Anmeldeinformationen für eine Anforderung zwischen verschiedenen Ursprüngen zulässt.</span><span class="sxs-lookup"><span data-stu-id="eefee-219">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="eefee-220">Wenn der Browser sendet die Anmeldeinformationen, aber die Antwort auf eine gültige beinhaltet keine `Access-Control-Allow-Credentials` -Header, der Browser nicht die Antwort auf die app verfügbar zu machen, und die cors-Anforderung schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="eefee-220">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="eefee-221">Genehmigung von Cross-Origin-Anmeldeinformationen ist ein Sicherheitsrisiko dar.</span><span class="sxs-lookup"><span data-stu-id="eefee-221">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="eefee-222">Eine Website in einer anderen Domäne kann die Anmeldeinformationen eines angemeldeten Benutzers an die app auf den Namen des Benutzers, ohne das Wissen des Benutzers senden.</span><span class="sxs-lookup"><span data-stu-id="eefee-222">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="eefee-223">CORS-Spezifikation gibt auch an dieser Einstellung Ursprüngen `"*"` (alle Ursprünge) ist ungültig. wenn die `Access-Control-Allow-Credentials` Header vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="eefee-223">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="eefee-224">Preflight-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="eefee-224">Preflight requests</span></span>

<span data-ttu-id="eefee-225">Für einige CORS-Anforderungen sendet der Browser eine zusätzliche Anforderung vor der tatsächlichen Anforderung an.</span><span class="sxs-lookup"><span data-stu-id="eefee-225">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="eefee-226">Diese Anforderung wird aufgerufen, eine *preflight-Anforderung*.</span><span class="sxs-lookup"><span data-stu-id="eefee-226">This request is called a *preflight request*.</span></span> <span data-ttu-id="eefee-227">Der Browser kann die preflight-Anforderung überspringen, wenn die folgenden Bedingungen erfüllt sind:</span><span class="sxs-lookup"><span data-stu-id="eefee-227">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="eefee-228">Die Anforderungsmethode ist GET, HEAD oder POST.</span><span class="sxs-lookup"><span data-stu-id="eefee-228">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="eefee-229">Die app keine Anforderungsheader außer festlegen `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, oder `Last-Event-ID`.</span><span class="sxs-lookup"><span data-stu-id="eefee-229">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="eefee-230">Die `Content-Type` -Header, wenn festgelegt ist, verfügt über mindestens einen der folgenden Werte:</span><span class="sxs-lookup"><span data-stu-id="eefee-230">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="eefee-231">Die Regel auf die Anforderungsheader festzulegen, für die Header die Anforderung des Clients gilt, die die app durch den Aufruf festlegt `setRequestHeader` auf die `XMLHttpRequest` Objekt.</span><span class="sxs-lookup"><span data-stu-id="eefee-231">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="eefee-232">CORS-Spezifikation ruft diese Header *erstellen Anforderungsheader*.</span><span class="sxs-lookup"><span data-stu-id="eefee-232">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="eefee-233">Die Regel trifft nicht auf der Browser, wie z. B. festlegen kann Header `User-Agent`, `Host`, oder `Content-Length`.</span><span class="sxs-lookup"><span data-stu-id="eefee-233">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="eefee-234">Im folgenden finden ein Beispiel für eine preflight-Anforderung:</span><span class="sxs-lookup"><span data-stu-id="eefee-234">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="eefee-235">Die Pre-Flight-Anforderung verwendet die HTTP OPTIONS-Methode.</span><span class="sxs-lookup"><span data-stu-id="eefee-235">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="eefee-236">Es enthält zwei spezielle Header:</span><span class="sxs-lookup"><span data-stu-id="eefee-236">It includes two special headers:</span></span>

* <span data-ttu-id="eefee-237">`Access-Control-Request-Method`: Die HTTP-Methode, die für die tatsächliche Anforderung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="eefee-237">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="eefee-238">`Access-Control-Request-Headers`: Eine Liste der Anforderungsheader, die die app für die tatsächliche Anforderung festlegt.</span><span class="sxs-lookup"><span data-stu-id="eefee-238">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="eefee-239">Wie bereits erwähnt, Dies beinhaltet keine Header, die der Browser legt, wie z. B. fest `User-Agent`.</span><span class="sxs-lookup"><span data-stu-id="eefee-239">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<span data-ttu-id="eefee-240">Eine CORS-preflight-Anforderung sind zum Beispiel eine `Access-Control-Request-Headers` -Header, der auf dem Server die Header gibt an, die mit der tatsächlichen Anforderung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="eefee-240">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="eefee-241">Um bestimmte Header zu ermöglichen, rufen <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span><span class="sxs-lookup"><span data-stu-id="eefee-241">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="eefee-242">Um zuzulassen, die alle erstellen Anforderungsheader, rufen Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span><span class="sxs-lookup"><span data-stu-id="eefee-242">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="eefee-243">Browser sind nicht vollständig konsistent in diese Festlegung `Access-Control-Request-Headers`.</span><span class="sxs-lookup"><span data-stu-id="eefee-243">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="eefee-244">Wenn Sie Header nicht festgelegt `"*"` (oder verwenden Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), Sie sollten mindestens einschließen `Accept`, `Content-Type`, und `Origin`, sowie alle benutzerdefinierten Header, die Sie unterstützen möchten.</span><span class="sxs-lookup"><span data-stu-id="eefee-244">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="eefee-245">Im folgenden finden eine Beispielantwort auf die preflight-Anforderung (vorausgesetzt, dass der Server die Anforderung zulässt):</span><span class="sxs-lookup"><span data-stu-id="eefee-245">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="eefee-246">Die Antwort enthält ein `Access-Control-Allow-Methods` Header, der die zulässigen Methoden enthält und optional eine `Access-Control-Allow-Headers` -Header, der die erlaubten Header aufgeführt sind.</span><span class="sxs-lookup"><span data-stu-id="eefee-246">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="eefee-247">Wenn die preflight-Anforderung erfolgreich ist, sendet der Browser die tatsächliche Anforderung an.</span><span class="sxs-lookup"><span data-stu-id="eefee-247">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="eefee-248">Wenn die preflight-Anforderung abgelehnt wird, gibt die app eine *200 OK* Antwort jedoch nicht wieder die CORS-Header sendet.</span><span class="sxs-lookup"><span data-stu-id="eefee-248">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="eefee-249">Aus diesem Grund versucht der Browser nicht die cors-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="eefee-249">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="eefee-250">Die Preflight-Ablaufzeit festlegen</span><span class="sxs-lookup"><span data-stu-id="eefee-250">Set the preflight expiration time</span></span>

<span data-ttu-id="eefee-251">Die `Access-Control-Max-Age` Header gibt an, wie lange die Antwort auf die preflight-Anforderung zwischengespeichert werden kann.</span><span class="sxs-lookup"><span data-stu-id="eefee-251">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="eefee-252">Rufen Sie zum Festlegen dieser Header <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span><span class="sxs-lookup"><span data-stu-id="eefee-252">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="eefee-253">Funktionsweise von CORS</span><span class="sxs-lookup"><span data-stu-id="eefee-253">How CORS works</span></span>

<span data-ttu-id="eefee-254">In diesem Abschnitt wird beschrieben, was geschieht, in einem [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) Anforderung auf der Ebene der HTTP-Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="eefee-254">This section describes what happens in a [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="eefee-255">CORS ist **nicht** eine Sicherheitsfunktion.</span><span class="sxs-lookup"><span data-stu-id="eefee-255">CORS is **not** a security feature.</span></span> <span data-ttu-id="eefee-256">CORS ist ein W3C-standard, der einem Server zu lockern die Richtlinie des gleichen Ursprungs ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="eefee-256">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="eefee-257">Beispielsweise können von ein böswilliger Akteur [zu verhindern, dass Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) für Ihre Website, und führen Sie eine standortübergreifende-Anforderung an den Standort der CORS-Aktivierung, Informationen zu stehlen.</span><span class="sxs-lookup"><span data-stu-id="eefee-257">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="eefee-258">Ihre API ist nicht von CORS zulassen sicherer.</span><span class="sxs-lookup"><span data-stu-id="eefee-258">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="eefee-259">Es liegt an den Client (Browser) um CORS zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="eefee-259">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="eefee-260">Der Server führt die Anforderung und gibt die Antwort zurück, sondern der Client, der ein Fehler auftritt und blockiert die Antwort zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="eefee-260">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="eefee-261">Beispielsweise wird eines der folgenden Tools die Serverantwort angezeigt:</span><span class="sxs-lookup"><span data-stu-id="eefee-261">For example, any of the following tools will display the server response:</span></span>
     * [<span data-ttu-id="eefee-262">Fiddler</span><span class="sxs-lookup"><span data-stu-id="eefee-262">Fiddler</span></span>](https://www.telerik.com/fiddler)
     * [<span data-ttu-id="eefee-263">Postman</span><span class="sxs-lookup"><span data-stu-id="eefee-263">Postman</span></span>](https://www.getpostman.com/)
     * [<span data-ttu-id="eefee-264">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="eefee-264">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
     * <span data-ttu-id="eefee-265">Ein Webbrowser, indem Sie die URL in die Adressleiste eingeben.</span><span class="sxs-lookup"><span data-stu-id="eefee-265">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="eefee-266">Dies ist eine Möglichkeit für einen Server zum Zulassen von Browsern zum Ausführen einer Cross-Origin [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) oder [API abrufen](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) -Anforderung, die andernfalls nicht zulässig sein sollen.</span><span class="sxs-lookup"><span data-stu-id="eefee-266">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="eefee-267">Browser (ohne CORS), die Cross-Origin-Anfragen nicht möglich.</span><span class="sxs-lookup"><span data-stu-id="eefee-267">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="eefee-268">Bevor Sie CORS [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) wurde verwendet, um diese Einschränkung zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="eefee-268">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="eefee-269">JSONP XHR nicht verwenden kann, verwendet er die `<script>` Tag zum Empfangen der Antwort.</span><span class="sxs-lookup"><span data-stu-id="eefee-269">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="eefee-270">Skripts sind zulässig, um ursprungsübergreifende geladen werden.</span><span class="sxs-lookup"><span data-stu-id="eefee-270">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="eefee-271">Die [CORS-Spezifikation]() eingeführt wurden mehrere neue HTTP-Header, die Cross-Origin-Anforderungen zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="eefee-271">The [CORS specification]() introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="eefee-272">Wenn ein Browser CORS unterstützt, wird diese Header automatisch für Cross-Origin-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="eefee-272">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="eefee-273">Benutzerdefinierte JavaScript-Code ist nicht erforderlich, um CORS zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="eefee-273">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="eefee-274">Folgendes ist ein Beispiel für eine cors-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="eefee-274">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="eefee-275">Die `Origin` Header enthält, die Domäne der Website, die die Anforderung gesendet wird:</span><span class="sxs-lookup"><span data-stu-id="eefee-275">The `Origin` header provides the domain of the site that's making the request:</span></span>

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

<span data-ttu-id="eefee-276">Wenn der Server die Anforderung zulässt, wird die `Access-Control-Allow-Origin` Header in der Antwort.</span><span class="sxs-lookup"><span data-stu-id="eefee-276">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="eefee-277">Entweder mit dem Wert dieses Headers übereinstimmt der `Origin` Header aus der Anforderung oder der Platzhalterwert `"*"`, Bedeutung, die einen beliebigen Ursprung zulässig ist:</span><span class="sxs-lookup"><span data-stu-id="eefee-277">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="eefee-278">Wenn die Antwort enthalten nicht die `Access-Control-Allow-Origin` -Header, die Cross-Origin-Anforderung schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="eefee-278">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="eefee-279">Der Browser lässt, die die Anforderung.</span><span class="sxs-lookup"><span data-stu-id="eefee-279">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="eefee-280">Selbst wenn der Server eine erfolgreiche Antwort gibt zurück, machen nicht im Browser die Antwort für die Client-app verfügbar.</span><span class="sxs-lookup"><span data-stu-id="eefee-280">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="eefee-281">Testen von CORS</span><span class="sxs-lookup"><span data-stu-id="eefee-281">Test CORS</span></span>

<span data-ttu-id="eefee-282">Um CORS zu testen:</span><span class="sxs-lookup"><span data-stu-id="eefee-282">To test CORS:</span></span>

1. <span data-ttu-id="eefee-283">[Erstellen Sie ein API-Projekt](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="eefee-283">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="eefee-284">Alternativ können Sie [Herunterladen des Beispiels](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="eefee-284">Alternatively, you can [download the sample](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="eefee-285">Aktivieren von CORS mit einer der Methoden in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="eefee-285">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="eefee-286">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="eefee-286">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]
  
  > [!WARNING]
  > <span data-ttu-id="eefee-287">`WithOrigins("https://localhost:<port>");` sollte nur verwendet werden, für das Testen einer Beispiel-app, die ähnlich wie die [Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="eefee-287">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/aspnet/Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="eefee-288">Erstellen Sie eine Web-app-Projekt (Razor-Seiten oder MVC).</span><span class="sxs-lookup"><span data-stu-id="eefee-288">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="eefee-289">Das Beispiel verwendet die Razor-Seiten.</span><span class="sxs-lookup"><span data-stu-id="eefee-289">The sample uses Razor Pages.</span></span> <span data-ttu-id="eefee-290">Sie können die Web-app in der gleichen Projektmappe wie das API-Projekt erstellen.</span><span class="sxs-lookup"><span data-stu-id="eefee-290">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="eefee-291">Fügen Sie den folgenden hervorgehobenen Code in die *"Index.cshtml"* Datei:</span><span class="sxs-lookup"><span data-stu-id="eefee-291">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="eefee-292">Ersetzen Sie im vorangehenden Code `url: 'https://<web app>.azurewebsites.net/api/values/1',` mit der URL für die bereitgestellte app.</span><span class="sxs-lookup"><span data-stu-id="eefee-292">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="eefee-293">Bereitstellen des API-Projekts.</span><span class="sxs-lookup"><span data-stu-id="eefee-293">Deploy the API project.</span></span> <span data-ttu-id="eefee-294">Z. B. [Bereitstellen in Azure](xref:host-and-deploy/azure-apps/index).</span><span class="sxs-lookup"><span data-stu-id="eefee-294">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="eefee-295">Führen Sie die Razor-Seiten oder MVC-app über den Desktop, und klicken Sie auf die **Test** Schaltfläche.</span><span class="sxs-lookup"><span data-stu-id="eefee-295">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="eefee-296">Verwenden Sie die F12-Tools, um Fehlermeldungen zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="eefee-296">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="eefee-297">Entfernen Sie "localhost" Ursprung von `WithOrigins` und Bereitstellen der app.</span><span class="sxs-lookup"><span data-stu-id="eefee-297">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="eefee-298">Führen Sie alternativ die Client-app mit einem anderen Port.</span><span class="sxs-lookup"><span data-stu-id="eefee-298">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="eefee-299">Führen Sie z. B. in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="eefee-299">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="eefee-300">Testen Sie die Client-app.</span><span class="sxs-lookup"><span data-stu-id="eefee-300">Test with the client app.</span></span> <span data-ttu-id="eefee-301">CORS-Fehler ein Fehler zurückgegeben, aber die Fehlermeldung nicht für JavaScript verfügbar.</span><span class="sxs-lookup"><span data-stu-id="eefee-301">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="eefee-302">Verwenden Sie die Registerkarte "Console" in den F12-Tools, um den Fehler anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="eefee-302">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="eefee-303">Je nach Browser erhalten Sie eine Fehlermeldung (in der F12-Toolskonsole) ähnelt dem folgenden:</span><span class="sxs-lookup"><span data-stu-id="eefee-303">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

  * <span data-ttu-id="eefee-304">Verwenden von Microsoft Edge:</span><span class="sxs-lookup"><span data-stu-id="eefee-304">Using Microsoft Edge:</span></span>

    <span data-ttu-id="eefee-305">**SEC7120: [CORS] den Ursprung "https://localhost:44375"wurde nicht gefunden"https://localhost:44375"in der Access-Control-Allow-Origin-Antwortheader für Cross-Origin Resource unter"https://webapi.azurewebsites.net/api/values/1".**</span><span class="sxs-lookup"><span data-stu-id="eefee-305">**SEC7120: [CORS] The origin 'https://localhost:44375' did not find 'https://localhost:44375' in the Access-Control-Allow-Origin response header for cross-origin  resource at 'https://webapi.azurewebsites.net/api/values/1'.**</span></span>

  * <span data-ttu-id="eefee-306">Verwenden von Chrome:</span><span class="sxs-lookup"><span data-stu-id="eefee-306">Using Chrome:</span></span>

    <span data-ttu-id="eefee-307">**Zugriff auf XMLHttpRequest unter "https://webapi.azurewebsites.net/api/values/1"von"Origin"https://localhost:44375"wurde blockiert von CORS-Richtlinie: Kein 'Access-Control-Allow-Origin' Header, die für die angeforderte Ressource vorhanden ist.**</span><span class="sxs-lookup"><span data-stu-id="eefee-307">**Access to XMLHttpRequest at 'https://webapi.azurewebsites.net/api/values/1' from origin 'https://localhost:44375' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>

## <a name="additional-resources"></a><span data-ttu-id="eefee-308">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="eefee-308">Additional resources</span></span>

* [<span data-ttu-id="eefee-309">Cross-Origin Resource Sharing (CORS)</span><span class="sxs-lookup"><span data-stu-id="eefee-309">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)

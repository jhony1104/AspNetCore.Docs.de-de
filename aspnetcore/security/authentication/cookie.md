---
title: Verwenden der Cookie-Authentifizierung ohne ASP.net Core Identität
author: rick-anderson
description: Erfahren Sie, wie Sie die Cookie-Authentifizierung ohne ASP.net Core Identität verwenden.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 08/20/2019
uid: security/authentication/cookie
ms.openlocfilehash: 288fa4317801544bf0d689280c56836431017c89
ms.sourcegitcommit: 9e85c2562df5e108d7933635c830297f484bb775
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73462932"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a><span data-ttu-id="713cc-103">Verwenden der Cookie-Authentifizierung ohne ASP.net Core Identität</span><span class="sxs-lookup"><span data-stu-id="713cc-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="713cc-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="713cc-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="713cc-105">ASP.net Core Identity ist ein vollständiger Authentifizierungs Anbieter mit vollem Funktionsumfang zum Erstellen und warten von Anmeldungen.</span><span class="sxs-lookup"><span data-stu-id="713cc-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="713cc-106">Es kann jedoch ein cookiebasierter Authentifizierungs Anbieter ohne ASP.net Core Identität verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="713cc-106">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="713cc-107">Weitere Informationen finden Sie unter <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="713cc-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="713cc-108">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="713cc-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="713cc-109">Zu Demonstrationszwecken in der Beispiel-APP ist das Benutzerkonto für den hypothetischen Benutzer Maria Rodriguez in der APP hart codiert.</span><span class="sxs-lookup"><span data-stu-id="713cc-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="713cc-110">Verwenden Sie die **e-Mail-** Adresse `maria.rodriguez@contoso.com` und jedes Kennwort, um den Benutzer anzumelden.</span><span class="sxs-lookup"><span data-stu-id="713cc-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="713cc-111">Der Benutzer wird in der `AuthenticateUser`-Methode in der Datei *pages/Account/Login. cshtml. cs* authentifiziert.</span><span class="sxs-lookup"><span data-stu-id="713cc-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="713cc-112">In einem realen Beispiel wird der Benutzer für eine-Datenbank authentifiziert.</span><span class="sxs-lookup"><span data-stu-id="713cc-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="713cc-113">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="713cc-113">Configuration</span></span>

<span data-ttu-id="713cc-114">Wenn die APP das [Metapaket Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app)nicht verwendet, erstellen Sie einen Paket Verweis in der Projektdatei für das Paket [Microsoft. aspnetcore. Authentication. Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .</span><span class="sxs-lookup"><span data-stu-id="713cc-114">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="713cc-115">Erstellen Sie in der `Startup.ConfigureServices`-Methode die Authentifizierungs-Middleware-Dienste mit den Methoden <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> und <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>:</span><span class="sxs-lookup"><span data-stu-id="713cc-115">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="713cc-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> an `AddAuthentication` wird das Standard Authentifizierungsschema für die APP festgelegt.</span><span class="sxs-lookup"><span data-stu-id="713cc-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="713cc-117">`AuthenticationScheme` ist nützlich, wenn mehrere Instanzen der Cookie-Authentifizierung vorhanden sind und Sie die [Autorisierung mit einem bestimmten Schema durch](xref:security/authorization/limitingidentitybyscheme)laufen möchten.</span><span class="sxs-lookup"><span data-stu-id="713cc-117">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="713cc-118">Beim Festlegen der `AuthenticationScheme` auf [cookieauthenticationdefaults. authenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) wird der Wert "Cookies" für das Schema bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="713cc-118">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="713cc-119">Sie können einen beliebigen Zeichen folgen Wert angeben, der das Schema unterscheidet.</span><span class="sxs-lookup"><span data-stu-id="713cc-119">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="713cc-120">Das Authentifizierungsschema der APP unterscheidet sich vom Cookie-Authentifizierungsschema der app.</span><span class="sxs-lookup"><span data-stu-id="713cc-120">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="713cc-121">Wenn kein cookieauthentifizierungsschema für <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>bereitgestellt wird, wird `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies") verwendet.</span><span class="sxs-lookup"><span data-stu-id="713cc-121">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="713cc-122">Die <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential>-Eigenschaft des Authentifizierungs Cookies ist standardmäßig auf `true` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="713cc-122">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="713cc-123">Authentifizierungs Cookies sind zulässig, wenn ein Website Besucher der Datensammlung nicht zugestimmt hat.</span><span class="sxs-lookup"><span data-stu-id="713cc-123">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="713cc-124">Weitere Informationen finden Sie unter <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="713cc-124">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="713cc-125">In `Startup.Configure` werden `UseAuthentication` und `UseAuthorization` aufgerufen, um die `HttpContext.User`-Eigenschaft festzulegen und die Autorisierungs Middleware für Anforderungen auszuführen.</span><span class="sxs-lookup"><span data-stu-id="713cc-125">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="713cc-126">Rufen Sie die Methoden `UseAuthentication` und `UseAuthorization` auf, bevor Sie `UseEndpoints` aufrufen:</span><span class="sxs-lookup"><span data-stu-id="713cc-126">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="713cc-127">Die <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>-Klasse wird verwendet, um die Authentifizierungs Anbieter Optionen zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="713cc-127">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="713cc-128">Legen Sie `CookieAuthenticationOptions` in der Dienst Konfiguration für die Authentifizierung in der `Startup.ConfigureServices`-Methode fest:</span><span class="sxs-lookup"><span data-stu-id="713cc-128">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="713cc-129">Cookie-Richtlinien Middleware</span><span class="sxs-lookup"><span data-stu-id="713cc-129">Cookie Policy Middleware</span></span>

<span data-ttu-id="713cc-130">[Cookingrichtlinienmiddleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) ermöglicht Cookingrichtlinienfunktionen.</span><span class="sxs-lookup"><span data-stu-id="713cc-130">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="713cc-131">Das Hinzufügen der Middleware zur APP-Verarbeitungs Pipeline ist die Reihenfolge,&mdash;Sie nur auf Downstreamkomponenten wirkt, die in der Pipeline registriert sind.</span><span class="sxs-lookup"><span data-stu-id="713cc-131">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="713cc-132">Verwenden Sie <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions>, die der cookierichtlinienmiddleware bereitgestellt werden, um globale Merkmale der Cookieverarbeitung zu steuern und in cookieverarbeitungs Handler zu stecken, wenn Cookies angehängt oder gelöscht</span><span class="sxs-lookup"><span data-stu-id="713cc-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="713cc-133">Der Standard <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> Wert ist `SameSiteMode.Lax`, um die OAuth2-Authentifizierung zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="713cc-133">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="713cc-134">Legen Sie die `MinimumSameSitePolicy`fest, um eine Richtlinie für denselben Standort `SameSiteMode.Strict`streng zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="713cc-134">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="713cc-135">Obwohl diese Einstellung OAuth2 und andere Ursprungs übergreifende Authentifizierungs Schemas unterbricht, erhöht Sie die Sicherheitsstufe für andere Typen von apps, die sich nicht auf die Ursprungs übergreifende Anforderungs Verarbeitung stützen.</span><span class="sxs-lookup"><span data-stu-id="713cc-135">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="713cc-136">Die Middleware-Einstellung der Cookie-Richtlinie für `MinimumSameSitePolicy` kann die Einstellung `Cookie.SameSite` in `CookieAuthenticationOptions` Einstellungen entsprechend der folgenden Matrix beeinflussen.</span><span class="sxs-lookup"><span data-stu-id="713cc-136">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="713cc-137">Minimumsamesitepolicy</span><span class="sxs-lookup"><span data-stu-id="713cc-137">MinimumSameSitePolicy</span></span> | <span data-ttu-id="713cc-138">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="713cc-138">Cookie.SameSite</span></span> | <span data-ttu-id="713cc-139">Resultierende Cookie. SameSite-Einstellung</span><span class="sxs-lookup"><span data-stu-id="713cc-139">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="713cc-140">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="713cc-140">SameSiteMode.None</span></span>     | <span data-ttu-id="713cc-141">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="713cc-141">SameSiteMode.None</span></span><br><span data-ttu-id="713cc-142">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="713cc-142">SameSiteMode.Lax</span></span><br><span data-ttu-id="713cc-143">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="713cc-143">SameSiteMode.Strict</span></span> | <span data-ttu-id="713cc-144">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="713cc-144">SameSiteMode.None</span></span><br><span data-ttu-id="713cc-145">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="713cc-145">SameSiteMode.Lax</span></span><br><span data-ttu-id="713cc-146">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="713cc-146">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="713cc-147">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="713cc-147">SameSiteMode.Lax</span></span>      | <span data-ttu-id="713cc-148">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="713cc-148">SameSiteMode.None</span></span><br><span data-ttu-id="713cc-149">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="713cc-149">SameSiteMode.Lax</span></span><br><span data-ttu-id="713cc-150">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="713cc-150">SameSiteMode.Strict</span></span> | <span data-ttu-id="713cc-151">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="713cc-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="713cc-152">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="713cc-152">SameSiteMode.Lax</span></span><br><span data-ttu-id="713cc-153">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="713cc-153">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="713cc-154">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="713cc-154">SameSiteMode.Strict</span></span>   | <span data-ttu-id="713cc-155">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="713cc-155">SameSiteMode.None</span></span><br><span data-ttu-id="713cc-156">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="713cc-156">SameSiteMode.Lax</span></span><br><span data-ttu-id="713cc-157">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="713cc-157">SameSiteMode.Strict</span></span> | <span data-ttu-id="713cc-158">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="713cc-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="713cc-159">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="713cc-159">SameSiteMode.Strict</span></span><br><span data-ttu-id="713cc-160">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="713cc-160">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="713cc-161">Erstellen eines Authentifizierungs Cookies</span><span class="sxs-lookup"><span data-stu-id="713cc-161">Create an authentication cookie</span></span>

<span data-ttu-id="713cc-162">Erstellen Sie eine <xref:System.Security.Claims.ClaimsPrincipal>, um ein Cookie mit Benutzerinformationen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="713cc-162">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="713cc-163">Die Benutzerinformationen werden serialisiert und im Cookie gespeichert.</span><span class="sxs-lookup"><span data-stu-id="713cc-163">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="713cc-164">Erstellen Sie eine <xref:System.Security.Claims.ClaimsIdentity> mit allen erforderlichen <xref:System.Security.Claims.Claim>s, und rufen Sie <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> auf, um den Benutzer anzumelden:</span><span class="sxs-lookup"><span data-stu-id="713cc-164">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="713cc-165">`SignInAsync` erstellt ein verschlüsseltes Cookie und fügt es der aktuellen Antwort hinzu.</span><span class="sxs-lookup"><span data-stu-id="713cc-165">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="713cc-166">Wenn `AuthenticationScheme` nicht angegeben ist, wird das Standardschema verwendet.</span><span class="sxs-lookup"><span data-stu-id="713cc-166">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="713cc-167">ASP.net Core- [Datenschutz](xref:security/data-protection/using-data-protection) System wird für die Verschlüsselung verwendet.</span><span class="sxs-lookup"><span data-stu-id="713cc-167">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="713cc-168">Konfigurieren Sie für eine APP, die auf mehreren Computern gehostet wird, den Lastenausgleich über apps oder eine Webfarm, den [Datenschutz](xref:security/data-protection/configuration/overview) so, dass derselbe Schlüsselbund und der APP-Bezeichner verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="713cc-168">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="713cc-169">Abmelden</span><span class="sxs-lookup"><span data-stu-id="713cc-169">Sign out</span></span>

<span data-ttu-id="713cc-170">Um den aktuellen Benutzer zu abmelden und das Cookie zu löschen, wenden Sie <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>an:</span><span class="sxs-lookup"><span data-stu-id="713cc-170">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="713cc-171">Wenn `CookieAuthenticationDefaults.AuthenticationScheme` (oder "Cookies") nicht als Schema (z. b. "contosocookie") verwendet wird, geben Sie das Schema an, das beim Konfigurieren des Authentifizierungs Anbieters verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="713cc-171">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="713cc-172">Andernfalls wird das Standardschema verwendet.</span><span class="sxs-lookup"><span data-stu-id="713cc-172">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="713cc-173">Reagieren auf Back-End-Änderungen</span><span class="sxs-lookup"><span data-stu-id="713cc-173">React to back-end changes</span></span>

<span data-ttu-id="713cc-174">Nachdem ein Cookie erstellt wurde, ist das Cookie die einzige Quelle der Identität.</span><span class="sxs-lookup"><span data-stu-id="713cc-174">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="713cc-175">Wenn ein Benutzerkonto in Back-End-Systemen deaktiviert ist:</span><span class="sxs-lookup"><span data-stu-id="713cc-175">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="713cc-176">Das cookieauthentifizierungssystem der APP verarbeitet weiterhin Anforderungen auf der Grundlage des Authentifizierungs Cookies.</span><span class="sxs-lookup"><span data-stu-id="713cc-176">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="713cc-177">Der Benutzer bleibt bei der App angemeldet, solange das Authentifizierungs Cookie gültig ist.</span><span class="sxs-lookup"><span data-stu-id="713cc-177">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="713cc-178">Das <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> Ereignis kann verwendet werden, um die Überprüfung der Cookie-Identität abzufangen und zu überschreiben.</span><span class="sxs-lookup"><span data-stu-id="713cc-178">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="713cc-179">Das Überprüfen des Cookies bei jeder Anforderung verringert das Risiko, dass widerrufene Benutzer auf die App zugreifen.</span><span class="sxs-lookup"><span data-stu-id="713cc-179">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="713cc-180">Ein Ansatz für die Cookievalidierung basiert darauf, wann die Benutzerdatenbank geändert wird.</span><span class="sxs-lookup"><span data-stu-id="713cc-180">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="713cc-181">Wenn die Datenbank nicht geändert wurde, weil das Cookie des Benutzers ausgestellt wurde, ist es nicht erforderlich, den Benutzer erneut zu authentifizieren, wenn das Cookie noch gültig ist.</span><span class="sxs-lookup"><span data-stu-id="713cc-181">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="713cc-182">In der Beispiel-APP wird die Datenbank in `IUserRepository` implementiert und speichert einen `LastChanged` Wert.</span><span class="sxs-lookup"><span data-stu-id="713cc-182">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="713cc-183">Wenn ein Benutzer in der Datenbank aktualisiert wird, wird der `LastChanged` Wert auf die aktuelle Zeit festgelegt.</span><span class="sxs-lookup"><span data-stu-id="713cc-183">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="713cc-184">Erstellen Sie das Cookie mit einem `LastChanged`-Anspruch, der den aktuellen `LastChanged` Wert aus der Datenbank enthält, um ein Cookie für ungültig zu erklären, wenn sich die Datenbank auf der Grundlage des `LastChanged` Werts ändert:</span><span class="sxs-lookup"><span data-stu-id="713cc-184">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

<span data-ttu-id="713cc-185">Um eine außer Kraft setzung für das `ValidatePrincipal`-Ereignis zu implementieren, schreiben Sie eine-Methode mit der folgenden Signatur in eine Klasse, die von <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>abgeleitet wird:</span><span class="sxs-lookup"><span data-stu-id="713cc-185">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="713cc-186">Im folgenden finden Sie eine Beispiel Implementierung von `CookieAuthenticationEvents`:</span><span class="sxs-lookup"><span data-stu-id="713cc-186">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="713cc-187">Registrieren Sie die Ereignis Instanz während der Registrierung des Cookie-Dienstanbieter in der `Startup.ConfigureServices` Methode.</span><span class="sxs-lookup"><span data-stu-id="713cc-187">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="713cc-188">Stellen Sie eine Bereichs bezogene [Dienst Registrierung](xref:fundamentals/dependency-injection#service-lifetimes) für Ihre `CustomCookieAuthenticationEvents`-Klasse bereit:</span><span class="sxs-lookup"><span data-stu-id="713cc-188">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="713cc-189">Stellen Sie sich eine Situation vor, in der der Name des Benutzers&mdash;eine Entscheidung aktualisiert wird, die die Sicherheit in keiner Weise beeinflusst.</span><span class="sxs-lookup"><span data-stu-id="713cc-189">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="713cc-190">Wenn Sie den Benutzer Prinzipal nicht dedestruktiv aktualisieren möchten, müssen Sie `context.ReplacePrincipal` aufrufen und die `context.ShouldRenew`-Eigenschaft auf `true`festlegen.</span><span class="sxs-lookup"><span data-stu-id="713cc-190">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="713cc-191">Der hier beschriebene Ansatz wird bei jeder Anforderung ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="713cc-191">The approach described here is triggered on every request.</span></span> <span data-ttu-id="713cc-192">Das Überprüfen von Authentifizierungs Cookies für alle Benutzer bei jeder Anforderung kann zu einer hohen Leistungs Einbuße für die APP führen.</span><span class="sxs-lookup"><span data-stu-id="713cc-192">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="713cc-193">Persistente Cookies</span><span class="sxs-lookup"><span data-stu-id="713cc-193">Persistent cookies</span></span>

<span data-ttu-id="713cc-194">Möglicherweise möchten Sie, dass das Cookie über Browsersitzungen hinweg beibehalten wird.</span><span class="sxs-lookup"><span data-stu-id="713cc-194">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="713cc-195">Diese Persistenz sollte nur mit der expliziten Zustimmung des Benutzers mit dem Kontrollkästchen "merken" bei der Anmeldung oder einem ähnlichen Mechanismus aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="713cc-195">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="713cc-196">Der folgende Code Ausschnitt erstellt eine Identität und entsprechendes Cookie, das bei Browser Abschlüssen überlebt.</span><span class="sxs-lookup"><span data-stu-id="713cc-196">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="713cc-197">Alle zuvor konfigurierten gleitenden Ablauf Einstellungen werden berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="713cc-197">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="713cc-198">Wenn das Cookie abläuft, während der Browser geschlossen wird, löscht der Browser das Cookie, sobald es neu gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="713cc-198">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="713cc-199">Legen Sie <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> auf `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>fest:</span><span class="sxs-lookup"><span data-stu-id="713cc-199">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="713cc-200">Absoluter Cookieablauf</span><span class="sxs-lookup"><span data-stu-id="713cc-200">Absolute cookie expiration</span></span>

<span data-ttu-id="713cc-201">Eine absolute Ablaufzeit kann mit <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="713cc-201">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="713cc-202">Um ein dauerhaftes Cookie zu erstellen, müssen `IsPersistent` ebenfalls festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="713cc-202">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="713cc-203">Andernfalls wird das Cookie mit einer Sitzungs basierten Lebensdauer erstellt und kann entweder vor oder nach dem Authentifizierungs Ticket ablaufen, das es enthält.</span><span class="sxs-lookup"><span data-stu-id="713cc-203">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="713cc-204">Wenn `ExpiresUtc` festgelegt ist, wird der Wert der <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan>-Option von <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>überschrieben, sofern festgelegt.</span><span class="sxs-lookup"><span data-stu-id="713cc-204">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="713cc-205">Der folgende Code Ausschnitt erstellt eine Identität und entsprechendes Cookie, das 20 Minuten lang dauert.</span><span class="sxs-lookup"><span data-stu-id="713cc-205">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="713cc-206">Dadurch werden alle zuvor konfigurierten gleitenden Ablauf Einstellungen ignoriert.</span><span class="sxs-lookup"><span data-stu-id="713cc-206">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="713cc-207">ASP.net Core Identity ist ein vollständiger Authentifizierungs Anbieter mit vollem Funktionsumfang zum Erstellen und warten von Anmeldungen.</span><span class="sxs-lookup"><span data-stu-id="713cc-207">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="713cc-208">Es kann jedoch ein cookiebasierter Authentifizierungs Anbieter ohne ASP.net Core Identität verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="713cc-208">However, a cookie-based authentication authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="713cc-209">Weitere Informationen finden Sie unter <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="713cc-209">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="713cc-210">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="713cc-210">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="713cc-211">Zu Demonstrationszwecken in der Beispiel-APP ist das Benutzerkonto für den hypothetischen Benutzer Maria Rodriguez in der APP hart codiert.</span><span class="sxs-lookup"><span data-stu-id="713cc-211">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="713cc-212">Verwenden Sie die **e-Mail-** Adresse `maria.rodriguez@contoso.com` und jedes Kennwort, um den Benutzer anzumelden.</span><span class="sxs-lookup"><span data-stu-id="713cc-212">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="713cc-213">Der Benutzer wird in der `AuthenticateUser`-Methode in der Datei *pages/Account/Login. cshtml. cs* authentifiziert.</span><span class="sxs-lookup"><span data-stu-id="713cc-213">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="713cc-214">In einem realen Beispiel wird der Benutzer für eine-Datenbank authentifiziert.</span><span class="sxs-lookup"><span data-stu-id="713cc-214">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="713cc-215">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="713cc-215">Configuration</span></span>

<span data-ttu-id="713cc-216">Wenn die APP das [Metapaket Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app)nicht verwendet, erstellen Sie einen Paket Verweis in der Projektdatei für das Paket [Microsoft. aspnetcore. Authentication. Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .</span><span class="sxs-lookup"><span data-stu-id="713cc-216">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="713cc-217">Erstellen Sie in der `Startup.ConfigureServices`-Methode den Authentifizierungs-Middleware-Dienst mit den Methoden <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> und <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>:</span><span class="sxs-lookup"><span data-stu-id="713cc-217">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="713cc-218"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> an `AddAuthentication` wird das Standard Authentifizierungsschema für die APP festgelegt.</span><span class="sxs-lookup"><span data-stu-id="713cc-218"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="713cc-219">`AuthenticationScheme` ist nützlich, wenn mehrere Instanzen der Cookie-Authentifizierung vorhanden sind und Sie die [Autorisierung mit einem bestimmten Schema durch](xref:security/authorization/limitingidentitybyscheme)laufen möchten.</span><span class="sxs-lookup"><span data-stu-id="713cc-219">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="713cc-220">Beim Festlegen der `AuthenticationScheme` auf [cookieauthenticationdefaults. authenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) wird der Wert "Cookies" für das Schema bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="713cc-220">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="713cc-221">Sie können einen beliebigen Zeichen folgen Wert angeben, der das Schema unterscheidet.</span><span class="sxs-lookup"><span data-stu-id="713cc-221">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="713cc-222">Das Authentifizierungsschema der APP unterscheidet sich vom Cookie-Authentifizierungsschema der app.</span><span class="sxs-lookup"><span data-stu-id="713cc-222">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="713cc-223">Wenn kein cookieauthentifizierungsschema für <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>bereitgestellt wird, wird `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies") verwendet.</span><span class="sxs-lookup"><span data-stu-id="713cc-223">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="713cc-224">Die <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential>-Eigenschaft des Authentifizierungs Cookies ist standardmäßig auf `true` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="713cc-224">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="713cc-225">Authentifizierungs Cookies sind zulässig, wenn ein Website Besucher der Datensammlung nicht zugestimmt hat.</span><span class="sxs-lookup"><span data-stu-id="713cc-225">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="713cc-226">Weitere Informationen finden Sie unter <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="713cc-226">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="713cc-227">Rufen Sie in der `Startup.Configure`-Methode die `UseAuthentication`-Methode auf, um die Authentifizierungs Middleware aufzurufen, die die `HttpContext.User`-Eigenschaft festlegt.</span><span class="sxs-lookup"><span data-stu-id="713cc-227">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="713cc-228">Rufen Sie die `UseAuthentication`-Methode auf, bevor Sie `UseMvcWithDefaultRoute` oder `UseMvc` aufrufen:</span><span class="sxs-lookup"><span data-stu-id="713cc-228">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="713cc-229">Die <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>-Klasse wird verwendet, um die Authentifizierungs Anbieter Optionen zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="713cc-229">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="713cc-230">Legen Sie `CookieAuthenticationOptions` in der Dienst Konfiguration für die Authentifizierung in der `Startup.ConfigureServices`-Methode fest:</span><span class="sxs-lookup"><span data-stu-id="713cc-230">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="713cc-231">Cookie-Richtlinien Middleware</span><span class="sxs-lookup"><span data-stu-id="713cc-231">Cookie Policy Middleware</span></span>

<span data-ttu-id="713cc-232">[Cookingrichtlinienmiddleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) ermöglicht Cookingrichtlinienfunktionen.</span><span class="sxs-lookup"><span data-stu-id="713cc-232">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="713cc-233">Das Hinzufügen der Middleware zur APP-Verarbeitungs Pipeline ist die Reihenfolge,&mdash;Sie nur auf Downstreamkomponenten wirkt, die in der Pipeline registriert sind.</span><span class="sxs-lookup"><span data-stu-id="713cc-233">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="713cc-234">Verwenden Sie <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions>, die der cookierichtlinienmiddleware bereitgestellt werden, um globale Merkmale der Cookieverarbeitung zu steuern und in cookieverarbeitungs Handler zu stecken, wenn Cookies angehängt oder gelöscht</span><span class="sxs-lookup"><span data-stu-id="713cc-234">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="713cc-235">Der Standard <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> Wert ist `SameSiteMode.Lax`, um die OAuth2-Authentifizierung zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="713cc-235">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="713cc-236">Legen Sie die `MinimumSameSitePolicy`fest, um eine Richtlinie für denselben Standort `SameSiteMode.Strict`streng zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="713cc-236">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="713cc-237">Obwohl diese Einstellung OAuth2 und andere Ursprungs übergreifende Authentifizierungs Schemas unterbricht, erhöht Sie die Sicherheitsstufe für andere Typen von apps, die sich nicht auf die Ursprungs übergreifende Anforderungs Verarbeitung stützen.</span><span class="sxs-lookup"><span data-stu-id="713cc-237">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="713cc-238">Die Middleware-Einstellung der Cookie-Richtlinie für `MinimumSameSitePolicy` kann die Einstellung `Cookie.SameSite` in `CookieAuthenticationOptions` Einstellungen entsprechend der folgenden Matrix beeinflussen.</span><span class="sxs-lookup"><span data-stu-id="713cc-238">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="713cc-239">Minimumsamesitepolicy</span><span class="sxs-lookup"><span data-stu-id="713cc-239">MinimumSameSitePolicy</span></span> | <span data-ttu-id="713cc-240">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="713cc-240">Cookie.SameSite</span></span> | <span data-ttu-id="713cc-241">Resultierende Cookie. SameSite-Einstellung</span><span class="sxs-lookup"><span data-stu-id="713cc-241">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="713cc-242">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="713cc-242">SameSiteMode.None</span></span>     | <span data-ttu-id="713cc-243">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="713cc-243">SameSiteMode.None</span></span><br><span data-ttu-id="713cc-244">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="713cc-244">SameSiteMode.Lax</span></span><br><span data-ttu-id="713cc-245">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="713cc-245">SameSiteMode.Strict</span></span> | <span data-ttu-id="713cc-246">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="713cc-246">SameSiteMode.None</span></span><br><span data-ttu-id="713cc-247">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="713cc-247">SameSiteMode.Lax</span></span><br><span data-ttu-id="713cc-248">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="713cc-248">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="713cc-249">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="713cc-249">SameSiteMode.Lax</span></span>      | <span data-ttu-id="713cc-250">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="713cc-250">SameSiteMode.None</span></span><br><span data-ttu-id="713cc-251">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="713cc-251">SameSiteMode.Lax</span></span><br><span data-ttu-id="713cc-252">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="713cc-252">SameSiteMode.Strict</span></span> | <span data-ttu-id="713cc-253">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="713cc-253">SameSiteMode.Lax</span></span><br><span data-ttu-id="713cc-254">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="713cc-254">SameSiteMode.Lax</span></span><br><span data-ttu-id="713cc-255">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="713cc-255">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="713cc-256">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="713cc-256">SameSiteMode.Strict</span></span>   | <span data-ttu-id="713cc-257">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="713cc-257">SameSiteMode.None</span></span><br><span data-ttu-id="713cc-258">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="713cc-258">SameSiteMode.Lax</span></span><br><span data-ttu-id="713cc-259">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="713cc-259">SameSiteMode.Strict</span></span> | <span data-ttu-id="713cc-260">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="713cc-260">SameSiteMode.Strict</span></span><br><span data-ttu-id="713cc-261">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="713cc-261">SameSiteMode.Strict</span></span><br><span data-ttu-id="713cc-262">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="713cc-262">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="713cc-263">Erstellen eines Authentifizierungs Cookies</span><span class="sxs-lookup"><span data-stu-id="713cc-263">Create an authentication cookie</span></span>

<span data-ttu-id="713cc-264">Erstellen Sie eine <xref:System.Security.Claims.ClaimsPrincipal>, um ein Cookie mit Benutzerinformationen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="713cc-264">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="713cc-265">Die Benutzerinformationen werden serialisiert und im Cookie gespeichert.</span><span class="sxs-lookup"><span data-stu-id="713cc-265">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="713cc-266">Erstellen Sie eine <xref:System.Security.Claims.ClaimsIdentity> mit allen erforderlichen <xref:System.Security.Claims.Claim>s, und rufen Sie <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> auf, um den Benutzer anzumelden:</span><span class="sxs-lookup"><span data-stu-id="713cc-266">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="713cc-267">`SignInAsync` erstellt ein verschlüsseltes Cookie und fügt es der aktuellen Antwort hinzu.</span><span class="sxs-lookup"><span data-stu-id="713cc-267">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="713cc-268">Wenn `AuthenticationScheme` nicht angegeben ist, wird das Standardschema verwendet.</span><span class="sxs-lookup"><span data-stu-id="713cc-268">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="713cc-269">ASP.net Core- [Datenschutz](xref:security/data-protection/using-data-protection) System wird für die Verschlüsselung verwendet.</span><span class="sxs-lookup"><span data-stu-id="713cc-269">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="713cc-270">Konfigurieren Sie für eine APP, die auf mehreren Computern gehostet wird, den Lastenausgleich über apps oder eine Webfarm, den [Datenschutz](xref:security/data-protection/configuration/overview) so, dass derselbe Schlüsselbund und der APP-Bezeichner verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="713cc-270">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="713cc-271">Abmelden</span><span class="sxs-lookup"><span data-stu-id="713cc-271">Sign out</span></span>

<span data-ttu-id="713cc-272">Um den aktuellen Benutzer zu abmelden und das Cookie zu löschen, wenden Sie <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>an:</span><span class="sxs-lookup"><span data-stu-id="713cc-272">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="713cc-273">Wenn `CookieAuthenticationDefaults.AuthenticationScheme` (oder "Cookies") nicht als Schema (z. b. "contosocookie") verwendet wird, geben Sie das Schema an, das beim Konfigurieren des Authentifizierungs Anbieters verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="713cc-273">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="713cc-274">Andernfalls wird das Standardschema verwendet.</span><span class="sxs-lookup"><span data-stu-id="713cc-274">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="713cc-275">Reagieren auf Back-End-Änderungen</span><span class="sxs-lookup"><span data-stu-id="713cc-275">React to back-end changes</span></span>

<span data-ttu-id="713cc-276">Nachdem ein Cookie erstellt wurde, ist das Cookie die einzige Quelle der Identität.</span><span class="sxs-lookup"><span data-stu-id="713cc-276">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="713cc-277">Wenn ein Benutzerkonto in Back-End-Systemen deaktiviert ist:</span><span class="sxs-lookup"><span data-stu-id="713cc-277">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="713cc-278">Das cookieauthentifizierungssystem der APP verarbeitet weiterhin Anforderungen auf der Grundlage des Authentifizierungs Cookies.</span><span class="sxs-lookup"><span data-stu-id="713cc-278">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="713cc-279">Der Benutzer bleibt bei der App angemeldet, solange das Authentifizierungs Cookie gültig ist.</span><span class="sxs-lookup"><span data-stu-id="713cc-279">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="713cc-280">Das <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> Ereignis kann verwendet werden, um die Überprüfung der Cookie-Identität abzufangen und zu überschreiben.</span><span class="sxs-lookup"><span data-stu-id="713cc-280">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="713cc-281">Das Überprüfen des Cookies bei jeder Anforderung verringert das Risiko, dass widerrufene Benutzer auf die App zugreifen.</span><span class="sxs-lookup"><span data-stu-id="713cc-281">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="713cc-282">Ein Ansatz für die Cookievalidierung basiert darauf, wann die Benutzerdatenbank geändert wird.</span><span class="sxs-lookup"><span data-stu-id="713cc-282">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="713cc-283">Wenn die Datenbank nicht geändert wurde, weil das Cookie des Benutzers ausgestellt wurde, ist es nicht erforderlich, den Benutzer erneut zu authentifizieren, wenn das Cookie noch gültig ist.</span><span class="sxs-lookup"><span data-stu-id="713cc-283">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="713cc-284">In der Beispiel-APP wird die Datenbank in `IUserRepository` implementiert und speichert einen `LastChanged` Wert.</span><span class="sxs-lookup"><span data-stu-id="713cc-284">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="713cc-285">Wenn ein Benutzer in der Datenbank aktualisiert wird, wird der `LastChanged` Wert auf die aktuelle Zeit festgelegt.</span><span class="sxs-lookup"><span data-stu-id="713cc-285">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="713cc-286">Erstellen Sie das Cookie mit einem `LastChanged`-Anspruch, der den aktuellen `LastChanged` Wert aus der Datenbank enthält, um ein Cookie für ungültig zu erklären, wenn sich die Datenbank auf der Grundlage des `LastChanged` Werts ändert:</span><span class="sxs-lookup"><span data-stu-id="713cc-286">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

<span data-ttu-id="713cc-287">Um eine außer Kraft setzung für das `ValidatePrincipal`-Ereignis zu implementieren, schreiben Sie eine-Methode mit der folgenden Signatur in eine Klasse, die von <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>abgeleitet wird:</span><span class="sxs-lookup"><span data-stu-id="713cc-287">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="713cc-288">Im folgenden finden Sie eine Beispiel Implementierung von `CookieAuthenticationEvents`:</span><span class="sxs-lookup"><span data-stu-id="713cc-288">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="713cc-289">Registrieren Sie die Ereignis Instanz während der Registrierung des Cookie-Dienstanbieter in der `Startup.ConfigureServices` Methode.</span><span class="sxs-lookup"><span data-stu-id="713cc-289">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="713cc-290">Stellen Sie eine Bereichs bezogene [Dienst Registrierung](xref:fundamentals/dependency-injection#service-lifetimes) für Ihre `CustomCookieAuthenticationEvents`-Klasse bereit:</span><span class="sxs-lookup"><span data-stu-id="713cc-290">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="713cc-291">Stellen Sie sich eine Situation vor, in der der Name des Benutzers&mdash;eine Entscheidung aktualisiert wird, die die Sicherheit in keiner Weise beeinflusst.</span><span class="sxs-lookup"><span data-stu-id="713cc-291">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="713cc-292">Wenn Sie den Benutzer Prinzipal nicht dedestruktiv aktualisieren möchten, müssen Sie `context.ReplacePrincipal` aufrufen und die `context.ShouldRenew`-Eigenschaft auf `true`festlegen.</span><span class="sxs-lookup"><span data-stu-id="713cc-292">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="713cc-293">Der hier beschriebene Ansatz wird bei jeder Anforderung ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="713cc-293">The approach described here is triggered on every request.</span></span> <span data-ttu-id="713cc-294">Das Überprüfen von Authentifizierungs Cookies für alle Benutzer bei jeder Anforderung kann zu einer hohen Leistungs Einbuße für die APP führen.</span><span class="sxs-lookup"><span data-stu-id="713cc-294">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="713cc-295">Persistente Cookies</span><span class="sxs-lookup"><span data-stu-id="713cc-295">Persistent cookies</span></span>

<span data-ttu-id="713cc-296">Möglicherweise möchten Sie, dass das Cookie über Browsersitzungen hinweg beibehalten wird.</span><span class="sxs-lookup"><span data-stu-id="713cc-296">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="713cc-297">Diese Persistenz sollte nur mit der expliziten Zustimmung des Benutzers mit dem Kontrollkästchen "merken" bei der Anmeldung oder einem ähnlichen Mechanismus aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="713cc-297">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="713cc-298">Der folgende Code Ausschnitt erstellt eine Identität und entsprechendes Cookie, das bei Browser Abschlüssen überlebt.</span><span class="sxs-lookup"><span data-stu-id="713cc-298">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="713cc-299">Alle zuvor konfigurierten gleitenden Ablauf Einstellungen werden berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="713cc-299">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="713cc-300">Wenn das Cookie abläuft, während der Browser geschlossen wird, löscht der Browser das Cookie, sobald es neu gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="713cc-300">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="713cc-301">Legen Sie <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> auf `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>fest:</span><span class="sxs-lookup"><span data-stu-id="713cc-301">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="713cc-302">Absoluter Cookieablauf</span><span class="sxs-lookup"><span data-stu-id="713cc-302">Absolute cookie expiration</span></span>

<span data-ttu-id="713cc-303">Eine absolute Ablaufzeit kann mit <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="713cc-303">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="713cc-304">Um ein dauerhaftes Cookie zu erstellen, müssen `IsPersistent` ebenfalls festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="713cc-304">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="713cc-305">Andernfalls wird das Cookie mit einer Sitzungs basierten Lebensdauer erstellt und kann entweder vor oder nach dem Authentifizierungs Ticket ablaufen, das es enthält.</span><span class="sxs-lookup"><span data-stu-id="713cc-305">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="713cc-306">Wenn `ExpiresUtc` festgelegt ist, wird der Wert der <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan>-Option von <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>überschrieben, sofern festgelegt.</span><span class="sxs-lookup"><span data-stu-id="713cc-306">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="713cc-307">Der folgende Code Ausschnitt erstellt eine Identität und entsprechendes Cookie, das 20 Minuten lang dauert.</span><span class="sxs-lookup"><span data-stu-id="713cc-307">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="713cc-308">Dadurch werden alle zuvor konfigurierten gleitenden Ablauf Einstellungen ignoriert.</span><span class="sxs-lookup"><span data-stu-id="713cc-308">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="713cc-309">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="713cc-309">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="713cc-310">Richtlinien basierte Rollen Überprüfungen</span><span class="sxs-lookup"><span data-stu-id="713cc-310">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>

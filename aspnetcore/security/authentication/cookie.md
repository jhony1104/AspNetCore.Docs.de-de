---
title: Verwenden der Cookie-Authentifizierung ohne ASP.net CoreIdentity
author: rick-anderson
description: Erfahren Sie, wie Sie die Cookie-Authentifizierung ohne ASP.net Core verwenden Identity .
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/cookie
ms.openlocfilehash: 7d2f338f8ece6bd3cc99d5f2ab8153b5c465c7a4
ms.sourcegitcommit: d243fadeda20ad4f142ea60301ae5f5e0d41ed60
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/12/2020
ms.locfileid: "84724236"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a><span data-ttu-id="e1ef9-103">Verwenden der Cookie-Authentifizierung ohne ASP.net CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="e1ef9-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="e1ef9-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e1ef9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e1ef9-105">ASP.net Core Identity ist ein vollständiger Authentifizierungs Anbieter mit vollem Funktionsumfang zum Erstellen und warten von Anmeldungen.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="e1ef9-106">Es kann jedoch ein cookiebasierter Authentifizierungs Anbieter ohne ASP.net Core Identity verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-106">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="e1ef9-107">Weitere Informationen finden Sie unter <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="e1ef9-108">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e1ef9-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e1ef9-109">Zu Demonstrationszwecken in der Beispiel-APP ist das Benutzerkonto für den hypothetischen Benutzer Maria Rodriguez in der APP hart codiert.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="e1ef9-110">Verwenden Sie die **e-Mail-** Adresse `maria.rodriguez@contoso.com` und jedes Kennwort, um den Benutzer anzumelden.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="e1ef9-111">Der Benutzer wird in der `AuthenticateUser` -Methode in der Datei *pages/Account/Login. cshtml. cs* authentifiziert.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="e1ef9-112">In einem realen Beispiel wird der Benutzer für eine-Datenbank authentifiziert.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="e1ef9-113">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="e1ef9-113">Configuration</span></span>

<span data-ttu-id="e1ef9-114">Wenn die APP das [Metapaket Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app)nicht verwendet, erstellen Sie einen Paket Verweis in der Projektdatei für das Paket [Microsoft. aspnetcore. Authentication. Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .</span><span class="sxs-lookup"><span data-stu-id="e1ef9-114">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="e1ef9-115">Erstellen Sie in der `Startup.ConfigureServices` -Methode die Authentifizierungs-Middleware-Dienste mit der <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> -Methode und der-Methode <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> :</span><span class="sxs-lookup"><span data-stu-id="e1ef9-115">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="e1ef9-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>wird an `AddAuthentication` das Standard Authentifizierungsschema für die APP festgelegt.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="e1ef9-117">`AuthenticationScheme`ist nützlich, wenn mehrere Instanzen der Cookie-Authentifizierung vorhanden sind und Sie die [Autorisierung mit einem bestimmten Schema durch](xref:security/authorization/limitingidentitybyscheme)laufen möchten.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-117">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="e1ef9-118">Durch Festlegen `AuthenticationScheme` von auf [cookieauthenticationdefaults. authenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) wird der Wert "Cookies" für das Schema bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-118">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="e1ef9-119">Sie können einen beliebigen Zeichen folgen Wert angeben, der das Schema unterscheidet.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-119">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="e1ef9-120">Das Authentifizierungsschema der APP unterscheidet sich vom Cookie-Authentifizierungsschema der app.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-120">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="e1ef9-121">Wenn kein cookieauthentifizierungsschema für bereitgestellt <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> wird, wird `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies") verwendet.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-121">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="e1ef9-122">Die-Eigenschaft des Authentifizierungs Cookies <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> ist standardmäßig auf festgelegt `true` .</span><span class="sxs-lookup"><span data-stu-id="e1ef9-122">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="e1ef9-123">Authentifizierungs Cookies sind zulässig, wenn ein Website Besucher der Datensammlung nicht zugestimmt hat.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-123">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="e1ef9-124">Weitere Informationen finden Sie unter <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-124">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="e1ef9-125">In `Startup.Configure` werden `UseAuthentication` und aufgerufen `UseAuthorization` , um die-Eigenschaft festzulegen `HttpContext.User` und die Autorisierungs Middleware für Anforderungen auszuführen.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-125">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="e1ef9-126">Rufen Sie `UseAuthentication` die `UseAuthorization` Methoden und vor, bevor Sie aufrufen `UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="e1ef9-126">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="e1ef9-127">Die- <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> Klasse wird verwendet, um die Authentifizierungs Anbieter Optionen zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-127">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="e1ef9-128">Legen Sie `CookieAuthenticationOptions` in der Dienst Konfiguration für die Authentifizierung in der- `Startup.ConfigureServices` Methode fest:</span><span class="sxs-lookup"><span data-stu-id="e1ef9-128">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="e1ef9-129">Cookie-Richtlinien Middleware</span><span class="sxs-lookup"><span data-stu-id="e1ef9-129">Cookie Policy Middleware</span></span>

<span data-ttu-id="e1ef9-130">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) Cookingrichtlinienmiddleware ermöglicht cookingrichtlinienfunktionen</span><span class="sxs-lookup"><span data-stu-id="e1ef9-130">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="e1ef9-131">Beim Hinzufügen der Middleware zur APP-Verarbeitungs Pipeline ist die Reihenfolge vertraulich, dass &mdash; Sie nur die in der Pipeline registrierten Downstreamkomponenten betrifft.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-131">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="e1ef9-132">Verwenden <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> Sie für die cookierichtlinienmiddleware, um globale Merkmale der Cookieverarbeitung zu steuern und in cookieverarbeitungs Handler zu eingebunden, wenn Cookies angehängt oder gelöscht werden.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="e1ef9-133">Der Standard <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> Wert ist `SameSiteMode.Lax` , um die OAuth2-Authentifizierung zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-133">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="e1ef9-134">Um eine Richtlinie für denselben Standort von streng zu erzwingen `SameSiteMode.Strict` , legen Sie den fest `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="e1ef9-134">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="e1ef9-135">Obwohl diese Einstellung OAuth2 und andere Ursprungs übergreifende Authentifizierungs Schemas unterbricht, erhöht Sie die Sicherheitsstufe für andere Typen von apps, die sich nicht auf die Ursprungs übergreifende Anforderungs Verarbeitung stützen.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-135">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="e1ef9-136">Die Middleware-Einstellung der Cookie-Richtlinie für `MinimumSameSitePolicy` kann die Einstellung von `Cookie.SameSite` in den `CookieAuthenticationOptions` Einstellungen entsprechend der folgenden Matrix beeinflussen.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-136">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="e1ef9-137">Minimumsamesitepolicy</span><span class="sxs-lookup"><span data-stu-id="e1ef9-137">MinimumSameSitePolicy</span></span> | <span data-ttu-id="e1ef9-138">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="e1ef9-138">Cookie.SameSite</span></span> | <span data-ttu-id="e1ef9-139">Resultierende Cookie. SameSite-Einstellung</span><span class="sxs-lookup"><span data-stu-id="e1ef9-139">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="e1ef9-140">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="e1ef9-140">SameSiteMode.None</span></span>     | <span data-ttu-id="e1ef9-141">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="e1ef9-141">SameSiteMode.None</span></span><br><span data-ttu-id="e1ef9-142">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="e1ef9-142">SameSiteMode.Lax</span></span><br><span data-ttu-id="e1ef9-143">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="e1ef9-143">SameSiteMode.Strict</span></span> | <span data-ttu-id="e1ef9-144">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="e1ef9-144">SameSiteMode.None</span></span><br><span data-ttu-id="e1ef9-145">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="e1ef9-145">SameSiteMode.Lax</span></span><br><span data-ttu-id="e1ef9-146">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="e1ef9-146">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="e1ef9-147">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="e1ef9-147">SameSiteMode.Lax</span></span>      | <span data-ttu-id="e1ef9-148">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="e1ef9-148">SameSiteMode.None</span></span><br><span data-ttu-id="e1ef9-149">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="e1ef9-149">SameSiteMode.Lax</span></span><br><span data-ttu-id="e1ef9-150">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="e1ef9-150">SameSiteMode.Strict</span></span> | <span data-ttu-id="e1ef9-151">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="e1ef9-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="e1ef9-152">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="e1ef9-152">SameSiteMode.Lax</span></span><br><span data-ttu-id="e1ef9-153">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="e1ef9-153">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="e1ef9-154">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="e1ef9-154">SameSiteMode.Strict</span></span>   | <span data-ttu-id="e1ef9-155">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="e1ef9-155">SameSiteMode.None</span></span><br><span data-ttu-id="e1ef9-156">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="e1ef9-156">SameSiteMode.Lax</span></span><br><span data-ttu-id="e1ef9-157">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="e1ef9-157">SameSiteMode.Strict</span></span> | <span data-ttu-id="e1ef9-158">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="e1ef9-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="e1ef9-159">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="e1ef9-159">SameSiteMode.Strict</span></span><br><span data-ttu-id="e1ef9-160">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="e1ef9-160">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="e1ef9-161">Erstellen eines Authentifizierungs Cookies</span><span class="sxs-lookup"><span data-stu-id="e1ef9-161">Create an authentication cookie</span></span>

<span data-ttu-id="e1ef9-162">Zum Erstellen eines Cookies, das Benutzerinformationen enthält, erstellen Sie eine <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="e1ef9-162">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="e1ef9-163">Die Benutzerinformationen werden serialisiert und im Cookie gespeichert.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-163">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="e1ef9-164">Erstellen <xref:System.Security.Claims.ClaimsIdentity> Sie mit allen erforderlichen <xref:System.Security.Claims.Claim> s, und rufen <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> Sie auf, um den Benutzer anzumelden:</span><span class="sxs-lookup"><span data-stu-id="e1ef9-164">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="e1ef9-165">`SignInAsync`erstellt ein verschlüsseltes Cookie und fügt es der aktuellen Antwort hinzu.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-165">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="e1ef9-166">Wenn `AuthenticationScheme` nicht angegeben ist, wird das Standardschema verwendet.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-166">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="e1ef9-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri>wird standardmäßig nur für einige bestimmte Pfade verwendet, z. b. für den Anmelde Pfad und die Abmelde Pfade.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> is only used on a few specific paths by default, for example, the login path and logout paths.</span></span> <span data-ttu-id="e1ef9-168">Weitere Informationen finden Sie in der [cookieauthenticationhandler-Quelle](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span><span class="sxs-lookup"><span data-stu-id="e1ef9-168">For more information see the [CookieAuthenticationHandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span></span>

<span data-ttu-id="e1ef9-169">ASP.net Core- [Datenschutz](xref:security/data-protection/using-data-protection) System wird für die Verschlüsselung verwendet.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-169">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="e1ef9-170">Konfigurieren Sie für eine APP, die auf mehreren Computern gehostet wird, den Lastenausgleich über apps oder eine Webfarm, den [Datenschutz](xref:security/data-protection/configuration/overview) so, dass derselbe Schlüsselbund und der APP-Bezeichner verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-170">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="e1ef9-171">Abmelden</span><span class="sxs-lookup"><span data-stu-id="e1ef9-171">Sign out</span></span>

<span data-ttu-id="e1ef9-172">Zum Abmelden des aktuellen Benutzers und zum Löschen des Cookies wenden Sie Folgendes an <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="e1ef9-172">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="e1ef9-173">Wenn `CookieAuthenticationDefaults.AuthenticationScheme` (oder "Cookies") nicht als Schema (z. b. "contosocookie") verwendet wird, geben Sie das Schema an, das beim Konfigurieren des Authentifizierungs Anbieters verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-173">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="e1ef9-174">Andernfalls wird das Standardschema verwendet.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-174">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="e1ef9-175">Reagieren auf Back-End-Änderungen</span><span class="sxs-lookup"><span data-stu-id="e1ef9-175">React to back-end changes</span></span>

<span data-ttu-id="e1ef9-176">Nachdem ein Cookie erstellt wurde, ist das Cookie die einzige Quelle der Identität.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-176">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="e1ef9-177">Wenn ein Benutzerkonto in Back-End-Systemen deaktiviert ist:</span><span class="sxs-lookup"><span data-stu-id="e1ef9-177">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="e1ef9-178">Das cookieauthentifizierungssystem der APP verarbeitet weiterhin Anforderungen auf der Grundlage des Authentifizierungs Cookies.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-178">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="e1ef9-179">Der Benutzer bleibt bei der App angemeldet, solange das Authentifizierungs Cookie gültig ist.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-179">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="e1ef9-180">Das <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> -Ereignis kann verwendet werden, um die Überprüfung der Cookie-Identität abzufangen und zu überschreiben.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-180">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="e1ef9-181">Das Überprüfen des Cookies bei jeder Anforderung verringert das Risiko, dass widerrufene Benutzer auf die App zugreifen.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-181">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="e1ef9-182">Ein Ansatz für die Cookievalidierung basiert darauf, wann die Benutzerdatenbank geändert wird.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-182">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="e1ef9-183">Wenn die Datenbank nicht geändert wurde, weil das Cookie des Benutzers ausgestellt wurde, ist es nicht erforderlich, den Benutzer erneut zu authentifizieren, wenn das Cookie noch gültig ist.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-183">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="e1ef9-184">In der Beispiel-APP wird die Datenbank in implementiert `IUserRepository` und speichert einen `LastChanged` Wert.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-184">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="e1ef9-185">Wenn ein Benutzer in der Datenbank aktualisiert wird, `LastChanged` wird der Wert auf die aktuelle Zeit festgelegt.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-185">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="e1ef9-186">`LastChanged`Erstellen Sie das Cookie mit einem Anspruch, der `LastChanged` den aktuellen `LastChanged` Wert aus der Datenbank enthält, um ein Cookie für ungültig zu erklären, wenn sich die Datenbank auf der Grundlage des Werts ändert:</span><span class="sxs-lookup"><span data-stu-id="e1ef9-186">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="e1ef9-187">Um eine außer Kraft setzung für das-Ereignis zu implementieren `ValidatePrincipal` , schreiben Sie eine-Methode mit der folgenden Signatur in eine Klasse, die von abgeleitet ist <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="e1ef9-187">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="e1ef9-188">Im folgenden finden Sie eine Beispiel Implementierung von `CookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="e1ef9-188">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="e1ef9-189">Registrieren Sie die Ereignis Instanz während der Registrierung des Cookie-Dienstanbieter in der `Startup.ConfigureServices` Methode.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-189">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="e1ef9-190">Stellen Sie eine Bereichs bezogene [Dienst Registrierung](xref:fundamentals/dependency-injection#service-lifetimes) für die `CustomCookieAuthenticationEvents` Klasse bereit:</span><span class="sxs-lookup"><span data-stu-id="e1ef9-190">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="e1ef9-191">Stellen Sie sich eine Situation vor, in der der Name des Benutzers &mdash; eine Entscheidung aktualisiert, die die Sicherheit in keiner Weise beeinträchtigt.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-191">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="e1ef9-192">Wenn Sie den Benutzer Prinzipal nicht dedestruktiv aktualisieren möchten, müssen Sie aufrufen `context.ReplacePrincipal` und die- `context.ShouldRenew` Eigenschaft auf festlegen `true` .</span><span class="sxs-lookup"><span data-stu-id="e1ef9-192">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="e1ef9-193">Der hier beschriebene Ansatz wird bei jeder Anforderung ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-193">The approach described here is triggered on every request.</span></span> <span data-ttu-id="e1ef9-194">Das Überprüfen von Authentifizierungs Cookies für alle Benutzer bei jeder Anforderung kann zu einer hohen Leistungs Einbuße für die APP führen.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-194">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="e1ef9-195">Persistente Cookies</span><span class="sxs-lookup"><span data-stu-id="e1ef9-195">Persistent cookies</span></span>

<span data-ttu-id="e1ef9-196">Möglicherweise möchten Sie, dass das Cookie über Browsersitzungen hinweg beibehalten wird.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-196">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="e1ef9-197">Diese Persistenz sollte nur mit der expliziten Zustimmung des Benutzers mit dem Kontrollkästchen "merken" bei der Anmeldung oder einem ähnlichen Mechanismus aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-197">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="e1ef9-198">Der folgende Code Ausschnitt erstellt eine Identität und entsprechendes Cookie, das bei Browser Abschlüssen überlebt.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-198">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="e1ef9-199">Alle zuvor konfigurierten gleitenden Ablauf Einstellungen werden berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-199">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="e1ef9-200">Wenn das Cookie abläuft, während der Browser geschlossen wird, löscht der Browser das Cookie, sobald es neu gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-200">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="e1ef9-201"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>Auf `true` in festlegen <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="e1ef9-201">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="e1ef9-202">Absoluter Cookieablauf</span><span class="sxs-lookup"><span data-stu-id="e1ef9-202">Absolute cookie expiration</span></span>

<span data-ttu-id="e1ef9-203">Eine absolute Ablaufzeit kann mit festgelegt werden <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="e1ef9-203">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="e1ef9-204">Zum Erstellen eines persistenten Cookies `IsPersistent` muss ebenfalls festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-204">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="e1ef9-205">Andernfalls wird das Cookie mit einer Sitzungs basierten Lebensdauer erstellt und kann entweder vor oder nach dem Authentifizierungs Ticket ablaufen, das es enthält.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-205">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="e1ef9-206">Wenn `ExpiresUtc` festgelegt ist, wird der Wert der- <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> Option von <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> , sofern festgelegt, überschrieben.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-206">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="e1ef9-207">Der folgende Code Ausschnitt erstellt eine Identität und entsprechendes Cookie, das 20 Minuten lang dauert.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-207">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="e1ef9-208">Dadurch werden alle zuvor konfigurierten gleitenden Ablauf Einstellungen ignoriert.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-208">This ignores any sliding expiration settings previously configured.</span></span>

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

<span data-ttu-id="e1ef9-209">ASP.net Core Identity ist ein vollständiger Authentifizierungs Anbieter mit vollem Funktionsumfang zum Erstellen und warten von Anmeldungen.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-209">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="e1ef9-210">Es kann jedoch ein cookiebasierter Authentifizierungs Anbieter ohne ASP.net Core Identity verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-210">However, a cookie-based authentication authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="e1ef9-211">Weitere Informationen finden Sie unter <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-211">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="e1ef9-212">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e1ef9-212">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e1ef9-213">Zu Demonstrationszwecken in der Beispiel-APP ist das Benutzerkonto für den hypothetischen Benutzer Maria Rodriguez in der APP hart codiert.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-213">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="e1ef9-214">Verwenden Sie die **e-Mail-** Adresse `maria.rodriguez@contoso.com` und jedes Kennwort, um den Benutzer anzumelden.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-214">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="e1ef9-215">Der Benutzer wird in der `AuthenticateUser` -Methode in der Datei *pages/Account/Login. cshtml. cs* authentifiziert.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-215">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="e1ef9-216">In einem realen Beispiel wird der Benutzer für eine-Datenbank authentifiziert.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-216">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="e1ef9-217">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="e1ef9-217">Configuration</span></span>

<span data-ttu-id="e1ef9-218">Wenn die APP das [Metapaket Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app)nicht verwendet, erstellen Sie einen Paket Verweis in der Projektdatei für das Paket [Microsoft. aspnetcore. Authentication. Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .</span><span class="sxs-lookup"><span data-stu-id="e1ef9-218">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="e1ef9-219">Erstellen Sie in der `Startup.ConfigureServices` -Methode den Authentifizierungs-Middleware-Dienst mit den <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> Methoden und:</span><span class="sxs-lookup"><span data-stu-id="e1ef9-219">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="e1ef9-220"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>wird an `AddAuthentication` das Standard Authentifizierungsschema für die APP festgelegt.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-220"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="e1ef9-221">`AuthenticationScheme`ist nützlich, wenn mehrere Instanzen der Cookie-Authentifizierung vorhanden sind und Sie die [Autorisierung mit einem bestimmten Schema durch](xref:security/authorization/limitingidentitybyscheme)laufen möchten.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-221">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="e1ef9-222">Durch Festlegen `AuthenticationScheme` von auf [cookieauthenticationdefaults. authenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) wird der Wert "Cookies" für das Schema bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-222">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="e1ef9-223">Sie können einen beliebigen Zeichen folgen Wert angeben, der das Schema unterscheidet.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-223">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="e1ef9-224">Das Authentifizierungsschema der APP unterscheidet sich vom Cookie-Authentifizierungsschema der app.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-224">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="e1ef9-225">Wenn kein cookieauthentifizierungsschema für bereitgestellt <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> wird, wird `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies") verwendet.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-225">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="e1ef9-226">Die-Eigenschaft des Authentifizierungs Cookies <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> ist standardmäßig auf festgelegt `true` .</span><span class="sxs-lookup"><span data-stu-id="e1ef9-226">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="e1ef9-227">Authentifizierungs Cookies sind zulässig, wenn ein Website Besucher der Datensammlung nicht zugestimmt hat.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-227">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="e1ef9-228">Weitere Informationen finden Sie unter <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-228">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="e1ef9-229">Rufen Sie in der- `Startup.Configure` Methode die- `UseAuthentication` Methode auf, um die Authentifizierungs Middleware aufzurufen, die die- `HttpContext.User` Eigenschaft festlegt.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-229">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="e1ef9-230">Rufen Sie die-Methode auf, `UseAuthentication` bevor Sie `UseMvcWithDefaultRoute` oder aufrufen `UseMvc` :</span><span class="sxs-lookup"><span data-stu-id="e1ef9-230">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="e1ef9-231">Die- <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> Klasse wird verwendet, um die Authentifizierungs Anbieter Optionen zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-231">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="e1ef9-232">Legen Sie `CookieAuthenticationOptions` in der Dienst Konfiguration für die Authentifizierung in der- `Startup.ConfigureServices` Methode fest:</span><span class="sxs-lookup"><span data-stu-id="e1ef9-232">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="e1ef9-233">Cookie-Richtlinien Middleware</span><span class="sxs-lookup"><span data-stu-id="e1ef9-233">Cookie Policy Middleware</span></span>

<span data-ttu-id="e1ef9-234">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) Cookingrichtlinienmiddleware ermöglicht cookingrichtlinienfunktionen</span><span class="sxs-lookup"><span data-stu-id="e1ef9-234">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="e1ef9-235">Beim Hinzufügen der Middleware zur APP-Verarbeitungs Pipeline ist die Reihenfolge vertraulich, dass &mdash; Sie nur die in der Pipeline registrierten Downstreamkomponenten betrifft.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-235">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="e1ef9-236">Verwenden <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> Sie für die cookierichtlinienmiddleware, um globale Merkmale der Cookieverarbeitung zu steuern und in cookieverarbeitungs Handler zu eingebunden, wenn Cookies angehängt oder gelöscht werden.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-236">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="e1ef9-237">Der Standard <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> Wert ist `SameSiteMode.Lax` , um die OAuth2-Authentifizierung zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-237">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="e1ef9-238">Um eine Richtlinie für denselben Standort von streng zu erzwingen `SameSiteMode.Strict` , legen Sie den fest `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="e1ef9-238">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="e1ef9-239">Obwohl diese Einstellung OAuth2 und andere Ursprungs übergreifende Authentifizierungs Schemas unterbricht, erhöht Sie die Sicherheitsstufe für andere Typen von apps, die sich nicht auf die Ursprungs übergreifende Anforderungs Verarbeitung stützen.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-239">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="e1ef9-240">Die Middleware-Einstellung der Cookie-Richtlinie für `MinimumSameSitePolicy` kann die Einstellung von `Cookie.SameSite` in den `CookieAuthenticationOptions` Einstellungen entsprechend der folgenden Matrix beeinflussen.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-240">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="e1ef9-241">Minimumsamesitepolicy</span><span class="sxs-lookup"><span data-stu-id="e1ef9-241">MinimumSameSitePolicy</span></span> | <span data-ttu-id="e1ef9-242">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="e1ef9-242">Cookie.SameSite</span></span> | <span data-ttu-id="e1ef9-243">Resultierende Cookie. SameSite-Einstellung</span><span class="sxs-lookup"><span data-stu-id="e1ef9-243">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="e1ef9-244">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="e1ef9-244">SameSiteMode.None</span></span>     | <span data-ttu-id="e1ef9-245">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="e1ef9-245">SameSiteMode.None</span></span><br><span data-ttu-id="e1ef9-246">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="e1ef9-246">SameSiteMode.Lax</span></span><br><span data-ttu-id="e1ef9-247">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="e1ef9-247">SameSiteMode.Strict</span></span> | <span data-ttu-id="e1ef9-248">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="e1ef9-248">SameSiteMode.None</span></span><br><span data-ttu-id="e1ef9-249">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="e1ef9-249">SameSiteMode.Lax</span></span><br><span data-ttu-id="e1ef9-250">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="e1ef9-250">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="e1ef9-251">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="e1ef9-251">SameSiteMode.Lax</span></span>      | <span data-ttu-id="e1ef9-252">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="e1ef9-252">SameSiteMode.None</span></span><br><span data-ttu-id="e1ef9-253">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="e1ef9-253">SameSiteMode.Lax</span></span><br><span data-ttu-id="e1ef9-254">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="e1ef9-254">SameSiteMode.Strict</span></span> | <span data-ttu-id="e1ef9-255">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="e1ef9-255">SameSiteMode.Lax</span></span><br><span data-ttu-id="e1ef9-256">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="e1ef9-256">SameSiteMode.Lax</span></span><br><span data-ttu-id="e1ef9-257">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="e1ef9-257">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="e1ef9-258">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="e1ef9-258">SameSiteMode.Strict</span></span>   | <span data-ttu-id="e1ef9-259">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="e1ef9-259">SameSiteMode.None</span></span><br><span data-ttu-id="e1ef9-260">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="e1ef9-260">SameSiteMode.Lax</span></span><br><span data-ttu-id="e1ef9-261">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="e1ef9-261">SameSiteMode.Strict</span></span> | <span data-ttu-id="e1ef9-262">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="e1ef9-262">SameSiteMode.Strict</span></span><br><span data-ttu-id="e1ef9-263">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="e1ef9-263">SameSiteMode.Strict</span></span><br><span data-ttu-id="e1ef9-264">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="e1ef9-264">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="e1ef9-265">Erstellen eines Authentifizierungs Cookies</span><span class="sxs-lookup"><span data-stu-id="e1ef9-265">Create an authentication cookie</span></span>

<span data-ttu-id="e1ef9-266">Zum Erstellen eines Cookies, das Benutzerinformationen enthält, erstellen Sie eine <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="e1ef9-266">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="e1ef9-267">Die Benutzerinformationen werden serialisiert und im Cookie gespeichert.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-267">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="e1ef9-268">Erstellen <xref:System.Security.Claims.ClaimsIdentity> Sie mit allen erforderlichen <xref:System.Security.Claims.Claim> s, und rufen <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> Sie auf, um den Benutzer anzumelden:</span><span class="sxs-lookup"><span data-stu-id="e1ef9-268">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="e1ef9-269">`SignInAsync`erstellt ein verschlüsseltes Cookie und fügt es der aktuellen Antwort hinzu.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-269">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="e1ef9-270">Wenn `AuthenticationScheme` nicht angegeben ist, wird das Standardschema verwendet.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-270">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="e1ef9-271">ASP.net Core- [Datenschutz](xref:security/data-protection/using-data-protection) System wird für die Verschlüsselung verwendet.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-271">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="e1ef9-272">Konfigurieren Sie für eine APP, die auf mehreren Computern gehostet wird, den Lastenausgleich über apps oder eine Webfarm, den [Datenschutz](xref:security/data-protection/configuration/overview) so, dass derselbe Schlüsselbund und der APP-Bezeichner verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-272">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="e1ef9-273">Abmelden</span><span class="sxs-lookup"><span data-stu-id="e1ef9-273">Sign out</span></span>

<span data-ttu-id="e1ef9-274">Zum Abmelden des aktuellen Benutzers und zum Löschen des Cookies wenden Sie Folgendes an <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="e1ef9-274">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="e1ef9-275">Wenn `CookieAuthenticationDefaults.AuthenticationScheme` (oder "Cookies") nicht als Schema (z. b. "contosocookie") verwendet wird, geben Sie das Schema an, das beim Konfigurieren des Authentifizierungs Anbieters verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-275">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="e1ef9-276">Andernfalls wird das Standardschema verwendet.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-276">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="e1ef9-277">Reagieren auf Back-End-Änderungen</span><span class="sxs-lookup"><span data-stu-id="e1ef9-277">React to back-end changes</span></span>

<span data-ttu-id="e1ef9-278">Nachdem ein Cookie erstellt wurde, ist das Cookie die einzige Quelle der Identität.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-278">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="e1ef9-279">Wenn ein Benutzerkonto in Back-End-Systemen deaktiviert ist:</span><span class="sxs-lookup"><span data-stu-id="e1ef9-279">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="e1ef9-280">Das cookieauthentifizierungssystem der APP verarbeitet weiterhin Anforderungen auf der Grundlage des Authentifizierungs Cookies.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-280">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="e1ef9-281">Der Benutzer bleibt bei der App angemeldet, solange das Authentifizierungs Cookie gültig ist.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-281">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="e1ef9-282">Das <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> -Ereignis kann verwendet werden, um die Überprüfung der Cookie-Identität abzufangen und zu überschreiben.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-282">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="e1ef9-283">Das Überprüfen des Cookies bei jeder Anforderung verringert das Risiko, dass widerrufene Benutzer auf die App zugreifen.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-283">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="e1ef9-284">Ein Ansatz für die Cookievalidierung basiert darauf, wann die Benutzerdatenbank geändert wird.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-284">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="e1ef9-285">Wenn die Datenbank nicht geändert wurde, weil das Cookie des Benutzers ausgestellt wurde, ist es nicht erforderlich, den Benutzer erneut zu authentifizieren, wenn das Cookie noch gültig ist.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-285">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="e1ef9-286">In der Beispiel-APP wird die Datenbank in implementiert `IUserRepository` und speichert einen `LastChanged` Wert.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-286">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="e1ef9-287">Wenn ein Benutzer in der Datenbank aktualisiert wird, `LastChanged` wird der Wert auf die aktuelle Zeit festgelegt.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-287">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="e1ef9-288">`LastChanged`Erstellen Sie das Cookie mit einem Anspruch, der `LastChanged` den aktuellen `LastChanged` Wert aus der Datenbank enthält, um ein Cookie für ungültig zu erklären, wenn sich die Datenbank auf der Grundlage des Werts ändert:</span><span class="sxs-lookup"><span data-stu-id="e1ef9-288">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="e1ef9-289">Um eine außer Kraft setzung für das-Ereignis zu implementieren `ValidatePrincipal` , schreiben Sie eine-Methode mit der folgenden Signatur in eine Klasse, die von abgeleitet ist <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="e1ef9-289">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="e1ef9-290">Im folgenden finden Sie eine Beispiel Implementierung von `CookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="e1ef9-290">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="e1ef9-291">Registrieren Sie die Ereignis Instanz während der Registrierung des Cookie-Dienstanbieter in der `Startup.ConfigureServices` Methode.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-291">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="e1ef9-292">Stellen Sie eine Bereichs bezogene [Dienst Registrierung](xref:fundamentals/dependency-injection#service-lifetimes) für die `CustomCookieAuthenticationEvents` Klasse bereit:</span><span class="sxs-lookup"><span data-stu-id="e1ef9-292">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="e1ef9-293">Stellen Sie sich eine Situation vor, in der der Name des Benutzers &mdash; eine Entscheidung aktualisiert, die die Sicherheit in keiner Weise beeinträchtigt.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-293">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="e1ef9-294">Wenn Sie den Benutzer Prinzipal nicht dedestruktiv aktualisieren möchten, müssen Sie aufrufen `context.ReplacePrincipal` und die- `context.ShouldRenew` Eigenschaft auf festlegen `true` .</span><span class="sxs-lookup"><span data-stu-id="e1ef9-294">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="e1ef9-295">Der hier beschriebene Ansatz wird bei jeder Anforderung ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-295">The approach described here is triggered on every request.</span></span> <span data-ttu-id="e1ef9-296">Das Überprüfen von Authentifizierungs Cookies für alle Benutzer bei jeder Anforderung kann zu einer hohen Leistungs Einbuße für die APP führen.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-296">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="e1ef9-297">Persistente Cookies</span><span class="sxs-lookup"><span data-stu-id="e1ef9-297">Persistent cookies</span></span>

<span data-ttu-id="e1ef9-298">Möglicherweise möchten Sie, dass das Cookie über Browsersitzungen hinweg beibehalten wird.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-298">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="e1ef9-299">Diese Persistenz sollte nur mit der expliziten Zustimmung des Benutzers mit dem Kontrollkästchen "merken" bei der Anmeldung oder einem ähnlichen Mechanismus aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-299">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="e1ef9-300">Der folgende Code Ausschnitt erstellt eine Identität und entsprechendes Cookie, das bei Browser Abschlüssen überlebt.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-300">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="e1ef9-301">Alle zuvor konfigurierten gleitenden Ablauf Einstellungen werden berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-301">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="e1ef9-302">Wenn das Cookie abläuft, während der Browser geschlossen wird, löscht der Browser das Cookie, sobald es neu gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-302">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="e1ef9-303"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>Auf `true` in festlegen <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="e1ef9-303">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="e1ef9-304">Absoluter Cookieablauf</span><span class="sxs-lookup"><span data-stu-id="e1ef9-304">Absolute cookie expiration</span></span>

<span data-ttu-id="e1ef9-305">Eine absolute Ablaufzeit kann mit festgelegt werden <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="e1ef9-305">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="e1ef9-306">Zum Erstellen eines persistenten Cookies `IsPersistent` muss ebenfalls festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-306">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="e1ef9-307">Andernfalls wird das Cookie mit einer Sitzungs basierten Lebensdauer erstellt und kann entweder vor oder nach dem Authentifizierungs Ticket ablaufen, das es enthält.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-307">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="e1ef9-308">Wenn `ExpiresUtc` festgelegt ist, wird der Wert der- <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> Option von <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions> , sofern festgelegt, überschrieben.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-308">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="e1ef9-309">Der folgende Code Ausschnitt erstellt eine Identität und entsprechendes Cookie, das 20 Minuten lang dauert.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-309">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="e1ef9-310">Dadurch werden alle zuvor konfigurierten gleitenden Ablauf Einstellungen ignoriert.</span><span class="sxs-lookup"><span data-stu-id="e1ef9-310">This ignores any sliding expiration settings previously configured.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="e1ef9-311">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="e1ef9-311">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="e1ef9-312">Richtlinien basierte Rollen Überprüfungen</span><span class="sxs-lookup"><span data-stu-id="e1ef9-312">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>

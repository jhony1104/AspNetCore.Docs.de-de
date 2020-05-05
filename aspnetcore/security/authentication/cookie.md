---
title: Verwenden der Cookie-Authentifizierung ohne ASP.net CoreIdentity
author: rick-anderson
description: Erfahren Sie, wie Sie die Cookie- IdentityAuthentifizierung ohne ASP.net Core verwenden.
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
ms.openlocfilehash: c179b3657ad4cbda960c2afe685a63f3266a7402
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773843"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a><span data-ttu-id="97dad-103">Verwenden der Cookie-Authentifizierung ohne ASP.net Core Identität</span><span class="sxs-lookup"><span data-stu-id="97dad-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="97dad-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="97dad-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="97dad-105">ASP.net Core Identity ist ein vollständiger Authentifizierungs Anbieter mit vollem Funktionsumfang zum Erstellen und warten von Anmeldungen.</span><span class="sxs-lookup"><span data-stu-id="97dad-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="97dad-106">Es kann jedoch ein cookiebasierter Authentifizierungs Anbieter ohne ASP.net Core Identität verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="97dad-106">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="97dad-107">Weitere Informationen finden Sie unter <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="97dad-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="97dad-108">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="97dad-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="97dad-109">Zu Demonstrationszwecken in der Beispiel-APP ist das Benutzerkonto für den hypothetischen Benutzer Maria Rodriguez in der APP hart codiert.</span><span class="sxs-lookup"><span data-stu-id="97dad-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="97dad-110">Verwenden Sie die **e-Mail-** Adresse `maria.rodriguez@contoso.com` und jedes Kennwort, um den Benutzer anzumelden.</span><span class="sxs-lookup"><span data-stu-id="97dad-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="97dad-111">Der Benutzer wird in der- `AuthenticateUser` Methode in der Datei *pages/Account/Login. cshtml. cs* authentifiziert.</span><span class="sxs-lookup"><span data-stu-id="97dad-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="97dad-112">In einem realen Beispiel wird der Benutzer für eine-Datenbank authentifiziert.</span><span class="sxs-lookup"><span data-stu-id="97dad-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="97dad-113">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="97dad-113">Configuration</span></span>

<span data-ttu-id="97dad-114">Wenn die APP das [Metapaket Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app)nicht verwendet, erstellen Sie einen Paket Verweis in der Projektdatei für das Paket [Microsoft. aspnetcore. Authentication. Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .</span><span class="sxs-lookup"><span data-stu-id="97dad-114">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="97dad-115">Erstellen Sie `Startup.ConfigureServices` in der-Methode die Authentifizierungs-Middleware- <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> Dienste <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> mit der-Methode und der-Methode:</span><span class="sxs-lookup"><span data-stu-id="97dad-115">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="97dad-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>wird an `AddAuthentication` das Standard Authentifizierungsschema für die APP festgelegt.</span><span class="sxs-lookup"><span data-stu-id="97dad-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="97dad-117">`AuthenticationScheme`ist nützlich, wenn mehrere Instanzen der Cookie-Authentifizierung vorhanden sind und Sie die [Autorisierung mit einem bestimmten Schema durch](xref:security/authorization/limitingidentitybyscheme)laufen möchten.</span><span class="sxs-lookup"><span data-stu-id="97dad-117">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="97dad-118">Durch Festlegen `AuthenticationScheme` von auf [cookieauthenticationdefaults. authenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) wird der Wert "Cookies" für das Schema bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="97dad-118">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="97dad-119">Sie können einen beliebigen Zeichen folgen Wert angeben, der das Schema unterscheidet.</span><span class="sxs-lookup"><span data-stu-id="97dad-119">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="97dad-120">Das Authentifizierungsschema der APP unterscheidet sich vom Cookie-Authentifizierungsschema der app.</span><span class="sxs-lookup"><span data-stu-id="97dad-120">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="97dad-121">Wenn kein cookieauthentifizierungsschema <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>für bereitgestellt `CookieAuthenticationDefaults.AuthenticationScheme` wird, wird ("Cookies") verwendet.</span><span class="sxs-lookup"><span data-stu-id="97dad-121">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="97dad-122">Die- <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> Eigenschaft des Authentifizierungs Cookies ist Standard `true` mäßig auf festgelegt.</span><span class="sxs-lookup"><span data-stu-id="97dad-122">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="97dad-123">Authentifizierungs Cookies sind zulässig, wenn ein Website Besucher der Datensammlung nicht zugestimmt hat.</span><span class="sxs-lookup"><span data-stu-id="97dad-123">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="97dad-124">Weitere Informationen finden Sie unter <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="97dad-124">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="97dad-125">In `Startup.Configure`werden und `UseAuthentication` `UseAuthorization` aufgerufen, um die `HttpContext.User` -Eigenschaft festzulegen und die Autorisierungs Middleware für Anforderungen auszuführen.</span><span class="sxs-lookup"><span data-stu-id="97dad-125">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="97dad-126">Rufen Sie `UseAuthentication` die `UseAuthorization` Methoden und vor `UseEndpoints`, bevor Sie aufrufen:</span><span class="sxs-lookup"><span data-stu-id="97dad-126">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="97dad-127">Die <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> -Klasse wird verwendet, um die Authentifizierungs Anbieter Optionen zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="97dad-127">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="97dad-128">Legen `CookieAuthenticationOptions` Sie in der Dienst Konfiguration für die Authentifizierung `Startup.ConfigureServices` in der-Methode fest:</span><span class="sxs-lookup"><span data-stu-id="97dad-128">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="97dad-129">Cookie-Richtlinien Middleware</span><span class="sxs-lookup"><span data-stu-id="97dad-129">Cookie Policy Middleware</span></span>

<span data-ttu-id="97dad-130">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) Cookingrichtlinienmiddleware ermöglicht cookingrichtlinienfunktionen</span><span class="sxs-lookup"><span data-stu-id="97dad-130">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="97dad-131">Beim Hinzufügen der Middleware zur APP-Verarbeitungs Pipeline ist&mdash;die Reihenfolge vertraulich, dass Sie nur die in der Pipeline registrierten Downstreamkomponenten betrifft.</span><span class="sxs-lookup"><span data-stu-id="97dad-131">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="97dad-132">Verwenden <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> Sie für die cookierichtlinienmiddleware, um globale Merkmale der Cookieverarbeitung zu steuern und in cookieverarbeitungs Handler zu eingebunden, wenn Cookies angehängt oder gelöscht werden.</span><span class="sxs-lookup"><span data-stu-id="97dad-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="97dad-133">Der Standard <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> Wert ist `SameSiteMode.Lax` , um die OAuth2-Authentifizierung zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="97dad-133">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="97dad-134">Um eine Richtlinie für denselben Standort von `SameSiteMode.Strict`streng zu erzwingen, `MinimumSameSitePolicy`legen Sie den fest.</span><span class="sxs-lookup"><span data-stu-id="97dad-134">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="97dad-135">Obwohl diese Einstellung OAuth2 und andere Ursprungs übergreifende Authentifizierungs Schemas unterbricht, erhöht Sie die Sicherheitsstufe für andere Typen von apps, die sich nicht auf die Ursprungs übergreifende Anforderungs Verarbeitung stützen.</span><span class="sxs-lookup"><span data-stu-id="97dad-135">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="97dad-136">Die Middleware-Einstellung der Cookie `MinimumSameSitePolicy` -Richtlinie für kann `Cookie.SameSite` die `CookieAuthenticationOptions` Einstellung von in den Einstellungen entsprechend der folgenden Matrix beeinflussen.</span><span class="sxs-lookup"><span data-stu-id="97dad-136">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="97dad-137">Minimumsamesitepolicy</span><span class="sxs-lookup"><span data-stu-id="97dad-137">MinimumSameSitePolicy</span></span> | <span data-ttu-id="97dad-138">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="97dad-138">Cookie.SameSite</span></span> | <span data-ttu-id="97dad-139">Resultierende Cookie. SameSite-Einstellung</span><span class="sxs-lookup"><span data-stu-id="97dad-139">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="97dad-140">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="97dad-140">SameSiteMode.None</span></span>     | <span data-ttu-id="97dad-141">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="97dad-141">SameSiteMode.None</span></span><br><span data-ttu-id="97dad-142">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="97dad-142">SameSiteMode.Lax</span></span><br><span data-ttu-id="97dad-143">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="97dad-143">SameSiteMode.Strict</span></span> | <span data-ttu-id="97dad-144">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="97dad-144">SameSiteMode.None</span></span><br><span data-ttu-id="97dad-145">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="97dad-145">SameSiteMode.Lax</span></span><br><span data-ttu-id="97dad-146">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="97dad-146">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="97dad-147">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="97dad-147">SameSiteMode.Lax</span></span>      | <span data-ttu-id="97dad-148">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="97dad-148">SameSiteMode.None</span></span><br><span data-ttu-id="97dad-149">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="97dad-149">SameSiteMode.Lax</span></span><br><span data-ttu-id="97dad-150">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="97dad-150">SameSiteMode.Strict</span></span> | <span data-ttu-id="97dad-151">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="97dad-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="97dad-152">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="97dad-152">SameSiteMode.Lax</span></span><br><span data-ttu-id="97dad-153">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="97dad-153">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="97dad-154">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="97dad-154">SameSiteMode.Strict</span></span>   | <span data-ttu-id="97dad-155">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="97dad-155">SameSiteMode.None</span></span><br><span data-ttu-id="97dad-156">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="97dad-156">SameSiteMode.Lax</span></span><br><span data-ttu-id="97dad-157">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="97dad-157">SameSiteMode.Strict</span></span> | <span data-ttu-id="97dad-158">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="97dad-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="97dad-159">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="97dad-159">SameSiteMode.Strict</span></span><br><span data-ttu-id="97dad-160">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="97dad-160">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="97dad-161">Erstellen eines Authentifizierungs Cookies</span><span class="sxs-lookup"><span data-stu-id="97dad-161">Create an authentication cookie</span></span>

<span data-ttu-id="97dad-162">Zum Erstellen eines Cookies, das Benutzerinformationen enthält, <xref:System.Security.Claims.ClaimsPrincipal>erstellen Sie eine.</span><span class="sxs-lookup"><span data-stu-id="97dad-162">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="97dad-163">Die Benutzerinformationen werden serialisiert und im Cookie gespeichert.</span><span class="sxs-lookup"><span data-stu-id="97dad-163">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="97dad-164">Erstellen Sie <xref:System.Security.Claims.ClaimsIdentity> mit allen erforderlichen <xref:System.Security.Claims.Claim>s, und <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> rufen Sie auf, um den Benutzer anzumelden:</span><span class="sxs-lookup"><span data-stu-id="97dad-164">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="97dad-165">`SignInAsync`erstellt ein verschlüsseltes Cookie und fügt es der aktuellen Antwort hinzu.</span><span class="sxs-lookup"><span data-stu-id="97dad-165">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="97dad-166">Wenn `AuthenticationScheme` nicht angegeben ist, wird das Standardschema verwendet.</span><span class="sxs-lookup"><span data-stu-id="97dad-166">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="97dad-167">ASP.net Core- [Datenschutz](xref:security/data-protection/using-data-protection) System wird für die Verschlüsselung verwendet.</span><span class="sxs-lookup"><span data-stu-id="97dad-167">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="97dad-168">Konfigurieren Sie für eine APP, die auf mehreren Computern gehostet wird, den Lastenausgleich über apps oder eine Webfarm, den [Datenschutz](xref:security/data-protection/configuration/overview) so, dass derselbe Schlüsselbund und der APP-Bezeichner verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="97dad-168">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="97dad-169">Abmelden</span><span class="sxs-lookup"><span data-stu-id="97dad-169">Sign out</span></span>

<span data-ttu-id="97dad-170">Zum Abmelden des aktuellen Benutzers und zum Löschen des Cookies wenden <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>Sie Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="97dad-170">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="97dad-171">Wenn `CookieAuthenticationDefaults.AuthenticationScheme` (oder "Cookies") nicht als Schema (z. b. "contosocookie") verwendet wird, geben Sie das Schema an, das beim Konfigurieren des Authentifizierungs Anbieters verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="97dad-171">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="97dad-172">Andernfalls wird das Standardschema verwendet.</span><span class="sxs-lookup"><span data-stu-id="97dad-172">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="97dad-173">Reagieren auf Back-End-Änderungen</span><span class="sxs-lookup"><span data-stu-id="97dad-173">React to back-end changes</span></span>

<span data-ttu-id="97dad-174">Nachdem ein Cookie erstellt wurde, ist das Cookie die einzige Quelle der Identität.</span><span class="sxs-lookup"><span data-stu-id="97dad-174">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="97dad-175">Wenn ein Benutzerkonto in Back-End-Systemen deaktiviert ist:</span><span class="sxs-lookup"><span data-stu-id="97dad-175">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="97dad-176">Das cookieauthentifizierungssystem der APP verarbeitet weiterhin Anforderungen auf der Grundlage des Authentifizierungs Cookies.</span><span class="sxs-lookup"><span data-stu-id="97dad-176">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="97dad-177">Der Benutzer bleibt bei der App angemeldet, solange das Authentifizierungs Cookie gültig ist.</span><span class="sxs-lookup"><span data-stu-id="97dad-177">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="97dad-178">Das <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> -Ereignis kann verwendet werden, um die Überprüfung der Cookie-Identität abzufangen und zu überschreiben.</span><span class="sxs-lookup"><span data-stu-id="97dad-178">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="97dad-179">Das Überprüfen des Cookies bei jeder Anforderung verringert das Risiko, dass widerrufene Benutzer auf die App zugreifen.</span><span class="sxs-lookup"><span data-stu-id="97dad-179">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="97dad-180">Ein Ansatz für die Cookievalidierung basiert darauf, wann die Benutzerdatenbank geändert wird.</span><span class="sxs-lookup"><span data-stu-id="97dad-180">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="97dad-181">Wenn die Datenbank nicht geändert wurde, weil das Cookie des Benutzers ausgestellt wurde, ist es nicht erforderlich, den Benutzer erneut zu authentifizieren, wenn das Cookie noch gültig ist.</span><span class="sxs-lookup"><span data-stu-id="97dad-181">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="97dad-182">In der Beispiel-APP wird die Datenbank in `IUserRepository` implementiert und speichert einen `LastChanged` Wert.</span><span class="sxs-lookup"><span data-stu-id="97dad-182">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="97dad-183">Wenn ein Benutzer in der Datenbank aktualisiert wird, wird `LastChanged` der Wert auf die aktuelle Zeit festgelegt.</span><span class="sxs-lookup"><span data-stu-id="97dad-183">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="97dad-184">Erstellen Sie das Cookie mit einem `LastChanged` `LastChanged` Anspruch, der den aktuellen `LastChanged` Wert aus der Datenbank enthält, um ein Cookie für ungültig zu erklären, wenn sich die Datenbank auf der Grundlage des Werts ändert:</span><span class="sxs-lookup"><span data-stu-id="97dad-184">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="97dad-185">Um eine außer Kraft setzung für `ValidatePrincipal` das-Ereignis zu implementieren, schreiben Sie eine-Methode mit der folgenden Signatur <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>in eine Klasse, die von abgeleitet ist:</span><span class="sxs-lookup"><span data-stu-id="97dad-185">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="97dad-186">Im folgenden finden Sie eine Beispiel Implementierung `CookieAuthenticationEvents`von:</span><span class="sxs-lookup"><span data-stu-id="97dad-186">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="97dad-187">Registrieren Sie die Ereignis Instanz während der Registrierung des Cookie `Startup.ConfigureServices` -Dienstanbieter in der Methode.</span><span class="sxs-lookup"><span data-stu-id="97dad-187">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="97dad-188">Stellen Sie eine Bereichs bezogene [Dienst Registrierung](xref:fundamentals/dependency-injection#service-lifetimes) für `CustomCookieAuthenticationEvents` die Klasse bereit:</span><span class="sxs-lookup"><span data-stu-id="97dad-188">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="97dad-189">Stellen Sie sich eine Situation vor, in der der Name&mdash;des Benutzers eine Entscheidung aktualisiert, die die Sicherheit in keiner Weise beeinträchtigt.</span><span class="sxs-lookup"><span data-stu-id="97dad-189">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="97dad-190">Wenn Sie den Benutzer Prinzipal nicht dedestruktiv aktualisieren möchten, müssen `context.ReplacePrincipal` Sie aufrufen und `context.ShouldRenew` die- `true`Eigenschaft auf festlegen.</span><span class="sxs-lookup"><span data-stu-id="97dad-190">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="97dad-191">Der hier beschriebene Ansatz wird bei jeder Anforderung ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="97dad-191">The approach described here is triggered on every request.</span></span> <span data-ttu-id="97dad-192">Das Überprüfen von Authentifizierungs Cookies für alle Benutzer bei jeder Anforderung kann zu einer hohen Leistungs Einbuße für die APP führen.</span><span class="sxs-lookup"><span data-stu-id="97dad-192">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="97dad-193">Persistente Cookies</span><span class="sxs-lookup"><span data-stu-id="97dad-193">Persistent cookies</span></span>

<span data-ttu-id="97dad-194">Möglicherweise möchten Sie, dass das Cookie über Browsersitzungen hinweg beibehalten wird.</span><span class="sxs-lookup"><span data-stu-id="97dad-194">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="97dad-195">Diese Persistenz sollte nur mit der expliziten Zustimmung des Benutzers mit dem Kontrollkästchen "merken" bei der Anmeldung oder einem ähnlichen Mechanismus aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="97dad-195">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="97dad-196">Der folgende Code Ausschnitt erstellt eine Identität und entsprechendes Cookie, das bei Browser Abschlüssen überlebt.</span><span class="sxs-lookup"><span data-stu-id="97dad-196">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="97dad-197">Alle zuvor konfigurierten gleitenden Ablauf Einstellungen werden berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="97dad-197">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="97dad-198">Wenn das Cookie abläuft, während der Browser geschlossen wird, löscht der Browser das Cookie, sobald es neu gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="97dad-198">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="97dad-199">Auf <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>festlegen:</span><span class="sxs-lookup"><span data-stu-id="97dad-199">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="97dad-200">Absoluter Cookieablauf</span><span class="sxs-lookup"><span data-stu-id="97dad-200">Absolute cookie expiration</span></span>

<span data-ttu-id="97dad-201">Eine absolute Ablaufzeit kann mit <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="97dad-201">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="97dad-202">Zum Erstellen eines persistenten Cookies `IsPersistent` muss ebenfalls festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="97dad-202">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="97dad-203">Andernfalls wird das Cookie mit einer Sitzungs basierten Lebensdauer erstellt und kann entweder vor oder nach dem Authentifizierungs Ticket ablaufen, das es enthält.</span><span class="sxs-lookup"><span data-stu-id="97dad-203">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="97dad-204">Wenn `ExpiresUtc` festgelegt ist, wird der Wert der- <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> Option von <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, sofern festgelegt, überschrieben.</span><span class="sxs-lookup"><span data-stu-id="97dad-204">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="97dad-205">Der folgende Code Ausschnitt erstellt eine Identität und entsprechendes Cookie, das 20 Minuten lang dauert.</span><span class="sxs-lookup"><span data-stu-id="97dad-205">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="97dad-206">Dadurch werden alle zuvor konfigurierten gleitenden Ablauf Einstellungen ignoriert.</span><span class="sxs-lookup"><span data-stu-id="97dad-206">This ignores any sliding expiration settings previously configured.</span></span>

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

<span data-ttu-id="97dad-207">ASP.net Core Identity ist ein vollständiger Authentifizierungs Anbieter mit vollem Funktionsumfang zum Erstellen und warten von Anmeldungen.</span><span class="sxs-lookup"><span data-stu-id="97dad-207">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="97dad-208">Es kann jedoch ein cookiebasierter Authentifizierungs Anbieter ohne ASP.net Core Identität verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="97dad-208">However, a cookie-based authentication authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="97dad-209">Weitere Informationen finden Sie unter <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="97dad-209">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="97dad-210">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="97dad-210">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="97dad-211">Zu Demonstrationszwecken in der Beispiel-APP ist das Benutzerkonto für den hypothetischen Benutzer Maria Rodriguez in der APP hart codiert.</span><span class="sxs-lookup"><span data-stu-id="97dad-211">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="97dad-212">Verwenden Sie die **e-Mail-** Adresse `maria.rodriguez@contoso.com` und jedes Kennwort, um den Benutzer anzumelden.</span><span class="sxs-lookup"><span data-stu-id="97dad-212">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="97dad-213">Der Benutzer wird in der- `AuthenticateUser` Methode in der Datei *pages/Account/Login. cshtml. cs* authentifiziert.</span><span class="sxs-lookup"><span data-stu-id="97dad-213">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="97dad-214">In einem realen Beispiel wird der Benutzer für eine-Datenbank authentifiziert.</span><span class="sxs-lookup"><span data-stu-id="97dad-214">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="97dad-215">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="97dad-215">Configuration</span></span>

<span data-ttu-id="97dad-216">Wenn die APP das [Metapaket Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app)nicht verwendet, erstellen Sie einen Paket Verweis in der Projektdatei für das Paket [Microsoft. aspnetcore. Authentication. Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .</span><span class="sxs-lookup"><span data-stu-id="97dad-216">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="97dad-217">Erstellen Sie `Startup.ConfigureServices` in der-Methode den Authentifizierungs-Middleware- <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> Dienst <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> mit den Methoden und:</span><span class="sxs-lookup"><span data-stu-id="97dad-217">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="97dad-218"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>wird an `AddAuthentication` das Standard Authentifizierungsschema für die APP festgelegt.</span><span class="sxs-lookup"><span data-stu-id="97dad-218"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="97dad-219">`AuthenticationScheme`ist nützlich, wenn mehrere Instanzen der Cookie-Authentifizierung vorhanden sind und Sie die [Autorisierung mit einem bestimmten Schema durch](xref:security/authorization/limitingidentitybyscheme)laufen möchten.</span><span class="sxs-lookup"><span data-stu-id="97dad-219">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="97dad-220">Durch Festlegen `AuthenticationScheme` von auf [cookieauthenticationdefaults. authenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) wird der Wert "Cookies" für das Schema bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="97dad-220">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="97dad-221">Sie können einen beliebigen Zeichen folgen Wert angeben, der das Schema unterscheidet.</span><span class="sxs-lookup"><span data-stu-id="97dad-221">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="97dad-222">Das Authentifizierungsschema der APP unterscheidet sich vom Cookie-Authentifizierungsschema der app.</span><span class="sxs-lookup"><span data-stu-id="97dad-222">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="97dad-223">Wenn kein cookieauthentifizierungsschema <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>für bereitgestellt `CookieAuthenticationDefaults.AuthenticationScheme` wird, wird ("Cookies") verwendet.</span><span class="sxs-lookup"><span data-stu-id="97dad-223">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="97dad-224">Die- <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> Eigenschaft des Authentifizierungs Cookies ist Standard `true` mäßig auf festgelegt.</span><span class="sxs-lookup"><span data-stu-id="97dad-224">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="97dad-225">Authentifizierungs Cookies sind zulässig, wenn ein Website Besucher der Datensammlung nicht zugestimmt hat.</span><span class="sxs-lookup"><span data-stu-id="97dad-225">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="97dad-226">Weitere Informationen finden Sie unter <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="97dad-226">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="97dad-227">Rufen Sie `Startup.Configure` in der-Methode `UseAuthentication` die-Methode auf, um die Authentifizierungs Middleware `HttpContext.User` aufzurufen, die die-Eigenschaft festlegt.</span><span class="sxs-lookup"><span data-stu-id="97dad-227">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="97dad-228">Rufen Sie `UseAuthentication` die-Methode `UseMvcWithDefaultRoute` auf `UseMvc`, bevor Sie oder aufrufen:</span><span class="sxs-lookup"><span data-stu-id="97dad-228">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="97dad-229">Die <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> -Klasse wird verwendet, um die Authentifizierungs Anbieter Optionen zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="97dad-229">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="97dad-230">Legen `CookieAuthenticationOptions` Sie in der Dienst Konfiguration für die Authentifizierung `Startup.ConfigureServices` in der-Methode fest:</span><span class="sxs-lookup"><span data-stu-id="97dad-230">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="97dad-231">Cookie-Richtlinien Middleware</span><span class="sxs-lookup"><span data-stu-id="97dad-231">Cookie Policy Middleware</span></span>

<span data-ttu-id="97dad-232">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) Cookingrichtlinienmiddleware ermöglicht cookingrichtlinienfunktionen</span><span class="sxs-lookup"><span data-stu-id="97dad-232">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="97dad-233">Beim Hinzufügen der Middleware zur APP-Verarbeitungs Pipeline ist&mdash;die Reihenfolge vertraulich, dass Sie nur die in der Pipeline registrierten Downstreamkomponenten betrifft.</span><span class="sxs-lookup"><span data-stu-id="97dad-233">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="97dad-234">Verwenden <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> Sie für die cookierichtlinienmiddleware, um globale Merkmale der Cookieverarbeitung zu steuern und in cookieverarbeitungs Handler zu eingebunden, wenn Cookies angehängt oder gelöscht werden.</span><span class="sxs-lookup"><span data-stu-id="97dad-234">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="97dad-235">Der Standard <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> Wert ist `SameSiteMode.Lax` , um die OAuth2-Authentifizierung zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="97dad-235">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="97dad-236">Um eine Richtlinie für denselben Standort von `SameSiteMode.Strict`streng zu erzwingen, `MinimumSameSitePolicy`legen Sie den fest.</span><span class="sxs-lookup"><span data-stu-id="97dad-236">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="97dad-237">Obwohl diese Einstellung OAuth2 und andere Ursprungs übergreifende Authentifizierungs Schemas unterbricht, erhöht Sie die Sicherheitsstufe für andere Typen von apps, die sich nicht auf die Ursprungs übergreifende Anforderungs Verarbeitung stützen.</span><span class="sxs-lookup"><span data-stu-id="97dad-237">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="97dad-238">Die Middleware-Einstellung der Cookie `MinimumSameSitePolicy` -Richtlinie für kann `Cookie.SameSite` die `CookieAuthenticationOptions` Einstellung von in den Einstellungen entsprechend der folgenden Matrix beeinflussen.</span><span class="sxs-lookup"><span data-stu-id="97dad-238">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="97dad-239">Minimumsamesitepolicy</span><span class="sxs-lookup"><span data-stu-id="97dad-239">MinimumSameSitePolicy</span></span> | <span data-ttu-id="97dad-240">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="97dad-240">Cookie.SameSite</span></span> | <span data-ttu-id="97dad-241">Resultierende Cookie. SameSite-Einstellung</span><span class="sxs-lookup"><span data-stu-id="97dad-241">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="97dad-242">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="97dad-242">SameSiteMode.None</span></span>     | <span data-ttu-id="97dad-243">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="97dad-243">SameSiteMode.None</span></span><br><span data-ttu-id="97dad-244">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="97dad-244">SameSiteMode.Lax</span></span><br><span data-ttu-id="97dad-245">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="97dad-245">SameSiteMode.Strict</span></span> | <span data-ttu-id="97dad-246">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="97dad-246">SameSiteMode.None</span></span><br><span data-ttu-id="97dad-247">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="97dad-247">SameSiteMode.Lax</span></span><br><span data-ttu-id="97dad-248">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="97dad-248">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="97dad-249">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="97dad-249">SameSiteMode.Lax</span></span>      | <span data-ttu-id="97dad-250">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="97dad-250">SameSiteMode.None</span></span><br><span data-ttu-id="97dad-251">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="97dad-251">SameSiteMode.Lax</span></span><br><span data-ttu-id="97dad-252">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="97dad-252">SameSiteMode.Strict</span></span> | <span data-ttu-id="97dad-253">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="97dad-253">SameSiteMode.Lax</span></span><br><span data-ttu-id="97dad-254">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="97dad-254">SameSiteMode.Lax</span></span><br><span data-ttu-id="97dad-255">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="97dad-255">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="97dad-256">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="97dad-256">SameSiteMode.Strict</span></span>   | <span data-ttu-id="97dad-257">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="97dad-257">SameSiteMode.None</span></span><br><span data-ttu-id="97dad-258">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="97dad-258">SameSiteMode.Lax</span></span><br><span data-ttu-id="97dad-259">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="97dad-259">SameSiteMode.Strict</span></span> | <span data-ttu-id="97dad-260">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="97dad-260">SameSiteMode.Strict</span></span><br><span data-ttu-id="97dad-261">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="97dad-261">SameSiteMode.Strict</span></span><br><span data-ttu-id="97dad-262">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="97dad-262">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="97dad-263">Erstellen eines Authentifizierungs Cookies</span><span class="sxs-lookup"><span data-stu-id="97dad-263">Create an authentication cookie</span></span>

<span data-ttu-id="97dad-264">Zum Erstellen eines Cookies, das Benutzerinformationen enthält, <xref:System.Security.Claims.ClaimsPrincipal>erstellen Sie eine.</span><span class="sxs-lookup"><span data-stu-id="97dad-264">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="97dad-265">Die Benutzerinformationen werden serialisiert und im Cookie gespeichert.</span><span class="sxs-lookup"><span data-stu-id="97dad-265">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="97dad-266">Erstellen Sie <xref:System.Security.Claims.ClaimsIdentity> mit allen erforderlichen <xref:System.Security.Claims.Claim>s, und <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> rufen Sie auf, um den Benutzer anzumelden:</span><span class="sxs-lookup"><span data-stu-id="97dad-266">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="97dad-267">`SignInAsync`erstellt ein verschlüsseltes Cookie und fügt es der aktuellen Antwort hinzu.</span><span class="sxs-lookup"><span data-stu-id="97dad-267">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="97dad-268">Wenn `AuthenticationScheme` nicht angegeben ist, wird das Standardschema verwendet.</span><span class="sxs-lookup"><span data-stu-id="97dad-268">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="97dad-269">ASP.net Core- [Datenschutz](xref:security/data-protection/using-data-protection) System wird für die Verschlüsselung verwendet.</span><span class="sxs-lookup"><span data-stu-id="97dad-269">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="97dad-270">Konfigurieren Sie für eine APP, die auf mehreren Computern gehostet wird, den Lastenausgleich über apps oder eine Webfarm, den [Datenschutz](xref:security/data-protection/configuration/overview) so, dass derselbe Schlüsselbund und der APP-Bezeichner verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="97dad-270">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="97dad-271">Abmelden</span><span class="sxs-lookup"><span data-stu-id="97dad-271">Sign out</span></span>

<span data-ttu-id="97dad-272">Zum Abmelden des aktuellen Benutzers und zum Löschen des Cookies wenden <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>Sie Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="97dad-272">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="97dad-273">Wenn `CookieAuthenticationDefaults.AuthenticationScheme` (oder "Cookies") nicht als Schema (z. b. "contosocookie") verwendet wird, geben Sie das Schema an, das beim Konfigurieren des Authentifizierungs Anbieters verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="97dad-273">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="97dad-274">Andernfalls wird das Standardschema verwendet.</span><span class="sxs-lookup"><span data-stu-id="97dad-274">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="97dad-275">Reagieren auf Back-End-Änderungen</span><span class="sxs-lookup"><span data-stu-id="97dad-275">React to back-end changes</span></span>

<span data-ttu-id="97dad-276">Nachdem ein Cookie erstellt wurde, ist das Cookie die einzige Quelle der Identität.</span><span class="sxs-lookup"><span data-stu-id="97dad-276">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="97dad-277">Wenn ein Benutzerkonto in Back-End-Systemen deaktiviert ist:</span><span class="sxs-lookup"><span data-stu-id="97dad-277">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="97dad-278">Das cookieauthentifizierungssystem der APP verarbeitet weiterhin Anforderungen auf der Grundlage des Authentifizierungs Cookies.</span><span class="sxs-lookup"><span data-stu-id="97dad-278">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="97dad-279">Der Benutzer bleibt bei der App angemeldet, solange das Authentifizierungs Cookie gültig ist.</span><span class="sxs-lookup"><span data-stu-id="97dad-279">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="97dad-280">Das <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> -Ereignis kann verwendet werden, um die Überprüfung der Cookie-Identität abzufangen und zu überschreiben.</span><span class="sxs-lookup"><span data-stu-id="97dad-280">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="97dad-281">Das Überprüfen des Cookies bei jeder Anforderung verringert das Risiko, dass widerrufene Benutzer auf die App zugreifen.</span><span class="sxs-lookup"><span data-stu-id="97dad-281">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="97dad-282">Ein Ansatz für die Cookievalidierung basiert darauf, wann die Benutzerdatenbank geändert wird.</span><span class="sxs-lookup"><span data-stu-id="97dad-282">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="97dad-283">Wenn die Datenbank nicht geändert wurde, weil das Cookie des Benutzers ausgestellt wurde, ist es nicht erforderlich, den Benutzer erneut zu authentifizieren, wenn das Cookie noch gültig ist.</span><span class="sxs-lookup"><span data-stu-id="97dad-283">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="97dad-284">In der Beispiel-APP wird die Datenbank in `IUserRepository` implementiert und speichert einen `LastChanged` Wert.</span><span class="sxs-lookup"><span data-stu-id="97dad-284">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="97dad-285">Wenn ein Benutzer in der Datenbank aktualisiert wird, wird `LastChanged` der Wert auf die aktuelle Zeit festgelegt.</span><span class="sxs-lookup"><span data-stu-id="97dad-285">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="97dad-286">Erstellen Sie das Cookie mit einem `LastChanged` `LastChanged` Anspruch, der den aktuellen `LastChanged` Wert aus der Datenbank enthält, um ein Cookie für ungültig zu erklären, wenn sich die Datenbank auf der Grundlage des Werts ändert:</span><span class="sxs-lookup"><span data-stu-id="97dad-286">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="97dad-287">Um eine außer Kraft setzung für `ValidatePrincipal` das-Ereignis zu implementieren, schreiben Sie eine-Methode mit der folgenden Signatur <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>in eine Klasse, die von abgeleitet ist:</span><span class="sxs-lookup"><span data-stu-id="97dad-287">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="97dad-288">Im folgenden finden Sie eine Beispiel Implementierung `CookieAuthenticationEvents`von:</span><span class="sxs-lookup"><span data-stu-id="97dad-288">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="97dad-289">Registrieren Sie die Ereignis Instanz während der Registrierung des Cookie `Startup.ConfigureServices` -Dienstanbieter in der Methode.</span><span class="sxs-lookup"><span data-stu-id="97dad-289">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="97dad-290">Stellen Sie eine Bereichs bezogene [Dienst Registrierung](xref:fundamentals/dependency-injection#service-lifetimes) für `CustomCookieAuthenticationEvents` die Klasse bereit:</span><span class="sxs-lookup"><span data-stu-id="97dad-290">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="97dad-291">Stellen Sie sich eine Situation vor, in der der Name&mdash;des Benutzers eine Entscheidung aktualisiert, die die Sicherheit in keiner Weise beeinträchtigt.</span><span class="sxs-lookup"><span data-stu-id="97dad-291">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="97dad-292">Wenn Sie den Benutzer Prinzipal nicht dedestruktiv aktualisieren möchten, müssen `context.ReplacePrincipal` Sie aufrufen und `context.ShouldRenew` die- `true`Eigenschaft auf festlegen.</span><span class="sxs-lookup"><span data-stu-id="97dad-292">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="97dad-293">Der hier beschriebene Ansatz wird bei jeder Anforderung ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="97dad-293">The approach described here is triggered on every request.</span></span> <span data-ttu-id="97dad-294">Das Überprüfen von Authentifizierungs Cookies für alle Benutzer bei jeder Anforderung kann zu einer hohen Leistungs Einbuße für die APP führen.</span><span class="sxs-lookup"><span data-stu-id="97dad-294">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="97dad-295">Persistente Cookies</span><span class="sxs-lookup"><span data-stu-id="97dad-295">Persistent cookies</span></span>

<span data-ttu-id="97dad-296">Möglicherweise möchten Sie, dass das Cookie über Browsersitzungen hinweg beibehalten wird.</span><span class="sxs-lookup"><span data-stu-id="97dad-296">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="97dad-297">Diese Persistenz sollte nur mit der expliziten Zustimmung des Benutzers mit dem Kontrollkästchen "merken" bei der Anmeldung oder einem ähnlichen Mechanismus aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="97dad-297">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="97dad-298">Der folgende Code Ausschnitt erstellt eine Identität und entsprechendes Cookie, das bei Browser Abschlüssen überlebt.</span><span class="sxs-lookup"><span data-stu-id="97dad-298">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="97dad-299">Alle zuvor konfigurierten gleitenden Ablauf Einstellungen werden berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="97dad-299">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="97dad-300">Wenn das Cookie abläuft, während der Browser geschlossen wird, löscht der Browser das Cookie, sobald es neu gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="97dad-300">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="97dad-301">Auf <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>festlegen:</span><span class="sxs-lookup"><span data-stu-id="97dad-301">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="97dad-302">Absoluter Cookieablauf</span><span class="sxs-lookup"><span data-stu-id="97dad-302">Absolute cookie expiration</span></span>

<span data-ttu-id="97dad-303">Eine absolute Ablaufzeit kann mit <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="97dad-303">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="97dad-304">Zum Erstellen eines persistenten Cookies `IsPersistent` muss ebenfalls festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="97dad-304">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="97dad-305">Andernfalls wird das Cookie mit einer Sitzungs basierten Lebensdauer erstellt und kann entweder vor oder nach dem Authentifizierungs Ticket ablaufen, das es enthält.</span><span class="sxs-lookup"><span data-stu-id="97dad-305">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="97dad-306">Wenn `ExpiresUtc` festgelegt ist, wird der Wert der- <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> Option von <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, sofern festgelegt, überschrieben.</span><span class="sxs-lookup"><span data-stu-id="97dad-306">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="97dad-307">Der folgende Code Ausschnitt erstellt eine Identität und entsprechendes Cookie, das 20 Minuten lang dauert.</span><span class="sxs-lookup"><span data-stu-id="97dad-307">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="97dad-308">Dadurch werden alle zuvor konfigurierten gleitenden Ablauf Einstellungen ignoriert.</span><span class="sxs-lookup"><span data-stu-id="97dad-308">This ignores any sliding expiration settings previously configured.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="97dad-309">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="97dad-309">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="97dad-310">Richtlinien basierte Rollen Überprüfungen</span><span class="sxs-lookup"><span data-stu-id="97dad-310">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>

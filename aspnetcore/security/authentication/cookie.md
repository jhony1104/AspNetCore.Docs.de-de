---
title: Verwenden der Cookieauthentifizierung ohne ASP.NET Core Identity
author: rick-anderson
description: Informationen Sie zum Verwenden der Cookieauthentifizierung ohne ASP.NET Core Identity.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/07/2019
uid: security/authentication/cookie
ms.openlocfilehash: bbba2e77f806e1ed30bb734763cdbaedc1471d62
ms.sourcegitcommit: 91cc1f07ef178ab709ea42f8b3a10399c970496e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/08/2019
ms.locfileid: "67622745"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a><span data-ttu-id="1e7bd-103">Verwenden der Cookieauthentifizierung ohne ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="1e7bd-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="1e7bd-104">Durch [Rick Anderson](https://twitter.com/RickAndMSFT) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1e7bd-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="1e7bd-105">ASP.NET Core Identity ist eine vollständige, voll ausgestattete Authentication-Anbieter zum Erstellen und Verwalten von Anmeldungen.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="1e7bd-106">Allerdings kann ein Authentifizierungsanbieter für cookiebasierte Authentifizierung ohne ASP.NET Core Identity verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-106">However, a cookie-based authentication authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="1e7bd-107">Weitere Informationen finden Sie unter <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="1e7bd-108">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1e7bd-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="1e7bd-109">Zu Demonstrationszwecken in der Beispiel-app ist das Benutzerkonto für den hypothetischen Benutzer Maria Rodriguez, in der app hartcodiert.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="1e7bd-110">Verwenden der **-e-Mail** Benutzernamen `maria.rodriguez@contoso.com` und eines Kennworts zum Anmelden des Benutzers.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-110">Use the **Email** user name `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="1e7bd-111">Der Benutzer wird authentifiziert, der `AuthenticateUser` -Methode in der die *Pages/Account/Login.cshtml.cs* Datei.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="1e7bd-112">Im Real-World-Beispiel würde der Benutzer für eine Datenbank authentifiziert werden.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="1e7bd-113">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="1e7bd-113">Configuration</span></span>

<span data-ttu-id="1e7bd-114">Wenn die app verwenden, nicht die [Microsoft.AspNetCore.App metapaket](xref:fundamentals/metapackage-app), erstellen Sie einen Paketverweis in die Projektdatei für die [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) Paket.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-114">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="1e7bd-115">In der `Startup.ConfigureServices` -Methode, erstellen Sie den Authentifizierungs-Middleware-Dienst, mit der <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> und <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> Methoden:</span><span class="sxs-lookup"><span data-stu-id="1e7bd-115">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="1e7bd-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> die an `AddAuthentication` legt das Authentifizierungsschema "Standard" für die app fest.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="1e7bd-117">`AuthenticationScheme` ist nützlich, wenn mehrere Instanzen der Cookie-Authentifizierung vorhanden sind, und Sie möchten [autorisieren mit einem bestimmten Schema](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="1e7bd-117">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="1e7bd-118">Festlegen der `AuthenticationScheme` zu [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) "Cookies" der Wert für das Schema enthält.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-118">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="1e7bd-119">Sie können einen beliebigen Zeichenfolgenwert angeben, der das Schema unterscheidet.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-119">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="1e7bd-120">Der app-Authentifizierungsschema unterscheidet sich von der app Cookie-Authentifizierungsschema.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-120">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="1e7bd-121">Wenn ein Cookie-Authentifizierungsschema bereitgestellt ist nicht <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, verwendet `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span><span class="sxs-lookup"><span data-stu-id="1e7bd-121">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="1e7bd-122">Des Authentifizierungscookies <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> -Eigenschaftensatz auf `true` standardmäßig.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-122">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="1e7bd-123">Authentifizierungscookies sind zulässig, wenn ein Besucher der Website, die Datensammlung zugestimmt wurde nicht.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-123">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="1e7bd-124">Weitere Informationen finden Sie unter <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-124">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="1e7bd-125">In der `Startup.Configure` -Methode, rufen die `UseAuthentication` Middleware für die Authentifizierung, die festlegt, aufzurufenden Methode der `HttpContext.User` Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-125">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="1e7bd-126">Rufen Sie die `UseAuthentication` Methode vor dem Aufruf `UseMvcWithDefaultRoute` oder `UseMvc`:</span><span class="sxs-lookup"><span data-stu-id="1e7bd-126">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="1e7bd-127">Die <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> Klasse wird verwendet, um die Optionen für die Authentifizierung-Anbieter konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-127">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="1e7bd-128">Legen Sie `CookieAuthenticationOptions` in der Dienstkonfiguration für die Authentifizierung in der `Startup.ConfigureServices` Methode:</span><span class="sxs-lookup"><span data-stu-id="1e7bd-128">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="1e7bd-129">Richtlinie Cookie-Middleware</span><span class="sxs-lookup"><span data-stu-id="1e7bd-129">Cookie Policy Middleware</span></span>

<span data-ttu-id="1e7bd-130">[Richtlinie Cookiemiddleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) Cookie Funktionen ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-130">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="1e7bd-131">Hinzufügen der Middleware zur Verarbeitungspipeline app ist die Reihenfolge, die vertrauliche&mdash;sie gilt nur für Downstreamkomponenten in der Pipeline registriert.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-131">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="1e7bd-132">Verwendung <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> bereitgestellt, um die Cookie-Richtlinie-Middleware zu globale Eigenschaften der Cookie-Verarbeitung und Hook in von Cookie-Verarbeitungshandler zu steuern, wenn Cookies angefügt oder gelöscht werden.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="1e7bd-133">Der Standardwert <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> Wert `SameSiteMode.Lax` um OAuth2-Authentifizierung zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-133">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="1e7bd-134">Genau eine SameSite-Richtlinie erzwingen `SameSiteMode.Strict`legen die `MinimumSameSitePolicy`.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-134">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="1e7bd-135">Auch wenn diese Einstellung OAuth2 und andere Cross-Origin-Authentifizierungsschemas unterbrochen wird, erhöht sie die Sicherheitsstufe Cookie für andere Typen von apps, die Verarbeitung von Cross-Origin-Anforderungen nicht benötigen.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-135">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="1e7bd-136">Die Richtlinie Cookie-Middleware-Einstellung für `MinimumSameSitePolicy` kann die Einstellung der beeinflussen `Cookie.SameSite` in `CookieAuthenticationOptions` gemäß der folgenden Matrix.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-136">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="1e7bd-137">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="1e7bd-137">MinimumSameSitePolicy</span></span> | <span data-ttu-id="1e7bd-138">Cookie.SameSite</span><span class="sxs-lookup"><span data-stu-id="1e7bd-138">Cookie.SameSite</span></span> | <span data-ttu-id="1e7bd-139">Resultierende Cookie.SameSite-Einstellung</span><span class="sxs-lookup"><span data-stu-id="1e7bd-139">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="1e7bd-140">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="1e7bd-140">SameSiteMode.None</span></span>     | <span data-ttu-id="1e7bd-141">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="1e7bd-141">SameSiteMode.None</span></span><br><span data-ttu-id="1e7bd-142">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="1e7bd-142">SameSiteMode.Lax</span></span><br><span data-ttu-id="1e7bd-143">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="1e7bd-143">SameSiteMode.Strict</span></span> | <span data-ttu-id="1e7bd-144">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="1e7bd-144">SameSiteMode.None</span></span><br><span data-ttu-id="1e7bd-145">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="1e7bd-145">SameSiteMode.Lax</span></span><br><span data-ttu-id="1e7bd-146">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="1e7bd-146">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="1e7bd-147">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="1e7bd-147">SameSiteMode.Lax</span></span>      | <span data-ttu-id="1e7bd-148">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="1e7bd-148">SameSiteMode.None</span></span><br><span data-ttu-id="1e7bd-149">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="1e7bd-149">SameSiteMode.Lax</span></span><br><span data-ttu-id="1e7bd-150">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="1e7bd-150">SameSiteMode.Strict</span></span> | <span data-ttu-id="1e7bd-151">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="1e7bd-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="1e7bd-152">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="1e7bd-152">SameSiteMode.Lax</span></span><br><span data-ttu-id="1e7bd-153">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="1e7bd-153">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="1e7bd-154">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="1e7bd-154">SameSiteMode.Strict</span></span>   | <span data-ttu-id="1e7bd-155">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="1e7bd-155">SameSiteMode.None</span></span><br><span data-ttu-id="1e7bd-156">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="1e7bd-156">SameSiteMode.Lax</span></span><br><span data-ttu-id="1e7bd-157">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="1e7bd-157">SameSiteMode.Strict</span></span> | <span data-ttu-id="1e7bd-158">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="1e7bd-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="1e7bd-159">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="1e7bd-159">SameSiteMode.Strict</span></span><br><span data-ttu-id="1e7bd-160">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="1e7bd-160">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="1e7bd-161">Erstellen Sie ein Authentifizierungscookie</span><span class="sxs-lookup"><span data-stu-id="1e7bd-161">Create an authentication cookie</span></span>

<span data-ttu-id="1e7bd-162">Um ein Cookie mit Benutzerinformationen zu erstellen, erstellen Sie eine <xref:System.Security.Claims.ClaimsPrincipal>.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-162">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="1e7bd-163">Die Benutzerinformationen serialisiert und im Cookie gespeichert.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-163">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="1e7bd-164">Erstellen Sie eine <xref:System.Security.Claims.ClaimsIdentity> mit ggf. erforderlichen <xref:System.Security.Claims.Claim>s, und rufen <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> zum Anmelden des Benutzers:</span><span class="sxs-lookup"><span data-stu-id="1e7bd-164">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="1e7bd-165">`SignInAsync` erstellt ein verschlüsseltes Cookie aus, und die aktuelle Antwort hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-165">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="1e7bd-166">Wenn `AuthenticationScheme` nicht angegeben ist, wird das Standardschema verwendet.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-166">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="1e7bd-167">ASP.NET Core [den Datenschutz](xref:security/data-protection/using-data-protection) System für die Verschlüsselung verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-167">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="1e7bd-168">Laden Sie für eine app auf mehreren Computern gehostet, Lastenausgleich zwischen verschiedenen apps oder mithilfe einer Webfarm [Schutz von Daten konfigurieren](xref:security/data-protection/configuration/overview) und mit dem Schlüsselbund für den gleichen app-Bezeichner.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-168">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="1e7bd-169">Abmelden</span><span class="sxs-lookup"><span data-stu-id="1e7bd-169">Sign out</span></span>

<span data-ttu-id="1e7bd-170">Um den aktuellen Benutzer abmelden, und ihre Cookies zu löschen, rufen Sie <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span><span class="sxs-lookup"><span data-stu-id="1e7bd-170">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="1e7bd-171">Wenn `CookieAuthenticationDefaults.AuthenticationScheme` (oder "Cookies") wird nicht verwendet werden, da das Schema (z. B. "ContosoCookie"), geben Sie das Schema, das verwendet wird, wenn Sie den Authentifizierungsanbieter konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-171">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="1e7bd-172">Andernfalls wird das Standardschema verwendet.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-172">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="1e7bd-173">Reagieren Sie auf Änderungen der Back-end</span><span class="sxs-lookup"><span data-stu-id="1e7bd-173">React to back-end changes</span></span>

<span data-ttu-id="1e7bd-174">Sobald ein Cookie erstellt wurde, ist das Cookie die einzige Quelle der Identität an.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-174">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="1e7bd-175">Wenn ein Benutzerkonto in Back-End-Systemen deaktiviert wird:</span><span class="sxs-lookup"><span data-stu-id="1e7bd-175">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="1e7bd-176">Der app Cookie-Authentifizierungssystem her, die zum Verarbeiten von Anforderungen basierend auf das Authentifizierungscookie wird fortgesetzt.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-176">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="1e7bd-177">Der Benutzer bleibt mit Vorzeichen in die app, solange das Authentifizierungscookie gültig ist.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-177">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="1e7bd-178">Die <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> -Ereignis kann zum Abfangen und überschreiben die Überprüfung der Identität ein Cookie verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-178">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="1e7bd-179">Überprüfen das Cookie bei jeder Anforderung verringert das Risiko der gesperrten Benutzer Zugriff auf die app.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-179">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="1e7bd-180">Ein Ansatz für die gültigkeitsprüfung basiert zu verfolgen, wenn die Benutzerdatenbank geändert wird.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-180">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="1e7bd-181">Wenn die Datenbank geändert wurde, nachdem der Benutzer Cookies ausgegeben wurde, ist es nicht erforderlich, die der Benutzer erneut authentifizieren, wenn ihre Cookie noch gültig ist.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-181">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="1e7bd-182">In der Beispiel-app wird in die Datenbank implementiert `IUserRepository` und speichert eine `LastChanged` Wert.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-182">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="1e7bd-183">Wenn ein Benutzer in der Datenbank aktualisiert wird die `LastChanged` Wert wird auf die aktuelle Uhrzeit festgelegt.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-183">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="1e7bd-184">Um ein Cookie für ungültig erklären, wenn die Änderungen in der Datenbank auf Grundlage der `LastChanged` Wert, erstellen Sie das Cookie mit einem `LastChanged` Anspruch mit dem aktuellen `LastChanged` Wert aus der Datenbank:</span><span class="sxs-lookup"><span data-stu-id="1e7bd-184">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="1e7bd-185">Implementieren Sie eine Außerkraftsetzung für die `ValidatePrincipal` Ereignis schreiben, eine Methode mit der folgenden Signatur in eine abgeleitete Klasse <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span><span class="sxs-lookup"><span data-stu-id="1e7bd-185">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="1e7bd-186">Im folgenden finden Sie eine beispielimplementierung von `CookieAuthenticationEvents`:</span><span class="sxs-lookup"><span data-stu-id="1e7bd-186">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="1e7bd-187">Registrieren Sie die Ereignisse-Instanz während der Cookie-Service-Registrierung in der `Startup.ConfigureServices` Methode.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-187">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="1e7bd-188">Geben Sie einen [begrenzt dienstregistrierung](xref:fundamentals/dependency-injection#service-lifetimes) für Ihre `CustomCookieAuthenticationEvents` Klasse:</span><span class="sxs-lookup"><span data-stu-id="1e7bd-188">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="1e7bd-189">Betrachten Sie eine Situation, in dem der Name des Benutzers aktualisiert&mdash;eine Entscheidung, die Sicherheit in irgendeiner Weise nicht beeinträchtigt.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-189">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="1e7bd-190">Wenn Sie den Benutzerprinzipal zerstörungsfrei aktualisieren möchten, rufen Sie `context.ReplacePrincipal` und legen Sie die `context.ShouldRenew` Eigenschaft `true`.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-190">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="1e7bd-191">Die hier beschriebene Vorgehensweise wird bei jeder Anforderung ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-191">The approach described here is triggered on every request.</span></span> <span data-ttu-id="1e7bd-192">Überprüfen des Authentifizierungscookies für alle Benutzer für jede Anforderung kann dazu führen, dass eine große Leistungseinbuße für die app.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-192">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="1e7bd-193">Beständige cookies</span><span class="sxs-lookup"><span data-stu-id="1e7bd-193">Persistent cookies</span></span>

<span data-ttu-id="1e7bd-194">Sie sollten die Cookies, das über Browsersitzungen hinweg beizubehalten.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-194">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="1e7bd-195">Diese Persistenz sollte nur mit expliziten benutzerzustimmung mit "Erinnerung" das Kontrollkästchen bei der Anmeldung oder ein ähnlicher Mechanismus aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-195">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="1e7bd-196">Der folgende Codeausschnitt erstellt eine Identität und die entsprechenden Cookie, das über den Browser Closures zurückgegriffen werden kann.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-196">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="1e7bd-197">Alle gleitenden ablaufeinstellungen, die zuvor konfiguriert haben, werden berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-197">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="1e7bd-198">Wenn das Cookie abläuft, während der Browser geschlossen wird, löscht der Browser das Cookie an, nachdem er neu gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-198">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="1e7bd-199">Legen Sie <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> zu `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span><span class="sxs-lookup"><span data-stu-id="1e7bd-199">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="1e7bd-200">Absolute cookieablauf</span><span class="sxs-lookup"><span data-stu-id="1e7bd-200">Absolute cookie expiration</span></span>

<span data-ttu-id="1e7bd-201">Eine absolute Ablaufzeit kann festgelegt werden, mit <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-201">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="1e7bd-202">Erstellen Sie ein dauerhaftes Cookie, `IsPersistent` müssen ebenfalls festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-202">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="1e7bd-203">Andernfalls wird das Cookie wird mit einer Lebensdauer sitzungsbasierte erstellt und kann ablaufen, entweder vor oder nach das Authentifizierungsticket, das es enthält.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-203">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="1e7bd-204">Wenn `ExpiresUtc` festgelegt ist, wird er überschreibt den Wert von der <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> Option <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, sofern festgelegt.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-204">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="1e7bd-205">Der folgende Codeausschnitt erstellt eine Identität und die entsprechenden Cookie, das in der Regel 20 Minuten dauert.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-205">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="1e7bd-206">Dies ignoriert alle gleitenden ablaufeinstellungen, die zuvor konfiguriert haben.</span><span class="sxs-lookup"><span data-stu-id="1e7bd-206">This ignores any sliding expiration settings previously configured.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="1e7bd-207">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="1e7bd-207">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="1e7bd-208">Überprüfen der Richtlinie der richtlinienbasierten-Funktion</span><span class="sxs-lookup"><span data-stu-id="1e7bd-208">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>

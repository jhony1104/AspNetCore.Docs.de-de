---
title: Authentifizierung mit Facebook, Google und externem Anbieter ohne ASP.net CoreIdentity
author: rick-anderson
description: Eine Erläuterung der Verwendung von Facebook, Google, Twitter und der Kontobenutzer Authentifizierung ohne ASP.net Core Identity .
ms.author: riande
ms.date: 12/10/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: ed908526604b04f9aebb93935aa3ad4719621526
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406042"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a><span data-ttu-id="1d00f-103">Verwenden Sie die Authentifizierung für den Social Sign-in-Anbieter ohne ASP.net CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="1d00f-103">Use social sign-in provider authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="1d00f-104">Von [Kirk Larkin](https://twitter.com/serpent5) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1d00f-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1d00f-105"><xref:security/authentication/social/index>Beschreibt, wie Sie es Benutzern ermöglichen können, sich mithilfe von OAuth 2,0 mit Anmelde Informationen von externen Authentifizierungs Anbietern anzumelden.</span><span class="sxs-lookup"><span data-stu-id="1d00f-105"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="1d00f-106">Die in diesem Thema beschriebene Vorgehensweise umfasst ASP.net Core Identity als Authentifizierungs Anbieter.</span><span class="sxs-lookup"><span data-stu-id="1d00f-106">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="1d00f-107">In diesem Beispiel wird veranschaulicht, wie ein externer Authentifizierungs Anbieter **ohne** ASP.net Core verwendet wird Identity .</span><span class="sxs-lookup"><span data-stu-id="1d00f-107">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="1d00f-108">Dies ist nützlich für apps, die nicht alle Features von ASP.net Core benötigen Identity , aber trotzdem eine Integration in einen vertrauenswürdigen externen Authentifizierungs Anbieter erfordern.</span><span class="sxs-lookup"><span data-stu-id="1d00f-108">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="1d00f-109">In diesem Beispiel wird die [Google-Authentifizierung](xref:security/authentication/google-logins) zum Authentifizieren von Benutzern verwendet.</span><span class="sxs-lookup"><span data-stu-id="1d00f-109">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="1d00f-110">Die Verwendung der Google-Authentifizierung verlagert viele der Komplexitäten bei der Verwaltung des Anmeldevorgangs an Google.</span><span class="sxs-lookup"><span data-stu-id="1d00f-110">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="1d00f-111">Informationen zur Integration in einen anderen externen Authentifizierungs Anbieter finden Sie in den folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="1d00f-111">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="1d00f-112">Facebook-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="1d00f-112">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="1d00f-113">Microsoft-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="1d00f-113">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="1d00f-114">Twitter-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="1d00f-114">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="1d00f-115">Andere Anbieter</span><span class="sxs-lookup"><span data-stu-id="1d00f-115">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="1d00f-116">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="1d00f-116">Configuration</span></span>

<span data-ttu-id="1d00f-117">Konfigurieren Sie in der `ConfigureServices` -Methode die Authentifizierungs Schemas der APP mit den <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> Methoden, und <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> :</span><span class="sxs-lookup"><span data-stu-id="1d00f-117">In the `ConfigureServices` method, configure the app's authentication schemes with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, and <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

<span data-ttu-id="1d00f-118">Der-Befehl ruft <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> die der APP auf <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme> .</span><span class="sxs-lookup"><span data-stu-id="1d00f-118">The call to <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> sets the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>.</span></span> <span data-ttu-id="1d00f-119">Das `DefaultScheme` ist das Standardschema, das von den folgenden `HttpContext` Authentifizierungs Erweiterungs Methoden verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="1d00f-119">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="1d00f-120">Wenn Sie die APP `DefaultScheme` auf [cookieauthenticationdefaults. authenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") festlegen, wird die APP so konfiguriert, dass Cookies als Standardschema für diese Erweiterungs Methoden verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="1d00f-120">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="1d00f-121">Wenn Sie die APP <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> auf [googledefaults. authenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") festlegen, wird die APP für die Verwendung von Google als Standardschema für Aufrufe von konfiguriert `ChallengeAsync` .</span><span class="sxs-lookup"><span data-stu-id="1d00f-121">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="1d00f-122">`DefaultChallengeScheme`überschreibt `DefaultScheme` .</span><span class="sxs-lookup"><span data-stu-id="1d00f-122">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="1d00f-123"><xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>Weitere Eigenschaften, die bei Festlegung überschreiben, finden Sie unter `DefaultScheme` .</span><span class="sxs-lookup"><span data-stu-id="1d00f-123">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="1d00f-124">`Startup.Configure`Rufen Sie in `UseAuthentication` und zwischen dem Aufruf `UseAuthorization` von und auf `UseRouting` `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="1d00f-124">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` between calling `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="1d00f-125">Dadurch wird die `HttpContext.User` -Eigenschaft festgelegt und die Autorisierungs Middleware für Anforderungen ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="1d00f-125">This sets the `HttpContext.User` property and runs the Authorization Middleware for requests:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

<span data-ttu-id="1d00f-126">Weitere Informationen zu Authentifizierungs Schemas finden Sie unter [Authentifizierungs Konzepte](xref:security/authentication/index#authentication-concepts).</span><span class="sxs-lookup"><span data-stu-id="1d00f-126">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="1d00f-127">Weitere Informationen zur Cookie-Authentifizierung finden Sie unter <xref:security/authentication/cookie> .</span><span class="sxs-lookup"><span data-stu-id="1d00f-127">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="1d00f-128">Autorisierung anwenden</span><span class="sxs-lookup"><span data-stu-id="1d00f-128">Apply authorization</span></span>

<span data-ttu-id="1d00f-129">Testen Sie die Authentifizierungs Konfiguration der APP, indem `AuthorizeAttribute` Sie das-Attribut auf einen Controller, eine Aktion oder eine Seite anwenden.</span><span class="sxs-lookup"><span data-stu-id="1d00f-129">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="1d00f-130">Der folgende Code schränkt den Zugriff auf die *Datenschutz* Seite auf authentifizierte Benutzer ein:</span><span class="sxs-lookup"><span data-stu-id="1d00f-130">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="1d00f-131">Abmelden</span><span class="sxs-lookup"><span data-stu-id="1d00f-131">Sign out</span></span>

<span data-ttu-id="1d00f-132">Zum Abmelden des aktuellen Benutzers und zum Löschen des Cookies müssen Sie [signoutasync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)aufrufen.</span><span class="sxs-lookup"><span data-stu-id="1d00f-132">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="1d00f-133">Der folgende Code fügt `Logout` der *Index* Seite einen Seiten Handler hinzu:</span><span class="sxs-lookup"><span data-stu-id="1d00f-133">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="1d00f-134">Beachten Sie, dass der-aufrufswert `SignOutAsync` kein Authentifizierungsschema angibt.</span><span class="sxs-lookup"><span data-stu-id="1d00f-134">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="1d00f-135">Die APP `DefaultScheme` von `CookieAuthenticationDefaults.AuthenticationScheme` wird als Fallback verwendet.</span><span class="sxs-lookup"><span data-stu-id="1d00f-135">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1d00f-136">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="1d00f-136">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1d00f-137"><xref:security/authentication/social/index>Beschreibt, wie Sie es Benutzern ermöglichen können, sich mithilfe von OAuth 2,0 mit Anmelde Informationen von externen Authentifizierungs Anbietern anzumelden.</span><span class="sxs-lookup"><span data-stu-id="1d00f-137"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="1d00f-138">Die in diesem Thema beschriebene Vorgehensweise umfasst ASP.net Core Identity als Authentifizierungs Anbieter.</span><span class="sxs-lookup"><span data-stu-id="1d00f-138">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="1d00f-139">In diesem Beispiel wird veranschaulicht, wie ein externer Authentifizierungs Anbieter **ohne** ASP.net Core verwendet wird Identity .</span><span class="sxs-lookup"><span data-stu-id="1d00f-139">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="1d00f-140">Dies ist nützlich für apps, die nicht alle Features von ASP.net Core benötigen Identity , aber trotzdem eine Integration in einen vertrauenswürdigen externen Authentifizierungs Anbieter erfordern.</span><span class="sxs-lookup"><span data-stu-id="1d00f-140">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="1d00f-141">In diesem Beispiel wird die [Google-Authentifizierung](xref:security/authentication/google-logins) zum Authentifizieren von Benutzern verwendet.</span><span class="sxs-lookup"><span data-stu-id="1d00f-141">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="1d00f-142">Die Verwendung der Google-Authentifizierung verlagert viele der Komplexitäten bei der Verwaltung des Anmeldevorgangs an Google.</span><span class="sxs-lookup"><span data-stu-id="1d00f-142">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="1d00f-143">Informationen zur Integration in einen anderen externen Authentifizierungs Anbieter finden Sie in den folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="1d00f-143">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="1d00f-144">Facebook-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="1d00f-144">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="1d00f-145">Microsoft-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="1d00f-145">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="1d00f-146">Twitter-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="1d00f-146">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="1d00f-147">Andere Anbieter</span><span class="sxs-lookup"><span data-stu-id="1d00f-147">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="1d00f-148">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="1d00f-148">Configuration</span></span>

<span data-ttu-id="1d00f-149">Konfigurieren Sie in der `ConfigureServices` -Methode die Authentifizierungs Schemas der APP mit den `AddAuthentication` `AddCookie` Methoden, und `AddGoogle` :</span><span class="sxs-lookup"><span data-stu-id="1d00f-149">In the `ConfigureServices` method, configure the app's authentication schemes with the `AddAuthentication`, `AddCookie`, and `AddGoogle` methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

<span data-ttu-id="1d00f-150">Durch den [addauthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) -Rückruf wird das [defaultscheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)der APP festgelegt.</span><span class="sxs-lookup"><span data-stu-id="1d00f-150">The call to [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) sets the app's [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme).</span></span> <span data-ttu-id="1d00f-151">Das `DefaultScheme` ist das Standardschema, das von den folgenden `HttpContext` Authentifizierungs Erweiterungs Methoden verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="1d00f-151">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="1d00f-152">Wenn Sie die APP `DefaultScheme` auf [cookieauthenticationdefaults. authenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") festlegen, wird die APP so konfiguriert, dass Cookies als Standardschema für diese Erweiterungs Methoden verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="1d00f-152">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="1d00f-153">Wenn Sie die APP <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> auf [googledefaults. authenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") festlegen, wird die APP für die Verwendung von Google als Standardschema für Aufrufe von konfiguriert `ChallengeAsync` .</span><span class="sxs-lookup"><span data-stu-id="1d00f-153">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="1d00f-154">`DefaultChallengeScheme`überschreibt `DefaultScheme` .</span><span class="sxs-lookup"><span data-stu-id="1d00f-154">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="1d00f-155"><xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>Weitere Eigenschaften, die bei Festlegung überschreiben, finden Sie unter `DefaultScheme` .</span><span class="sxs-lookup"><span data-stu-id="1d00f-155">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="1d00f-156">Rufen Sie in der- `Configure` Methode die- `UseAuthentication` Methode auf, um die Authentifizierungs Middleware aufzurufen, die die- `HttpContext.User` Eigenschaft festlegt.</span><span class="sxs-lookup"><span data-stu-id="1d00f-156">In the `Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="1d00f-157">Rufen Sie die-Methode auf, `UseAuthentication` bevor Sie `UseMvcWithDefaultRoute` oder aufrufen `UseMvc` :</span><span class="sxs-lookup"><span data-stu-id="1d00f-157">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

<span data-ttu-id="1d00f-158">Weitere Informationen zu Authentifizierungs Schemas finden Sie unter [Authentifizierungs Konzepte](xref:security/authentication/index#authentication-concepts).</span><span class="sxs-lookup"><span data-stu-id="1d00f-158">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="1d00f-159">Weitere Informationen zur Cookie-Authentifizierung finden Sie unter <xref:security/authentication/cookie> .</span><span class="sxs-lookup"><span data-stu-id="1d00f-159">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="1d00f-160">Autorisierung anwenden</span><span class="sxs-lookup"><span data-stu-id="1d00f-160">Apply authorization</span></span>

<span data-ttu-id="1d00f-161">Testen Sie die Authentifizierungs Konfiguration der APP, indem `AuthorizeAttribute` Sie das-Attribut auf einen Controller, eine Aktion oder eine Seite anwenden.</span><span class="sxs-lookup"><span data-stu-id="1d00f-161">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="1d00f-162">Der folgende Code schränkt den Zugriff auf die *Datenschutz* Seite auf authentifizierte Benutzer ein:</span><span class="sxs-lookup"><span data-stu-id="1d00f-162">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="1d00f-163">Abmelden</span><span class="sxs-lookup"><span data-stu-id="1d00f-163">Sign out</span></span>

<span data-ttu-id="1d00f-164">Zum Abmelden des aktuellen Benutzers und zum Löschen des Cookies müssen Sie [signoutasync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)aufrufen.</span><span class="sxs-lookup"><span data-stu-id="1d00f-164">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="1d00f-165">Der folgende Code fügt `Logout` der *Index* Seite einen Seiten Handler hinzu:</span><span class="sxs-lookup"><span data-stu-id="1d00f-165">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="1d00f-166">Beachten Sie, dass der-aufrufswert `SignOutAsync` kein Authentifizierungsschema angibt.</span><span class="sxs-lookup"><span data-stu-id="1d00f-166">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="1d00f-167">Die APP `DefaultScheme` von `CookieAuthenticationDefaults.AuthenticationScheme` wird als Fallback verwendet.</span><span class="sxs-lookup"><span data-stu-id="1d00f-167">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1d00f-168">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="1d00f-168">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end

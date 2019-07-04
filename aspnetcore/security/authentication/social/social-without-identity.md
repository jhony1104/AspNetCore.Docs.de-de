---
title: Facebook, Google und externe Anbieter-Authentifizierung ohne ASP.NET Core Identity
author: rick-anderson
description: Eine Erläuterung der Verwendung von Facebook, Google, Twitter, Benutzer usw.-Kontoauthentifizierung ohne ASP.NET Core Identity.
ms.author: riande
ms.date: 07/04/2019
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: 1e7124e8b07c0faf2d005ec3ef55c0414a697d64
ms.sourcegitcommit: f6e6730872a7d6f039f97d1df762f0d0bd5e34cf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561567"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a><span data-ttu-id="bd918-103">Verwenden Sie die Anmeldung Anbieter sozialer Authentifizierung ohne ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="bd918-103">Use social sign-in provider authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="bd918-104"><xref:security/authentication/social/index> Beschreibt, wie Benutzer sich mithilfe von OAuth 2.0 mit den Anmeldeinformationen von externen Authentifizierungsanbietern anmelden zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="bd918-104"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="bd918-105">In diesem Thema beschriebene Ansatz umfasst die ASP.NET Core Identity als Authentifizierungsanbieter.</span><span class="sxs-lookup"><span data-stu-id="bd918-105">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="bd918-106">Dieses Beispiel veranschaulicht, wie einen externer Authentifizierungsanbieter **ohne** ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="bd918-106">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="bd918-107">Dies ist nützlich für apps, die nicht alle Funktionen von ASP.NET Core Identity erfordern, jedoch benötigen Sie bei der Integration mit einem vertrauenswürdigen externen Authentifizierungsanbieter.</span><span class="sxs-lookup"><span data-stu-id="bd918-107">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="bd918-108">In diesem Beispiel wird [Google-Authentifizierung](xref:security/authentication/google-logins) zum Authentifizieren von Benutzern.</span><span class="sxs-lookup"><span data-stu-id="bd918-108">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="bd918-109">Mithilfe von Google verlagert Authentifizierung einen Großteil der Komplexität der Verwaltung der Anmeldevorgang an Google.</span><span class="sxs-lookup"><span data-stu-id="bd918-109">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="bd918-110">Um mit einer anderen externen Authentifizierungsanbieter zu integrieren, finden Sie in den folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="bd918-110">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="bd918-111">Facebook-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="bd918-111">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="bd918-112">Microsoft-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="bd918-112">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="bd918-113">Twitter-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="bd918-113">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="bd918-114">Andere Anbieter</span><span class="sxs-lookup"><span data-stu-id="bd918-114">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="bd918-115">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="bd918-115">Configuration</span></span>

<span data-ttu-id="bd918-116">In der `ConfigureServices` -Methode, konfigurieren Sie die app Authentifizierungsschemas mit dem `AddAuthentication`, `AddCookie` und `AddGoogle` Methoden:</span><span class="sxs-lookup"><span data-stu-id="bd918-116">In the `ConfigureServices` method, configure the app's authentication schemes with the `AddAuthentication`, `AddCookie` and `AddGoogle` methods:</span></span>

[!code-csharp[](social-without-identity/sample/Startup.cs?name=snippet1)]

<span data-ttu-id="bd918-117">Der Aufruf von [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) für die App die [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme).</span><span class="sxs-lookup"><span data-stu-id="bd918-117">The call to [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) sets the app's [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme).</span></span> <span data-ttu-id="bd918-118">Die `DefaultScheme` ist das Standardschema verwendet wird, durch die folgenden `HttpContext` Erweiterungsmethoden für die Authentifizierung:</span><span class="sxs-lookup"><span data-stu-id="bd918-118">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="bd918-119">Festlegen der app `DefaultScheme` zu [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") konfiguriert die app aus, um Cookies als das Standardschema für diese besonderen Erweiterungsmethoden zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="bd918-119">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="bd918-120">Festlegen der app <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> zu [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") konfiguriert die app zur Verwendung von Google als das Standardschema für Aufrufe von `ChallengeAsync`.</span><span class="sxs-lookup"><span data-stu-id="bd918-120">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="bd918-121">`DefaultChallengeScheme` überschreibt `DefaultScheme`.</span><span class="sxs-lookup"><span data-stu-id="bd918-121">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="bd918-122">Finden Sie unter <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> für zusätzliche Eigenschaften, die außer Kraft setzen `DefaultScheme` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="bd918-122">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="bd918-123">In der `Configure` -Methode, rufen die `UseAuthentication` Middleware für die Authentifizierung, die festlegt, aufzurufenden Methode der `HttpContext.User` Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="bd918-123">In the `Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="bd918-124">Rufen Sie die `UseAuthentication` Methode vor dem Aufruf `UseMvcWithDefaultRoute` oder `UseMvc`:</span><span class="sxs-lookup"><span data-stu-id="bd918-124">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](social-without-identity/sample/Startup.cs?name=snippet2)]

<span data-ttu-id="bd918-125">Weitere Informationen zu Authentifizierungsschemas und Cookie-Authentifizierung finden Sie unter <xref:security/authentication/cookie>.</span><span class="sxs-lookup"><span data-stu-id="bd918-125">To learn more about authentication schemes and cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="applying-authorization"></a><span data-ttu-id="bd918-126">Anwenden der Autorisierung</span><span class="sxs-lookup"><span data-stu-id="bd918-126">Applying authorization</span></span>

<span data-ttu-id="bd918-127">Testen Sie die Authentifizierungskonfiguration der app, die durch Anwenden der `AuthorizeAttribute` -Attribut auf einen Controller, Aktion oder Seite.</span><span class="sxs-lookup"><span data-stu-id="bd918-127">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="bd918-128">Der folgende Code beschränkt den Zugriff auf die *Datenschutz* Seite, um Benutzer, die authentifiziert wurden:</span><span class="sxs-lookup"><span data-stu-id="bd918-128">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/sample/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="bd918-129">Abmelden</span><span class="sxs-lookup"><span data-stu-id="bd918-129">Sign out</span></span>

<span data-ttu-id="bd918-130">Um den aktuellen Benutzer abmelden, und ihre Cookies zu löschen, rufen Sie [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signoutasync?view=aspnetcore-2.0).</span><span class="sxs-lookup"><span data-stu-id="bd918-130">To sign out the current user and delete their cookie, call [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signoutasync?view=aspnetcore-2.0).</span></span> <span data-ttu-id="bd918-131">Der folgende Code Fügt eine `Logout` Seitenhandler, der die *Index* Seite:</span><span class="sxs-lookup"><span data-stu-id="bd918-131">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/sample/Pages/Index.cshtml.cs?name=snippet&highlight=7-11)]

<span data-ttu-id="bd918-132">Beachten Sie, dass der Aufruf von `SignOutAsync` gibt nicht an ein anderes Authentifizierungsschema.</span><span class="sxs-lookup"><span data-stu-id="bd918-132">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="bd918-133">Der app `DefaultScheme` von `CookieAuthenticationDefaults.AuthenticationScheme` als ein Fallback verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="bd918-133">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bd918-134">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="bd918-134">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

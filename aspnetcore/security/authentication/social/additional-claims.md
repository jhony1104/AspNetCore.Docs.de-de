---
title: Beibehalten von zusätzliche Ansprüche und Token von externen Anbietern in ASP.NET Core
author: guardrex
description: Erfahren Sie, wie zusätzliche Ansprüche und Token von externen Anbietern herzustellen.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2018
uid: security/authentication/social/additional-claims
ms.openlocfilehash: 37c7a51217576669bcaed79d4a212e6412aa8945
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897667"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="d0896-103">Beibehalten von zusätzliche Ansprüche und Token von externen Anbietern in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d0896-103">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

<span data-ttu-id="d0896-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d0896-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d0896-105">ASP.NET Core-Apps kann zusätzliche Ansprüche und Token von externen Authentifizierungsanbietern, z. B. Facebook, Google, Microsoft und Twitter herstellen.</span><span class="sxs-lookup"><span data-stu-id="d0896-105">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="d0896-106">Jeder Anbieter werden andere Informationen zu Benutzern auf der Plattform, aber die Muster zum Empfangen und Transformieren von Daten des Benutzers in zusätzliche Ansprüche ist das gleiche.</span><span class="sxs-lookup"><span data-stu-id="d0896-106">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="d0896-107">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d0896-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d0896-108">Vorraussetzungen</span><span class="sxs-lookup"><span data-stu-id="d0896-108">Prerequisites</span></span>

<span data-ttu-id="d0896-109">Entscheiden Sie, welche externen Authentifizierungsanbietern, um in der app zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="d0896-109">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="d0896-110">Für jeden Anbieter registrieren der app, und erhalten eine Client-ID und den geheimen Clientschlüssel.</span><span class="sxs-lookup"><span data-stu-id="d0896-110">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="d0896-111">Weitere Informationen finden Sie unter <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="d0896-111">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="d0896-112">Die [Beispiel-app](#sample-app-instructions) verwendet die [Google-Authentifizierungsanbieter](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="d0896-112">The [sample app](#sample-app-instructions) uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="d0896-113">Legen Sie die Client-ID und geheimer Clientschlüssel</span><span class="sxs-lookup"><span data-stu-id="d0896-113">Set the client ID and client secret</span></span>

<span data-ttu-id="d0896-114">Der OAuth-Authentifizierungsanbieter richtet eine Vertrauensstellung mit einer app mithilfe einer Client-ID und den geheimen Clientschlüssel.</span><span class="sxs-lookup"><span data-stu-id="d0896-114">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="d0896-115">Client-ID und des geheimen clientschlüssels werden erstellt, für die app durch den externen Authentifizierungsanbieter. wenn die app mit dem Anbieter registriert ist.</span><span class="sxs-lookup"><span data-stu-id="d0896-115">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="d0896-116">Jeder externe Anbieter von der app verwendeten muss unabhängig voneinander mit Client-ID und clientgeheimnis des Anbieters konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="d0896-116">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="d0896-117">Weitere Informationen finden Sie unter den Themen der externe Authentifizierung-Anbieter, die für Ihr Szenario gelten:</span><span class="sxs-lookup"><span data-stu-id="d0896-117">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="d0896-118">Facebook-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="d0896-118">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="d0896-119">Google-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="d0896-119">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="d0896-120">Microsoft-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="d0896-120">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="d0896-121">Twitter-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="d0896-121">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="d0896-122">Andere Authentifizierungsanbieter</span><span class="sxs-lookup"><span data-stu-id="d0896-122">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="d0896-123">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="d0896-123">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="d0896-124">Die Beispiel-app wird der Google-Authentifizierungsanbieter konfiguriert, mit einer Client-ID und clientgeheimnis, die von Google bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="d0896-124">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,6)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="d0896-125">Den Authentifizierungsbereich einrichten</span><span class="sxs-lookup"><span data-stu-id="d0896-125">Establish the authentication scope</span></span>

<span data-ttu-id="d0896-126">Geben Sie die Liste der Berechtigungen zum Abrufen des Anbieters durch Angabe der <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span><span class="sxs-lookup"><span data-stu-id="d0896-126">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="d0896-127">Authentifizierung-Bereiche für allgemeine externe Anbieter werden in der folgenden Tabelle angezeigt.</span><span class="sxs-lookup"><span data-stu-id="d0896-127">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="d0896-128">Anbieter</span><span class="sxs-lookup"><span data-stu-id="d0896-128">Provider</span></span>  | <span data-ttu-id="d0896-129">Bereich</span><span class="sxs-lookup"><span data-stu-id="d0896-129">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="d0896-130">Facebook</span><span class="sxs-lookup"><span data-stu-id="d0896-130">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="d0896-131">Google</span><span class="sxs-lookup"><span data-stu-id="d0896-131">Google</span></span>    | `https://www.googleapis.com/auth/plus.login`                     |
| <span data-ttu-id="d0896-132">Microsoft</span><span class="sxs-lookup"><span data-stu-id="d0896-132">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="d0896-133">Twitter</span><span class="sxs-lookup"><span data-stu-id="d0896-133">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="d0896-134">Die Beispiel-app fügt Google `plus.login` Bereich Google +-Anmeldung Berechtigungen anfordern:</span><span class="sxs-lookup"><span data-stu-id="d0896-134">The sample app adds the Google `plus.login` scope to request Google+ sign in permissions:</span></span>

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=7)]

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="d0896-135">Ordnen Sie die Schlüssel für den Benutzer Daten und Erstellen von Ansprüchen</span><span class="sxs-lookup"><span data-stu-id="d0896-135">Map user data keys and create claims</span></span>

<span data-ttu-id="d0896-136">Geben Sie in den Optionen des Anbieters ein <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> für jeden Schlüssel in der externe Anbieter JSON-Eingabe der Benutzerdaten für den app-Identität bei der Anmeldung zu lesen.</span><span class="sxs-lookup"><span data-stu-id="d0896-136">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> for each key in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="d0896-137">Weitere Informationen zu den Anspruchstypen, finden Sie unter <xref:System.Security.Claims.ClaimTypes>.</span><span class="sxs-lookup"><span data-stu-id="d0896-137">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="d0896-138">Die Beispiel-app erstellt ein <xref:System.Security.Claims.ClaimTypes.Gender> Anspruch von der `gender` -Schlüssels im Google-Benutzerdaten:</span><span class="sxs-lookup"><span data-stu-id="d0896-138">The sample app creates a <xref:System.Security.Claims.ClaimTypes.Gender> claim from the `gender` key in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=8)]

<span data-ttu-id="d0896-139">In <xref:Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync*>, <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) wird in die app signiert <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span><span class="sxs-lookup"><span data-stu-id="d0896-139">In <xref:Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync*>, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="d0896-140">Während des Anmeldeprozesses die <xref:Microsoft.AspNetCore.Identity.UserManager%601> speichern kann ein `ApplicationUser` -Anspruch für verfügbaren Benutzerdaten aus der <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span><span class="sxs-lookup"><span data-stu-id="d0896-140">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claim for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="d0896-141">In der Beispiel-app `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) Richtet eine <xref:System.Security.Claims.ClaimTypes.Gender> -Anspruch für den angemeldeten in `ApplicationUser`:</span><span class="sxs-lookup"><span data-stu-id="d0896-141">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes a <xref:System.Security.Claims.ClaimTypes.Gender> claim for the signed in `ApplicationUser`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=30-31)]

## <a name="save-the-access-token"></a><span data-ttu-id="d0896-142">Speichern Sie das Zugriffstoken</span><span class="sxs-lookup"><span data-stu-id="d0896-142">Save the access token</span></span>

<span data-ttu-id="d0896-143"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> definiert, ob die Zugriffs-und Aktualisierungstoken gespeichert werden sollen, in der <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> nach einer erfolgreichen Autorisierung.</span><span class="sxs-lookup"><span data-stu-id="d0896-143"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="d0896-144">`SaveTokens` nastaven NA hodnotu `false` werden standardmäßig die Größe des endgültigen Authentifizierungscookies zu reduzieren.</span><span class="sxs-lookup"><span data-stu-id="d0896-144">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="d0896-145">Die Beispiel-app wird der Wert der `SaveTokens` zu `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span><span class="sxs-lookup"><span data-stu-id="d0896-145">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=9)]

<span data-ttu-id="d0896-146">Wenn `OnPostConfirmationAsync` ausgeführt wird, speichern Sie das Zugriffstoken ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) vom externen Anbieter in der `ApplicationUser`des `AuthenticationProperties`.</span><span class="sxs-lookup"><span data-stu-id="d0896-146">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="d0896-147">Die Beispiel-app speichert das Zugriffstoken in:</span><span class="sxs-lookup"><span data-stu-id="d0896-147">The sample app saves the access token in:</span></span>

* <span data-ttu-id="d0896-148">`OnPostConfirmationAsync` &ndash; Führt für die Registrierung neuer Benutzer.</span><span class="sxs-lookup"><span data-stu-id="d0896-148">`OnPostConfirmationAsync` &ndash; Executes for new user registration.</span></span>
* <span data-ttu-id="d0896-149">`OnGetCallbackAsync` &ndash; Ausgeführt wird, wenn ein zuvor registrierter Benutzer bei der app anmeldet.</span><span class="sxs-lookup"><span data-stu-id="d0896-149">`OnGetCallbackAsync` &ndash; Executes when a previously registered user signs into the app.</span></span>

<span data-ttu-id="d0896-150">*Account/ExternalLogin.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="d0896-150">*Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=34-35)]

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnGetCallbackAsync&highlight=31-32)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="d0896-151">So fügen Sie zusätzliche benutzerdefinierte Token hinzu:</span><span class="sxs-lookup"><span data-stu-id="d0896-151">How to add additional custom tokens</span></span>

<span data-ttu-id="d0896-152">Veranschaulicht, wie ein benutzerdefiniertes Token an, hinzufügen, die als Teil der gespeichert wird `SaveTokens`, fügt die Beispiel-app eine <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> mit dem aktuellen <xref:System.DateTime> für eine [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) von `TicketCreated`:</span><span class="sxs-lookup"><span data-stu-id="d0896-152">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=10-21)]

## <a name="sample-app-instructions"></a><span data-ttu-id="d0896-153">Beispiel-app-Anweisungen</span><span class="sxs-lookup"><span data-stu-id="d0896-153">Sample app instructions</span></span>

<span data-ttu-id="d0896-154">Die Beispiel-app veranschaulicht, wie Sie:</span><span class="sxs-lookup"><span data-stu-id="d0896-154">The sample app demonstrates how to:</span></span>

* <span data-ttu-id="d0896-155">Rufen Sie das Geschlecht des Benutzers von Google und speichern Sie einen Geschlecht-Anspruch mit dem Wert.</span><span class="sxs-lookup"><span data-stu-id="d0896-155">Obtain the user's gender from Google and store a gender claim with the value.</span></span>
* <span data-ttu-id="d0896-156">Das Google-Zugriffstoken des Benutzers Store `AuthenticationProperties`.</span><span class="sxs-lookup"><span data-stu-id="d0896-156">Store the Google access token in the user's `AuthenticationProperties`.</span></span>

<span data-ttu-id="d0896-157">So verwenden Sie die Beispiel-app</span><span class="sxs-lookup"><span data-stu-id="d0896-157">To use the sample app:</span></span>

1. <span data-ttu-id="d0896-158">Registrieren der app, und erhalten Sie eine gültige Client-ID und geheimer Clientschlüssel für die Google-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="d0896-158">Register the app and obtain a valid client ID and client secret for Google authentication.</span></span> <span data-ttu-id="d0896-159">Weitere Informationen finden Sie unter <xref:security/authentication/google-logins>.</span><span class="sxs-lookup"><span data-stu-id="d0896-159">For more information, see <xref:security/authentication/google-logins>.</span></span>
1. <span data-ttu-id="d0896-160">Geben Sie den Client-ID und geheimer Clientschlüssel der App in der <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> von `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d0896-160">Provide the client ID and client secret to the app in the <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> of `Startup.ConfigureServices`.</span></span>
1. <span data-ttu-id="d0896-161">Führen Sie die app aus, und fordern Sie die Seite Meine Ansprüche.</span><span class="sxs-lookup"><span data-stu-id="d0896-161">Run the app and request the My Claims page.</span></span> <span data-ttu-id="d0896-162">Wenn der Benutzer angemeldet ist nicht, leitet die app an Google.</span><span class="sxs-lookup"><span data-stu-id="d0896-162">When the user isn't signed in, the app redirects to Google.</span></span> <span data-ttu-id="d0896-163">Melden Sie sich mit Google.</span><span class="sxs-lookup"><span data-stu-id="d0896-163">Sign in with Google.</span></span> <span data-ttu-id="d0896-164">Google leitet den Benutzer zurück an die app (`/Home/MyClaims`).</span><span class="sxs-lookup"><span data-stu-id="d0896-164">Google redirects the user back to the app (`/Home/MyClaims`).</span></span> <span data-ttu-id="d0896-165">Der Benutzer wird authentifiziert, und Laden der Seite Meine Ansprüche.</span><span class="sxs-lookup"><span data-stu-id="d0896-165">The user is authenticated, and the My Claims page is loaded.</span></span> <span data-ttu-id="d0896-166">Der Gender-Anspruch ist unter **Benutzeransprüche** mit dem Wert von Google abgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="d0896-166">The gender claim is present under **User Claims** with the value obtained from Google.</span></span> <span data-ttu-id="d0896-167">Das Zugriffstoken wird in der **Authentifizierungseigenschaften**.</span><span class="sxs-lookup"><span data-stu-id="d0896-167">The access token appears in the **Authentication Properties**.</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    b36a7b09-9135-4810-b7a5-78697ff23e99
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    username@gmail.com
AspNet.Identity.SecurityStamp
    29G2TB881ATCUQFJSRFG1S0QJ0OOAWVT
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/gender
    female
http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod
    Google

Authentication Properties

.Token.access_token
    bv42.Dgw...GQMv9ArLPs
.Token.token_type
    Bearer
.Token.expires_at
    2018-08-27T19:08:00.0000000+00:00
.Token.TicketCreated
    8/27/2018 6:08:00 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.issued
    Mon, 27 Aug 2018 18:08:05 GMT
.expires
    Mon, 10 Sep 2018 18:08:05 GMT
```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

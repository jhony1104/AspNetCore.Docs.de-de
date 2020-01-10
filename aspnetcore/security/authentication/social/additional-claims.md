---
title: Persistente weitere Ansprüche und Token von externen Anbietern in ASP.net Core
author: guardrex
description: Erfahren Sie, wie Sie zusätzliche Ansprüche und Token von externen Anbietern einrichten.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: security/authentication/social/additional-claims
ms.openlocfilehash: 44b3e72085e6265319b53b548f7f7ddde2adbd14
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828580"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="cf7d6-103">Persistente weitere Ansprüche und Token von externen Anbietern in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="cf7d6-103">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

<span data-ttu-id="cf7d6-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="cf7d6-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cf7d6-105">Eine ASP.net Core-App kann zusätzliche Ansprüche und Token von externen Authentifizierungs Anbietern (z. b. Facebook, Google, Microsoft und Twitter) einrichten.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-105">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="cf7d6-106">Jeder Anbieter zeigt verschiedene Informationen über Benutzer auf seiner Plattform an, aber das Muster für das empfangen und Transformieren von Benutzerdaten in zusätzliche Ansprüche ist identisch.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-106">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="cf7d6-107">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cf7d6-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="cf7d6-108">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="cf7d6-108">Prerequisites</span></span>

<span data-ttu-id="cf7d6-109">Entscheiden Sie, welche externen Authentifizierungs Anbieter in der App unterstützt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-109">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="cf7d6-110">Registrieren Sie die APP für jeden Anbieter, und erhalten Sie eine Client-ID und einen geheimen Client Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-110">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="cf7d6-111">Weitere Informationen finden Sie unter <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-111">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="cf7d6-112">Die Beispiel-App verwendet den [Google-Authentifizierungs Anbieter](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="cf7d6-112">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="cf7d6-113">Festlegen der Client-ID und des geheimen Client Schlüssels</span><span class="sxs-lookup"><span data-stu-id="cf7d6-113">Set the client ID and client secret</span></span>

<span data-ttu-id="cf7d6-114">Der OAuth-Authentifizierungs Anbieter richtet eine Vertrauensstellung mit einer App mithilfe einer Client-ID und eines geheimen Client Schlüssels ein.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-114">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="cf7d6-115">Die Werte für die Client-ID und den geheimen Client Schlüssel werden vom externen Authentifizierungs Anbieter für die App erstellt, wenn die APP beim Anbieter registriert ist.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-115">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="cf7d6-116">Jeder von der APP verwendete externe Anbieter muss unabhängig von der Client-ID des Anbieters und dem geheimen Client Schlüssel konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-116">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="cf7d6-117">Weitere Informationen finden Sie in den Themen zu externen Authentifizierungs Anbietern, die für Ihr Szenario gelten:</span><span class="sxs-lookup"><span data-stu-id="cf7d6-117">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="cf7d6-118">Facebook-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="cf7d6-118">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="cf7d6-119">Google-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="cf7d6-119">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="cf7d6-120">Microsoft-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="cf7d6-120">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="cf7d6-121">Twitter-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="cf7d6-121">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="cf7d6-122">Andere Authentifizierungsanbieter</span><span class="sxs-lookup"><span data-stu-id="cf7d6-122">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="cf7d6-123">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="cf7d6-123">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="cf7d6-124">Die Beispiel-App konfiguriert den Google-Authentifizierungs Anbieter mit einer Client-ID und einem geheimen Client Schlüssel, die von Google bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="cf7d6-124">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="cf7d6-125">Einrichten des Authentifizierungs Bereichs</span><span class="sxs-lookup"><span data-stu-id="cf7d6-125">Establish the authentication scope</span></span>

<span data-ttu-id="cf7d6-126">Geben Sie die Liste der Berechtigungen an, die vom Anbieter abgerufen werden sollen, indem Sie die <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>angeben.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-126">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="cf7d6-127">Authentifizierungs Bereiche für allgemeine externe Anbieter werden in der folgenden Tabelle angezeigt.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-127">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="cf7d6-128">anbieter</span><span class="sxs-lookup"><span data-stu-id="cf7d6-128">Provider</span></span>  | <span data-ttu-id="cf7d6-129">Gültigkeitsbereich</span><span class="sxs-lookup"><span data-stu-id="cf7d6-129">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="cf7d6-130">Facebook</span><span class="sxs-lookup"><span data-stu-id="cf7d6-130">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="cf7d6-131">Google</span><span class="sxs-lookup"><span data-stu-id="cf7d6-131">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="cf7d6-132">Microsoft</span><span class="sxs-lookup"><span data-stu-id="cf7d6-132">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="cf7d6-133">Twitter</span><span class="sxs-lookup"><span data-stu-id="cf7d6-133">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="cf7d6-134">In der Beispiel-APP wird der `userinfo.profile` Bereich von Google automatisch durch das Framework hinzugefügt, wenn <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> auf dem <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-134">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="cf7d6-135">Wenn die APP zusätzliche Bereiche benötigt, fügen Sie Sie den Optionen hinzu.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-135">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="cf7d6-136">Im folgenden Beispiel wird der Google `https://www.googleapis.com/auth/user.birthday.read`-Bereich hinzugefügt, um den Geburtstag eines Benutzers abzurufen:</span><span class="sxs-lookup"><span data-stu-id="cf7d6-136">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="cf7d6-137">Zuordnen von Benutzerdaten Schlüsseln und Erstellen von Ansprüchen</span><span class="sxs-lookup"><span data-stu-id="cf7d6-137">Map user data keys and create claims</span></span>

<span data-ttu-id="cf7d6-138">Geben Sie in den Optionen des Anbieters für jeden Schlüssel/Unterschlüssel in den JSON-Benutzerdaten des externen Anbieters eine <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> oder <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> an, damit die APP-Identität bei der Anmeldung gelesen werden kann.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-138">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="cf7d6-139">Weitere Informationen zu Anspruchs Typen finden Sie unter <xref:System.Security.Claims.ClaimTypes>.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-139">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="cf7d6-140">Die Beispiel-App erstellt die Ansprüche "locale (`urn:google:locale`)" und "Image" (`urn:google:picture`) aus den `locale` und `picture` Schlüsseln in Google User Data:</span><span class="sxs-lookup"><span data-stu-id="cf7d6-140">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="cf7d6-141">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`wird eine <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) mit <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>bei der App angemeldet.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-141">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="cf7d6-142">Während des Anmeldevorgangs können die <xref:Microsoft.AspNetCore.Identity.UserManager%601> eine `ApplicationUser` Ansprüche für Benutzerdaten speichern, die über die <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-142">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="cf7d6-143">In der Beispiel-App richtet `OnPostConfirmationAsync` (*Account/externzuweisung. cshtml. cs*) die Gebiets Schema-(`urn:google:locale`) und Bild (`urn:google:picture`)-Ansprüche für die signierte `ApplicationUser`ein, einschließlich eines Anspruchs für <xref:System.Security.Claims.ClaimTypes.GivenName>:</span><span class="sxs-lookup"><span data-stu-id="cf7d6-143">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="cf7d6-144">Standardmäßig werden die Ansprüche eines Benutzers im Authentifizierungs Cookie gespeichert.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-144">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="cf7d6-145">Wenn das Authentifizierungs Cookie zu groß ist, kann dies dazu führen, dass die APP fehlschlägt:</span><span class="sxs-lookup"><span data-stu-id="cf7d6-145">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="cf7d6-146">Der Browser erkennt, dass der Cookie-Header zu lang ist.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-146">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="cf7d6-147">Die Gesamtgröße der Anforderung ist zu groß.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-147">The overall size of the request is too large.</span></span>

<span data-ttu-id="cf7d6-148">Wenn eine große Menge an Benutzerdaten für die Verarbeitung von Benutzer Anforderungen erforderlich ist:</span><span class="sxs-lookup"><span data-stu-id="cf7d6-148">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="cf7d6-149">Beschränken Sie die Anzahl und die Größe von Benutzer Ansprüchen für die Anforderungs Verarbeitung auf das, was die APP benötigt.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-149">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="cf7d6-150">Verwenden Sie eine benutzerdefinierte <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> für die <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> der cookeauthentifizierungs-Middleware, um die Identitäts übergreifende Identität zu speichern.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-150">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="cf7d6-151">Bewahren Sie große Mengen an Identitätsinformationen auf dem Server auf, und senden Sie nur einen kleinen Sitzungs-ID-Schlüssel an den Client.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-151">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="cf7d6-152">Speichern des Zugriffs Tokens</span><span class="sxs-lookup"><span data-stu-id="cf7d6-152">Save the access token</span></span>

<span data-ttu-id="cf7d6-153"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> definiert, ob Zugriffs-und Aktualisierungs Token nach einer erfolgreichen Autorisierung in der <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> gespeichert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-153"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="cf7d6-154">`SaveTokens` ist standardmäßig auf `false` festgelegt, um die Größe des endgültigen Authentifizierungs Cookies zu verringern.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-154">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="cf7d6-155">In der Beispiel-APP wird der Wert von `SaveTokens` auf `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>festgelegt:</span><span class="sxs-lookup"><span data-stu-id="cf7d6-155">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="cf7d6-156">Wenn `OnPostConfirmationAsync` ausgeführt wird, speichern Sie das Zugriffs Token ([externzuweisung-Info. authenticationtokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) vom externen Anbieter im `AuthenticationProperties`der `ApplicationUser`.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-156">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="cf7d6-157">Die Beispiel-App speichert das Zugriffs Token in `OnPostConfirmationAsync` (Neue Benutzerregistrierung) und `OnGetCallbackAsync` (zuvor registrierter Benutzer) in *Account/externzuzugriffgin. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="cf7d6-157">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="cf7d6-158">Vorgehensweise beim Hinzufügen zusätzlicher benutzerdefinierter Token</span><span class="sxs-lookup"><span data-stu-id="cf7d6-158">How to add additional custom tokens</span></span>

<span data-ttu-id="cf7d6-159">Um zu veranschaulichen, wie Sie ein benutzerdefiniertes Token hinzufügen, das als Teil von `SaveTokens`gespeichert wird, fügt die Beispiel-App eine <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> mit dem aktuellen <xref:System.DateTime> für ein [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`hinzu:</span><span class="sxs-lookup"><span data-stu-id="cf7d6-159">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="cf7d6-160">Erstellen und Hinzufügen von Ansprüchen</span><span class="sxs-lookup"><span data-stu-id="cf7d6-160">Creating and adding claims</span></span>

<span data-ttu-id="cf7d6-161">Das Framework bietet allgemeine Aktionen und Erweiterungs Methoden zum Erstellen und Hinzufügen von Ansprüchen zur Auflistung.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-161">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="cf7d6-162">Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> und <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-162">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="cf7d6-163">Benutzer können benutzerdefinierte Aktionen definieren, indem Sie von <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> ableiten und die abstrakte <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*>-Methode implementieren.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-163">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="cf7d6-164">Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-164">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="cf7d6-165">Entfernen von Anspruchs Aktionen und Ansprüchen</span><span class="sxs-lookup"><span data-stu-id="cf7d6-165">Removal of claim actions and claims</span></span>

<span data-ttu-id="cf7d6-166">[Claimaktioncollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) entfernt alle Anspruchs Aktionen für die angegebene <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> aus der Auflistung.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-166">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="cf7d6-167">[Claimaktioncollectionmapextensions. deleteclaim (claimaktioncollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) löscht einen Anspruch der angegebenen <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> aus der Identität.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-167">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="cf7d6-168"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> wird hauptsächlich mit [OpenID Connect (oidc)](/azure/active-directory/develop/v2-protocols-oidc) verwendet, um vom Protokoll generierte Ansprüche zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-168"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="cf7d6-169">Beispiel-App-Ausgabe</span><span class="sxs-lookup"><span data-stu-id="cf7d6-169">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cf7d6-170">Eine ASP.net Core-App kann zusätzliche Ansprüche und Token von externen Authentifizierungs Anbietern (z. b. Facebook, Google, Microsoft und Twitter) einrichten.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-170">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="cf7d6-171">Jeder Anbieter zeigt verschiedene Informationen über Benutzer auf seiner Plattform an, aber das Muster für das empfangen und Transformieren von Benutzerdaten in zusätzliche Ansprüche ist identisch.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-171">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="cf7d6-172">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cf7d6-172">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="cf7d6-173">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="cf7d6-173">Prerequisites</span></span>

<span data-ttu-id="cf7d6-174">Entscheiden Sie, welche externen Authentifizierungs Anbieter in der App unterstützt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-174">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="cf7d6-175">Registrieren Sie die APP für jeden Anbieter, und erhalten Sie eine Client-ID und einen geheimen Client Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-175">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="cf7d6-176">Weitere Informationen finden Sie unter <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-176">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="cf7d6-177">Die Beispiel-App verwendet den [Google-Authentifizierungs Anbieter](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="cf7d6-177">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="cf7d6-178">Festlegen der Client-ID und des geheimen Client Schlüssels</span><span class="sxs-lookup"><span data-stu-id="cf7d6-178">Set the client ID and client secret</span></span>

<span data-ttu-id="cf7d6-179">Der OAuth-Authentifizierungs Anbieter richtet eine Vertrauensstellung mit einer App mithilfe einer Client-ID und eines geheimen Client Schlüssels ein.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-179">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="cf7d6-180">Die Werte für die Client-ID und den geheimen Client Schlüssel werden vom externen Authentifizierungs Anbieter für die App erstellt, wenn die APP beim Anbieter registriert ist.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-180">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="cf7d6-181">Jeder von der APP verwendete externe Anbieter muss unabhängig von der Client-ID des Anbieters und dem geheimen Client Schlüssel konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-181">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="cf7d6-182">Weitere Informationen finden Sie in den Themen zu externen Authentifizierungs Anbietern, die für Ihr Szenario gelten:</span><span class="sxs-lookup"><span data-stu-id="cf7d6-182">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="cf7d6-183">Facebook-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="cf7d6-183">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="cf7d6-184">Google-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="cf7d6-184">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="cf7d6-185">Microsoft-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="cf7d6-185">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="cf7d6-186">Twitter-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="cf7d6-186">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="cf7d6-187">Andere Authentifizierungsanbieter</span><span class="sxs-lookup"><span data-stu-id="cf7d6-187">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="cf7d6-188">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="cf7d6-188">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="cf7d6-189">Die Beispiel-App konfiguriert den Google-Authentifizierungs Anbieter mit einer Client-ID und einem geheimen Client Schlüssel, die von Google bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="cf7d6-189">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="cf7d6-190">Einrichten des Authentifizierungs Bereichs</span><span class="sxs-lookup"><span data-stu-id="cf7d6-190">Establish the authentication scope</span></span>

<span data-ttu-id="cf7d6-191">Geben Sie die Liste der Berechtigungen an, die vom Anbieter abgerufen werden sollen, indem Sie die <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>angeben.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-191">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="cf7d6-192">Authentifizierungs Bereiche für allgemeine externe Anbieter werden in der folgenden Tabelle angezeigt.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-192">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="cf7d6-193">anbieter</span><span class="sxs-lookup"><span data-stu-id="cf7d6-193">Provider</span></span>  | <span data-ttu-id="cf7d6-194">Gültigkeitsbereich</span><span class="sxs-lookup"><span data-stu-id="cf7d6-194">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="cf7d6-195">Facebook</span><span class="sxs-lookup"><span data-stu-id="cf7d6-195">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="cf7d6-196">Google</span><span class="sxs-lookup"><span data-stu-id="cf7d6-196">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="cf7d6-197">Microsoft</span><span class="sxs-lookup"><span data-stu-id="cf7d6-197">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="cf7d6-198">Twitter</span><span class="sxs-lookup"><span data-stu-id="cf7d6-198">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="cf7d6-199">In der Beispiel-APP wird der `userinfo.profile` Bereich von Google automatisch durch das Framework hinzugefügt, wenn <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> auf dem <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-199">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="cf7d6-200">Wenn die APP zusätzliche Bereiche benötigt, fügen Sie Sie den Optionen hinzu.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-200">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="cf7d6-201">Im folgenden Beispiel wird der Google `https://www.googleapis.com/auth/user.birthday.read`-Bereich hinzugefügt, um den Geburtstag eines Benutzers abzurufen:</span><span class="sxs-lookup"><span data-stu-id="cf7d6-201">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="cf7d6-202">Zuordnen von Benutzerdaten Schlüsseln und Erstellen von Ansprüchen</span><span class="sxs-lookup"><span data-stu-id="cf7d6-202">Map user data keys and create claims</span></span>

<span data-ttu-id="cf7d6-203">Geben Sie in den Optionen des Anbieters für jeden Schlüssel/Unterschlüssel in den JSON-Benutzerdaten des externen Anbieters eine <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> oder <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> an, damit die APP-Identität bei der Anmeldung gelesen werden kann.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-203">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="cf7d6-204">Weitere Informationen zu Anspruchs Typen finden Sie unter <xref:System.Security.Claims.ClaimTypes>.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-204">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="cf7d6-205">Die Beispiel-App erstellt die Ansprüche "locale (`urn:google:locale`)" und "Image" (`urn:google:picture`) aus den `locale` und `picture` Schlüsseln in Google User Data:</span><span class="sxs-lookup"><span data-stu-id="cf7d6-205">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="cf7d6-206">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`wird eine <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) mit <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>bei der App angemeldet.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-206">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="cf7d6-207">Während des Anmeldevorgangs können die <xref:Microsoft.AspNetCore.Identity.UserManager%601> eine `ApplicationUser` Ansprüche für Benutzerdaten speichern, die über die <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-207">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="cf7d6-208">In der Beispiel-App richtet `OnPostConfirmationAsync` (*Account/externzuweisung. cshtml. cs*) die Gebiets Schema-(`urn:google:locale`) und Bild (`urn:google:picture`)-Ansprüche für die signierte `ApplicationUser`ein, einschließlich eines Anspruchs für <xref:System.Security.Claims.ClaimTypes.GivenName>:</span><span class="sxs-lookup"><span data-stu-id="cf7d6-208">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="cf7d6-209">Standardmäßig werden die Ansprüche eines Benutzers im Authentifizierungs Cookie gespeichert.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-209">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="cf7d6-210">Wenn das Authentifizierungs Cookie zu groß ist, kann dies dazu führen, dass die APP fehlschlägt:</span><span class="sxs-lookup"><span data-stu-id="cf7d6-210">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="cf7d6-211">Der Browser erkennt, dass der Cookie-Header zu lang ist.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-211">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="cf7d6-212">Die Gesamtgröße der Anforderung ist zu groß.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-212">The overall size of the request is too large.</span></span>

<span data-ttu-id="cf7d6-213">Wenn eine große Menge an Benutzerdaten für die Verarbeitung von Benutzer Anforderungen erforderlich ist:</span><span class="sxs-lookup"><span data-stu-id="cf7d6-213">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="cf7d6-214">Beschränken Sie die Anzahl und die Größe von Benutzer Ansprüchen für die Anforderungs Verarbeitung auf das, was die APP benötigt.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-214">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="cf7d6-215">Verwenden Sie eine benutzerdefinierte <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> für die <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> der cookeauthentifizierungs-Middleware, um die Identitäts übergreifende Identität zu speichern.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-215">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="cf7d6-216">Bewahren Sie große Mengen an Identitätsinformationen auf dem Server auf, und senden Sie nur einen kleinen Sitzungs-ID-Schlüssel an den Client.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-216">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="cf7d6-217">Speichern des Zugriffs Tokens</span><span class="sxs-lookup"><span data-stu-id="cf7d6-217">Save the access token</span></span>

<span data-ttu-id="cf7d6-218"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> definiert, ob Zugriffs-und Aktualisierungs Token nach einer erfolgreichen Autorisierung in der <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> gespeichert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-218"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="cf7d6-219">`SaveTokens` ist standardmäßig auf `false` festgelegt, um die Größe des endgültigen Authentifizierungs Cookies zu verringern.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-219">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="cf7d6-220">In der Beispiel-APP wird der Wert von `SaveTokens` auf `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>festgelegt:</span><span class="sxs-lookup"><span data-stu-id="cf7d6-220">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="cf7d6-221">Wenn `OnPostConfirmationAsync` ausgeführt wird, speichern Sie das Zugriffs Token ([externzuweisung-Info. authenticationtokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) vom externen Anbieter im `AuthenticationProperties`der `ApplicationUser`.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-221">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="cf7d6-222">Die Beispiel-App speichert das Zugriffs Token in `OnPostConfirmationAsync` (Neue Benutzerregistrierung) und `OnGetCallbackAsync` (zuvor registrierter Benutzer) in *Account/externzuzugriffgin. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="cf7d6-222">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="cf7d6-223">Vorgehensweise beim Hinzufügen zusätzlicher benutzerdefinierter Token</span><span class="sxs-lookup"><span data-stu-id="cf7d6-223">How to add additional custom tokens</span></span>

<span data-ttu-id="cf7d6-224">Um zu veranschaulichen, wie Sie ein benutzerdefiniertes Token hinzufügen, das als Teil von `SaveTokens`gespeichert wird, fügt die Beispiel-App eine <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> mit dem aktuellen <xref:System.DateTime> für ein [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`hinzu:</span><span class="sxs-lookup"><span data-stu-id="cf7d6-224">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="cf7d6-225">Erstellen und Hinzufügen von Ansprüchen</span><span class="sxs-lookup"><span data-stu-id="cf7d6-225">Creating and adding claims</span></span>

<span data-ttu-id="cf7d6-226">Das Framework bietet allgemeine Aktionen und Erweiterungs Methoden zum Erstellen und Hinzufügen von Ansprüchen zur Auflistung.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-226">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="cf7d6-227">Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> und <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-227">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="cf7d6-228">Benutzer können benutzerdefinierte Aktionen definieren, indem Sie von <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> ableiten und die abstrakte <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*>-Methode implementieren.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-228">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="cf7d6-229">Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-229">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="cf7d6-230">Entfernen von Anspruchs Aktionen und Ansprüchen</span><span class="sxs-lookup"><span data-stu-id="cf7d6-230">Removal of claim actions and claims</span></span>

<span data-ttu-id="cf7d6-231">[Claimaktioncollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) entfernt alle Anspruchs Aktionen für die angegebene <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> aus der Auflistung.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-231">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="cf7d6-232">[Claimaktioncollectionmapextensions. deleteclaim (claimaktioncollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) löscht einen Anspruch der angegebenen <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> aus der Identität.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-232">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="cf7d6-233"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> wird hauptsächlich mit [OpenID Connect (oidc)](/azure/active-directory/develop/v2-protocols-oidc) verwendet, um vom Protokoll generierte Ansprüche zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-233"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="cf7d6-234">Beispiel-App-Ausgabe</span><span class="sxs-lookup"><span data-stu-id="cf7d6-234">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="cf7d6-235">Weitere Ressourcen</span><span class="sxs-lookup"><span data-stu-id="cf7d6-235">Additional resources</span></span>

* <span data-ttu-id="cf7d6-236">[dotnet/aspnetcore Engineering socialsample-App](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample) &ndash; die verknüpfte Beispiel-App befindet sich im `master` Engineering-Branch des [dotnet/aspnetcore-GitHub-](https://github.com/dotnet/AspNetCore) Repository.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-236">[dotnet/AspNetCore engineering SocialSample app](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample) &ndash; The linked sample app is on the [dotnet/AspNetCore GitHub repo's](https://github.com/dotnet/AspNetCore) `master` engineering branch.</span></span> <span data-ttu-id="cf7d6-237">Der `master` Branch enthält Code für die nächste Version von ASP.net Core unteraktive Entwicklung.</span><span class="sxs-lookup"><span data-stu-id="cf7d6-237">The `master` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="cf7d6-238">Um eine Version der Beispiel-App für eine veröffentlichte Version von ASP.net Core anzuzeigen, wählen Sie in der Dropdown Liste **Verzweigung** einen releasebranch aus (z. b. `release/{X.Y}`).</span><span class="sxs-lookup"><span data-stu-id="cf7d6-238">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/{X.Y}`).</span></span>

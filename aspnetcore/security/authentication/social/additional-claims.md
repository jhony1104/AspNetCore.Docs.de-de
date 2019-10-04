---
title: Persistente weitere Ansprüche und Token von externen Anbietern in ASP.net Core
author: guardrex
description: Erfahren Sie, wie Sie zusätzliche Ansprüche und Token von externen Anbietern einrichten.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2019
uid: security/authentication/social/additional-claims
ms.openlocfilehash: cdf263df8d1aa17ea3820a16ecbd10abce9d683d
ms.sourcegitcommit: 73e255e846e414821b8cc20ffa3aec946735cd4e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/03/2019
ms.locfileid: "71925155"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="34f5a-103">Persistente weitere Ansprüche und Token von externen Anbietern in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="34f5a-103">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

<span data-ttu-id="34f5a-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="34f5a-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="34f5a-105">Eine ASP.net Core-App kann zusätzliche Ansprüche und Token von externen Authentifizierungs Anbietern (z. b. Facebook, Google, Microsoft und Twitter) einrichten.</span><span class="sxs-lookup"><span data-stu-id="34f5a-105">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="34f5a-106">Jeder Anbieter zeigt verschiedene Informationen über Benutzer auf seiner Plattform an, aber das Muster für das empfangen und Transformieren von Benutzerdaten in zusätzliche Ansprüche ist identisch.</span><span class="sxs-lookup"><span data-stu-id="34f5a-106">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="34f5a-107">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="34f5a-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="34f5a-108">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="34f5a-108">Prerequisites</span></span>

<span data-ttu-id="34f5a-109">Entscheiden Sie, welche externen Authentifizierungs Anbieter in der App unterstützt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="34f5a-109">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="34f5a-110">Registrieren Sie die APP für jeden Anbieter, und erhalten Sie eine Client-ID und einen geheimen Client Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="34f5a-110">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="34f5a-111">Weitere Informationen finden Sie unter <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="34f5a-111">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="34f5a-112">Die Beispiel-App verwendet den [Google-Authentifizierungs Anbieter](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="34f5a-112">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="34f5a-113">Festlegen der Client-ID und des geheimen Client Schlüssels</span><span class="sxs-lookup"><span data-stu-id="34f5a-113">Set the client ID and client secret</span></span>

<span data-ttu-id="34f5a-114">Der OAuth-Authentifizierungs Anbieter richtet eine Vertrauensstellung mit einer App mithilfe einer Client-ID und eines geheimen Client Schlüssels ein.</span><span class="sxs-lookup"><span data-stu-id="34f5a-114">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="34f5a-115">Die Werte für die Client-ID und den geheimen Client Schlüssel werden vom externen Authentifizierungs Anbieter für die App erstellt, wenn die APP beim Anbieter registriert ist.</span><span class="sxs-lookup"><span data-stu-id="34f5a-115">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="34f5a-116">Jeder von der APP verwendete externe Anbieter muss unabhängig von der Client-ID des Anbieters und dem geheimen Client Schlüssel konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="34f5a-116">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="34f5a-117">Weitere Informationen finden Sie in den Themen zu externen Authentifizierungs Anbietern, die für Ihr Szenario gelten:</span><span class="sxs-lookup"><span data-stu-id="34f5a-117">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="34f5a-118">Facebook-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="34f5a-118">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="34f5a-119">Google-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="34f5a-119">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="34f5a-120">Microsoft-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="34f5a-120">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="34f5a-121">Twitter-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="34f5a-121">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="34f5a-122">Andere Authentifizierungsanbieter</span><span class="sxs-lookup"><span data-stu-id="34f5a-122">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="34f5a-123">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="34f5a-123">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="34f5a-124">Die Beispiel-App konfiguriert den Google-Authentifizierungs Anbieter mit einer Client-ID und einem geheimen Client Schlüssel, die von Google bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="34f5a-124">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="34f5a-125">Einrichten des Authentifizierungs Bereichs</span><span class="sxs-lookup"><span data-stu-id="34f5a-125">Establish the authentication scope</span></span>

<span data-ttu-id="34f5a-126">Geben Sie die Liste der Berechtigungen an, die vom Anbieter abgerufen werden sollen, indem Sie die <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> angeben.</span><span class="sxs-lookup"><span data-stu-id="34f5a-126">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="34f5a-127">Authentifizierungs Bereiche für allgemeine externe Anbieter werden in der folgenden Tabelle angezeigt.</span><span class="sxs-lookup"><span data-stu-id="34f5a-127">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="34f5a-128">Anbieter</span><span class="sxs-lookup"><span data-stu-id="34f5a-128">Provider</span></span>  | <span data-ttu-id="34f5a-129">Bereich</span><span class="sxs-lookup"><span data-stu-id="34f5a-129">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="34f5a-130">Facebook</span><span class="sxs-lookup"><span data-stu-id="34f5a-130">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="34f5a-131">Google</span><span class="sxs-lookup"><span data-stu-id="34f5a-131">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="34f5a-132">Microsoft</span><span class="sxs-lookup"><span data-stu-id="34f5a-132">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="34f5a-133">Twitter</span><span class="sxs-lookup"><span data-stu-id="34f5a-133">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="34f5a-134">In der Beispiel-APP wird der `userinfo.profile`-Bereich von Google automatisch durch das Framework hinzugefügt, wenn <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> auf dem <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="34f5a-134">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="34f5a-135">Wenn die APP zusätzliche Bereiche benötigt, fügen Sie Sie den Optionen hinzu.</span><span class="sxs-lookup"><span data-stu-id="34f5a-135">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="34f5a-136">Im folgenden Beispiel wird der Google `https://www.googleapis.com/auth/user.birthday.read`-Bereich hinzugefügt, um den Geburtstag eines Benutzers abzurufen:</span><span class="sxs-lookup"><span data-stu-id="34f5a-136">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="34f5a-137">Zuordnen von Benutzerdaten Schlüsseln und Erstellen von Ansprüchen</span><span class="sxs-lookup"><span data-stu-id="34f5a-137">Map user data keys and create claims</span></span>

<span data-ttu-id="34f5a-138">Geben Sie in den Optionen des Anbieters für jeden Schlüssel/Unterschlüssel in den JSON-Benutzerdaten des externen Anbieters eine <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> oder <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> an, damit die APP-Identität bei der Anmeldung gelesen werden kann.</span><span class="sxs-lookup"><span data-stu-id="34f5a-138">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="34f5a-139">Weitere Informationen zu Anspruchs Typen finden Sie unter <xref:System.Security.Claims.ClaimTypes>.</span><span class="sxs-lookup"><span data-stu-id="34f5a-139">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="34f5a-140">Die Beispiel-App erstellt Gebiets Schema-(`urn:google:locale`) und Bild-(`urn:google:picture`) Ansprüche aus den Schlüsseln `locale` und `picture` in Google User Data:</span><span class="sxs-lookup"><span data-stu-id="34f5a-140">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="34f5a-141">In <xref:Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync*> wird ein <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) mit <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> bei der App angemeldet.</span><span class="sxs-lookup"><span data-stu-id="34f5a-141">In <xref:Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync*>, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="34f5a-142">Während des Anmeldevorgangs kann der <xref:Microsoft.AspNetCore.Identity.UserManager%601> einen `ApplicationUser`-Anspruch für Benutzerdaten speichern, die über die <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="34f5a-142">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="34f5a-143">In der Beispiel-App werden von `OnPostConfirmationAsync` (*Account/externzuweisung. cshtml. cs*) die Gebiets Schema-(`urn:google:locale`) und Bild-(`urn:google:picture`) Ansprüche für das signierte `ApplicationUser` festgelegt, einschließlich eines Anspruchs für <xref:System.Security.Claims.ClaimTypes.GivenName>:</span><span class="sxs-lookup"><span data-stu-id="34f5a-143">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="34f5a-144">Standardmäßig werden die Ansprüche eines Benutzers im Authentifizierungs Cookie gespeichert.</span><span class="sxs-lookup"><span data-stu-id="34f5a-144">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="34f5a-145">Wenn das Authentifizierungs Cookie zu groß ist, kann dies dazu führen, dass die APP fehlschlägt:</span><span class="sxs-lookup"><span data-stu-id="34f5a-145">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="34f5a-146">Der Browser erkennt, dass der Cookie-Header zu lang ist.</span><span class="sxs-lookup"><span data-stu-id="34f5a-146">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="34f5a-147">Die Gesamtgröße der Anforderung ist zu groß.</span><span class="sxs-lookup"><span data-stu-id="34f5a-147">The overall size of the request is too large.</span></span>

<span data-ttu-id="34f5a-148">Wenn eine große Menge an Benutzerdaten für die Verarbeitung von Benutzer Anforderungen erforderlich ist:</span><span class="sxs-lookup"><span data-stu-id="34f5a-148">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="34f5a-149">Beschränken Sie die Anzahl und die Größe von Benutzer Ansprüchen für die Anforderungs Verarbeitung auf das, was die APP benötigt.</span><span class="sxs-lookup"><span data-stu-id="34f5a-149">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="34f5a-150">Verwenden Sie eine benutzerdefinierte <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> für die <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> der cookeauthentifizierungs-Middleware, um die Identität über Anforderungen hinweg zu speichern.</span><span class="sxs-lookup"><span data-stu-id="34f5a-150">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="34f5a-151">Bewahren Sie große Mengen an Identitätsinformationen auf dem Server auf, und senden Sie nur einen kleinen Sitzungs-ID-Schlüssel an den Client.</span><span class="sxs-lookup"><span data-stu-id="34f5a-151">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="34f5a-152">Speichern des Zugriffs Tokens</span><span class="sxs-lookup"><span data-stu-id="34f5a-152">Save the access token</span></span>

<span data-ttu-id="34f5a-153"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> definiert, ob Zugriffs-und Aktualisierungs Token nach einer erfolgreichen Autorisierung in der <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> gespeichert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="34f5a-153"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="34f5a-154">`SaveTokens` ist standardmäßig auf `false` festgelegt, um die Größe des endgültigen Authentifizierungs Cookies zu verringern.</span><span class="sxs-lookup"><span data-stu-id="34f5a-154">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="34f5a-155">Die Beispiel-App legt den Wert `SaveTokens` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> auf `true` fest:</span><span class="sxs-lookup"><span data-stu-id="34f5a-155">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="34f5a-156">Wenn `OnPostConfirmationAsync` ausgeführt wird, speichern Sie das Zugriffs Token ([externverteilgininfo. authenticationtokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) vom externen Anbieter in den `ApplicationUser`-`AuthenticationProperties`.</span><span class="sxs-lookup"><span data-stu-id="34f5a-156">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="34f5a-157">Die Beispiel-App speichert das Zugriffs Token in `OnPostConfirmationAsync` (Neue Benutzerregistrierung) und `OnGetCallbackAsync` (zuvor registrierter Benutzer) in *Account/externzuzugriffgin. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="34f5a-157">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="34f5a-158">Vorgehensweise beim Hinzufügen zusätzlicher benutzerdefinierter Token</span><span class="sxs-lookup"><span data-stu-id="34f5a-158">How to add additional custom tokens</span></span>

<span data-ttu-id="34f5a-159">Um zu veranschaulichen, wie Sie ein benutzerdefiniertes Token hinzufügen, das als Teil von `SaveTokens` gespeichert wird, fügt die Beispiel-APP einen <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> mit dem aktuellen <xref:System.DateTime> für eine [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) des `TicketCreated` hinzu:</span><span class="sxs-lookup"><span data-stu-id="34f5a-159">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="34f5a-160">Erstellen und Hinzufügen von Ansprüchen</span><span class="sxs-lookup"><span data-stu-id="34f5a-160">Creating and adding claims</span></span>

<span data-ttu-id="34f5a-161">Das Framework bietet allgemeine Aktionen und Erweiterungs Methoden zum Erstellen und Hinzufügen von Ansprüchen zur Auflistung.</span><span class="sxs-lookup"><span data-stu-id="34f5a-161">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="34f5a-162">Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> und <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span><span class="sxs-lookup"><span data-stu-id="34f5a-162">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="34f5a-163">Benutzer können benutzerdefinierte Aktionen definieren, indem Sie von <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> ableiten und die abstrakte <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*>-Methode implementieren.</span><span class="sxs-lookup"><span data-stu-id="34f5a-163">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="34f5a-164">Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="34f5a-164">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="34f5a-165">Entfernen von Anspruchs Aktionen und Ansprüchen</span><span class="sxs-lookup"><span data-stu-id="34f5a-165">Removal of claim actions and claims</span></span>

<span data-ttu-id="34f5a-166">[Claimaktioncollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) entfernt alle Anspruchs Aktionen für die angegebene <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> aus der Auflistung.</span><span class="sxs-lookup"><span data-stu-id="34f5a-166">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="34f5a-167">[Claimaktioncollectionmapextensions. deleteclaim (claimaktioncollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) löscht einen Anspruch des angegebenen <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> aus der Identität.</span><span class="sxs-lookup"><span data-stu-id="34f5a-167">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="34f5a-168"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> wird hauptsächlich mit [OpenID Connect (oidc)](/azure/active-directory/develop/v2-protocols-oidc) verwendet, um vom Protokoll generierte Ansprüche zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="34f5a-168"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="34f5a-169">Beispiel-App-Ausgabe</span><span class="sxs-lookup"><span data-stu-id="34f5a-169">Sample app output</span></span>

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

<span data-ttu-id="34f5a-170">Eine ASP.net Core-App kann zusätzliche Ansprüche und Token von externen Authentifizierungs Anbietern (z. b. Facebook, Google, Microsoft und Twitter) einrichten.</span><span class="sxs-lookup"><span data-stu-id="34f5a-170">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="34f5a-171">Jeder Anbieter zeigt verschiedene Informationen über Benutzer auf seiner Plattform an, aber das Muster für das empfangen und Transformieren von Benutzerdaten in zusätzliche Ansprüche ist identisch.</span><span class="sxs-lookup"><span data-stu-id="34f5a-171">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="34f5a-172">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="34f5a-172">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="34f5a-173">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="34f5a-173">Prerequisites</span></span>

<span data-ttu-id="34f5a-174">Entscheiden Sie, welche externen Authentifizierungs Anbieter in der App unterstützt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="34f5a-174">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="34f5a-175">Registrieren Sie die APP für jeden Anbieter, und erhalten Sie eine Client-ID und einen geheimen Client Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="34f5a-175">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="34f5a-176">Weitere Informationen finden Sie unter <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="34f5a-176">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="34f5a-177">Die Beispiel-App verwendet den [Google-Authentifizierungs Anbieter](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="34f5a-177">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="34f5a-178">Festlegen der Client-ID und des geheimen Client Schlüssels</span><span class="sxs-lookup"><span data-stu-id="34f5a-178">Set the client ID and client secret</span></span>

<span data-ttu-id="34f5a-179">Der OAuth-Authentifizierungs Anbieter richtet eine Vertrauensstellung mit einer App mithilfe einer Client-ID und eines geheimen Client Schlüssels ein.</span><span class="sxs-lookup"><span data-stu-id="34f5a-179">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="34f5a-180">Die Werte für die Client-ID und den geheimen Client Schlüssel werden vom externen Authentifizierungs Anbieter für die App erstellt, wenn die APP beim Anbieter registriert ist.</span><span class="sxs-lookup"><span data-stu-id="34f5a-180">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="34f5a-181">Jeder von der APP verwendete externe Anbieter muss unabhängig von der Client-ID des Anbieters und dem geheimen Client Schlüssel konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="34f5a-181">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="34f5a-182">Weitere Informationen finden Sie in den Themen zu externen Authentifizierungs Anbietern, die für Ihr Szenario gelten:</span><span class="sxs-lookup"><span data-stu-id="34f5a-182">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="34f5a-183">Facebook-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="34f5a-183">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="34f5a-184">Google-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="34f5a-184">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="34f5a-185">Microsoft-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="34f5a-185">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="34f5a-186">Twitter-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="34f5a-186">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="34f5a-187">Andere Authentifizierungsanbieter</span><span class="sxs-lookup"><span data-stu-id="34f5a-187">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="34f5a-188">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="34f5a-188">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="34f5a-189">Die Beispiel-App konfiguriert den Google-Authentifizierungs Anbieter mit einer Client-ID und einem geheimen Client Schlüssel, die von Google bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="34f5a-189">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="34f5a-190">Einrichten des Authentifizierungs Bereichs</span><span class="sxs-lookup"><span data-stu-id="34f5a-190">Establish the authentication scope</span></span>

<span data-ttu-id="34f5a-191">Geben Sie die Liste der Berechtigungen an, die vom Anbieter abgerufen werden sollen, indem Sie die <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> angeben.</span><span class="sxs-lookup"><span data-stu-id="34f5a-191">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="34f5a-192">Authentifizierungs Bereiche für allgemeine externe Anbieter werden in der folgenden Tabelle angezeigt.</span><span class="sxs-lookup"><span data-stu-id="34f5a-192">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="34f5a-193">Anbieter</span><span class="sxs-lookup"><span data-stu-id="34f5a-193">Provider</span></span>  | <span data-ttu-id="34f5a-194">Bereich</span><span class="sxs-lookup"><span data-stu-id="34f5a-194">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="34f5a-195">Facebook</span><span class="sxs-lookup"><span data-stu-id="34f5a-195">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="34f5a-196">Google</span><span class="sxs-lookup"><span data-stu-id="34f5a-196">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="34f5a-197">Microsoft</span><span class="sxs-lookup"><span data-stu-id="34f5a-197">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="34f5a-198">Twitter</span><span class="sxs-lookup"><span data-stu-id="34f5a-198">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="34f5a-199">In der Beispiel-APP wird der `userinfo.profile`-Bereich von Google automatisch durch das Framework hinzugefügt, wenn <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> auf dem <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="34f5a-199">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="34f5a-200">Wenn die APP zusätzliche Bereiche benötigt, fügen Sie Sie den Optionen hinzu.</span><span class="sxs-lookup"><span data-stu-id="34f5a-200">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="34f5a-201">Im folgenden Beispiel wird der Google `https://www.googleapis.com/auth/user.birthday.read`-Bereich hinzugefügt, um den Geburtstag eines Benutzers abzurufen:</span><span class="sxs-lookup"><span data-stu-id="34f5a-201">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="34f5a-202">Zuordnen von Benutzerdaten Schlüsseln und Erstellen von Ansprüchen</span><span class="sxs-lookup"><span data-stu-id="34f5a-202">Map user data keys and create claims</span></span>

<span data-ttu-id="34f5a-203">Geben Sie in den Optionen des Anbieters für jeden Schlüssel/Unterschlüssel in den JSON-Benutzerdaten des externen Anbieters eine <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> oder <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> an, damit die APP-Identität bei der Anmeldung gelesen werden kann.</span><span class="sxs-lookup"><span data-stu-id="34f5a-203">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="34f5a-204">Weitere Informationen zu Anspruchs Typen finden Sie unter <xref:System.Security.Claims.ClaimTypes>.</span><span class="sxs-lookup"><span data-stu-id="34f5a-204">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="34f5a-205">Die Beispiel-App erstellt Gebiets Schema-(`urn:google:locale`) und Bild-(`urn:google:picture`) Ansprüche aus den Schlüsseln `locale` und `picture` in Google User Data:</span><span class="sxs-lookup"><span data-stu-id="34f5a-205">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="34f5a-206">In <xref:Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync*> wird ein <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) mit <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> bei der App angemeldet.</span><span class="sxs-lookup"><span data-stu-id="34f5a-206">In <xref:Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync*>, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="34f5a-207">Während des Anmeldevorgangs kann der <xref:Microsoft.AspNetCore.Identity.UserManager%601> einen `ApplicationUser`-Anspruch für Benutzerdaten speichern, die über die <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="34f5a-207">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="34f5a-208">In der Beispiel-App werden von `OnPostConfirmationAsync` (*Account/externzuweisung. cshtml. cs*) die Gebiets Schema-(`urn:google:locale`) und Bild-(`urn:google:picture`) Ansprüche für das signierte `ApplicationUser` festgelegt, einschließlich eines Anspruchs für <xref:System.Security.Claims.ClaimTypes.GivenName>:</span><span class="sxs-lookup"><span data-stu-id="34f5a-208">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="34f5a-209">Standardmäßig werden die Ansprüche eines Benutzers im Authentifizierungs Cookie gespeichert.</span><span class="sxs-lookup"><span data-stu-id="34f5a-209">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="34f5a-210">Wenn das Authentifizierungs Cookie zu groß ist, kann dies dazu führen, dass die APP fehlschlägt:</span><span class="sxs-lookup"><span data-stu-id="34f5a-210">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="34f5a-211">Der Browser erkennt, dass der Cookie-Header zu lang ist.</span><span class="sxs-lookup"><span data-stu-id="34f5a-211">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="34f5a-212">Die Gesamtgröße der Anforderung ist zu groß.</span><span class="sxs-lookup"><span data-stu-id="34f5a-212">The overall size of the request is too large.</span></span>

<span data-ttu-id="34f5a-213">Wenn eine große Menge an Benutzerdaten für die Verarbeitung von Benutzer Anforderungen erforderlich ist:</span><span class="sxs-lookup"><span data-stu-id="34f5a-213">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="34f5a-214">Beschränken Sie die Anzahl und die Größe von Benutzer Ansprüchen für die Anforderungs Verarbeitung auf das, was die APP benötigt.</span><span class="sxs-lookup"><span data-stu-id="34f5a-214">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="34f5a-215">Verwenden Sie eine benutzerdefinierte <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> für die <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> der cookeauthentifizierungs-Middleware, um die Identität über Anforderungen hinweg zu speichern.</span><span class="sxs-lookup"><span data-stu-id="34f5a-215">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="34f5a-216">Bewahren Sie große Mengen an Identitätsinformationen auf dem Server auf, und senden Sie nur einen kleinen Sitzungs-ID-Schlüssel an den Client.</span><span class="sxs-lookup"><span data-stu-id="34f5a-216">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="34f5a-217">Speichern des Zugriffs Tokens</span><span class="sxs-lookup"><span data-stu-id="34f5a-217">Save the access token</span></span>

<span data-ttu-id="34f5a-218"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> definiert, ob Zugriffs-und Aktualisierungs Token nach einer erfolgreichen Autorisierung in der <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> gespeichert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="34f5a-218"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="34f5a-219">`SaveTokens` ist standardmäßig auf `false` festgelegt, um die Größe des endgültigen Authentifizierungs Cookies zu verringern.</span><span class="sxs-lookup"><span data-stu-id="34f5a-219">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="34f5a-220">Die Beispiel-App legt den Wert `SaveTokens` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> auf `true` fest:</span><span class="sxs-lookup"><span data-stu-id="34f5a-220">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="34f5a-221">Wenn `OnPostConfirmationAsync` ausgeführt wird, speichern Sie das Zugriffs Token ([externverteilgininfo. authenticationtokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) vom externen Anbieter in den `ApplicationUser`-`AuthenticationProperties`.</span><span class="sxs-lookup"><span data-stu-id="34f5a-221">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="34f5a-222">Die Beispiel-App speichert das Zugriffs Token in `OnPostConfirmationAsync` (Neue Benutzerregistrierung) und `OnGetCallbackAsync` (zuvor registrierter Benutzer) in *Account/externzuzugriffgin. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="34f5a-222">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="34f5a-223">Vorgehensweise beim Hinzufügen zusätzlicher benutzerdefinierter Token</span><span class="sxs-lookup"><span data-stu-id="34f5a-223">How to add additional custom tokens</span></span>

<span data-ttu-id="34f5a-224">Um zu veranschaulichen, wie Sie ein benutzerdefiniertes Token hinzufügen, das als Teil von `SaveTokens` gespeichert wird, fügt die Beispiel-APP einen <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> mit dem aktuellen <xref:System.DateTime> für eine [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) des `TicketCreated` hinzu:</span><span class="sxs-lookup"><span data-stu-id="34f5a-224">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="34f5a-225">Erstellen und Hinzufügen von Ansprüchen</span><span class="sxs-lookup"><span data-stu-id="34f5a-225">Creating and adding claims</span></span>

<span data-ttu-id="34f5a-226">Das Framework bietet allgemeine Aktionen und Erweiterungs Methoden zum Erstellen und Hinzufügen von Ansprüchen zur Auflistung.</span><span class="sxs-lookup"><span data-stu-id="34f5a-226">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="34f5a-227">Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> und <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span><span class="sxs-lookup"><span data-stu-id="34f5a-227">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="34f5a-228">Benutzer können benutzerdefinierte Aktionen definieren, indem Sie von <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> ableiten und die abstrakte <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*>-Methode implementieren.</span><span class="sxs-lookup"><span data-stu-id="34f5a-228">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="34f5a-229">Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="34f5a-229">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="34f5a-230">Entfernen von Anspruchs Aktionen und Ansprüchen</span><span class="sxs-lookup"><span data-stu-id="34f5a-230">Removal of claim actions and claims</span></span>

<span data-ttu-id="34f5a-231">[Claimaktioncollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) entfernt alle Anspruchs Aktionen für die angegebene <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> aus der Auflistung.</span><span class="sxs-lookup"><span data-stu-id="34f5a-231">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="34f5a-232">[Claimaktioncollectionmapextensions. deleteclaim (claimaktioncollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) löscht einen Anspruch des angegebenen <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> aus der Identität.</span><span class="sxs-lookup"><span data-stu-id="34f5a-232">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="34f5a-233"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> wird hauptsächlich mit [OpenID Connect (oidc)](/azure/active-directory/develop/v2-protocols-oidc) verwendet, um vom Protokoll generierte Ansprüche zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="34f5a-233"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="34f5a-234">Beispiel-App-Ausgabe</span><span class="sxs-lookup"><span data-stu-id="34f5a-234">Sample app output</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="34f5a-235">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="34f5a-235">Additional resources</span></span>

* <span data-ttu-id="34f5a-236">[ASPNET/aspnetcore Engineering socialsample-App](https://github.com/aspnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample) &ndash; die verknüpfte Beispiel-App befindet sich im `master` Engineering-Branch des [ASPNET/aspnetcore-GitHub](https://github.com/aspnet/AspNetCore) -Repository.</span><span class="sxs-lookup"><span data-stu-id="34f5a-236">[aspnet/AspNetCore engineering SocialSample app](https://github.com/aspnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample) &ndash; The linked sample app is on the [aspnet/AspNetCore GitHub repo's](https://github.com/aspnet/AspNetCore) `master` engineering branch.</span></span> <span data-ttu-id="34f5a-237">Der Branch "`master`" enthält Code, der in der aktiven Entwicklung für die nächste Version von ASP.net Core enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="34f5a-237">The `master` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="34f5a-238">Um eine Version der Beispiel-App für eine veröffentlichte Version von ASP.net Core anzuzeigen, wählen Sie in der Dropdown Liste **Verzweigung** einen releasebranch aus (z. b. `release/{X.Y}`).</span><span class="sxs-lookup"><span data-stu-id="34f5a-238">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/{X.Y}`).</span></span>

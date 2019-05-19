---
title: Beibehalten von zusätzliche Ansprüche und Token von externen Anbietern in ASP.NET Core
author: guardrex
description: Erfahren Sie, wie zusätzliche Ansprüche und Token von externen Anbietern herzustellen.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/14/2019
uid: security/authentication/social/additional-claims
ms.openlocfilehash: e18287e5a4171b3f7a6daa122111448b8447c1da
ms.sourcegitcommit: ccbb84ae307a5bc527441d3d509c20b5c1edde05
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/19/2019
ms.locfileid: "65874841"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="590e8-103">Beibehalten von zusätzliche Ansprüche und Token von externen Anbietern in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="590e8-103">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

<span data-ttu-id="590e8-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="590e8-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="590e8-105">ASP.NET Core-Apps kann zusätzliche Ansprüche und Token von externen Authentifizierungsanbietern, z. B. Facebook, Google, Microsoft und Twitter herstellen.</span><span class="sxs-lookup"><span data-stu-id="590e8-105">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="590e8-106">Jeder Anbieter werden andere Informationen zu Benutzern auf der Plattform, aber die Muster zum Empfangen und Transformieren von Daten des Benutzers in zusätzliche Ansprüche ist das gleiche.</span><span class="sxs-lookup"><span data-stu-id="590e8-106">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="590e8-107">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="590e8-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="590e8-108">Vorraussetzungen</span><span class="sxs-lookup"><span data-stu-id="590e8-108">Prerequisites</span></span>

<span data-ttu-id="590e8-109">Entscheiden Sie, welche externen Authentifizierungsanbietern, um in der app zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="590e8-109">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="590e8-110">Für jeden Anbieter registrieren der app, und erhalten eine Client-ID und den geheimen Clientschlüssel.</span><span class="sxs-lookup"><span data-stu-id="590e8-110">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="590e8-111">Weitere Informationen finden Sie unter <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="590e8-111">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="590e8-112">Die beispielanwendung verwendet die [Google-Authentifizierungsanbieter](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="590e8-112">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="590e8-113">Legen Sie die Client-ID und geheimer Clientschlüssel</span><span class="sxs-lookup"><span data-stu-id="590e8-113">Set the client ID and client secret</span></span>

<span data-ttu-id="590e8-114">Der OAuth-Authentifizierungsanbieter richtet eine Vertrauensstellung mit einer app mithilfe einer Client-ID und den geheimen Clientschlüssel.</span><span class="sxs-lookup"><span data-stu-id="590e8-114">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="590e8-115">Client-ID und des geheimen clientschlüssels werden erstellt, für die app durch den externen Authentifizierungsanbieter. wenn die app mit dem Anbieter registriert ist.</span><span class="sxs-lookup"><span data-stu-id="590e8-115">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="590e8-116">Jeder externe Anbieter von der app verwendeten muss unabhängig voneinander mit Client-ID und clientgeheimnis des Anbieters konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="590e8-116">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="590e8-117">Weitere Informationen finden Sie unter den Themen der externe Authentifizierung-Anbieter, die für Ihr Szenario gelten:</span><span class="sxs-lookup"><span data-stu-id="590e8-117">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="590e8-118">Facebook-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="590e8-118">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="590e8-119">Google-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="590e8-119">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="590e8-120">Microsoft-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="590e8-120">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="590e8-121">Twitter-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="590e8-121">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="590e8-122">Andere Authentifizierungsanbieter</span><span class="sxs-lookup"><span data-stu-id="590e8-122">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="590e8-123">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="590e8-123">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="590e8-124">Die Beispiel-app wird der Google-Authentifizierungsanbieter konfiguriert, mit einer Client-ID und clientgeheimnis, die von Google bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="590e8-124">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="590e8-125">Den Authentifizierungsbereich einrichten</span><span class="sxs-lookup"><span data-stu-id="590e8-125">Establish the authentication scope</span></span>

<span data-ttu-id="590e8-126">Geben Sie die Liste der Berechtigungen zum Abrufen des Anbieters durch Angabe der <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span><span class="sxs-lookup"><span data-stu-id="590e8-126">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="590e8-127">Authentifizierung-Bereiche für allgemeine externe Anbieter werden in der folgenden Tabelle angezeigt.</span><span class="sxs-lookup"><span data-stu-id="590e8-127">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="590e8-128">Anbieter</span><span class="sxs-lookup"><span data-stu-id="590e8-128">Provider</span></span>  | <span data-ttu-id="590e8-129">Bereich</span><span class="sxs-lookup"><span data-stu-id="590e8-129">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="590e8-130">Facebook</span><span class="sxs-lookup"><span data-stu-id="590e8-130">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="590e8-131">Google</span><span class="sxs-lookup"><span data-stu-id="590e8-131">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="590e8-132">Microsoft</span><span class="sxs-lookup"><span data-stu-id="590e8-132">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="590e8-133">Twitter</span><span class="sxs-lookup"><span data-stu-id="590e8-133">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="590e8-134">In der Beispiel-app, Google `userinfo.profile` Bereich wird automatisch hinzugefügt, durch das Framework beim <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> aufgerufen wird, auf die <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="590e8-134">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="590e8-135">Wenn die app zusätzliche Bereiche erfordert, fügen sie die Optionen hinzu.</span><span class="sxs-lookup"><span data-stu-id="590e8-135">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="590e8-136">Im folgenden Beispiel das Google `https://www.googleapis.com/auth/user.birthday.read` Bereich wird hinzugefügt, um Geburtstag des Benutzers abzurufen:</span><span class="sxs-lookup"><span data-stu-id="590e8-136">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="590e8-137">Ordnen Sie die Schlüssel für den Benutzer Daten und Erstellen von Ansprüchen</span><span class="sxs-lookup"><span data-stu-id="590e8-137">Map user data keys and create claims</span></span>

<span data-ttu-id="590e8-138">Geben Sie in den Optionen des Anbieters ein <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> oder <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> für jeden Schlüssel/Unterschlüssel in der externe Anbieter JSON-Eingabe der Benutzerdaten für den app-Identität bei der Anmeldung zu lesen.</span><span class="sxs-lookup"><span data-stu-id="590e8-138">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="590e8-139">Weitere Informationen zu den Anspruchstypen, finden Sie unter <xref:System.Security.Claims.ClaimTypes>.</span><span class="sxs-lookup"><span data-stu-id="590e8-139">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="590e8-140">Die Beispiel-app erstellt Gebietsschema (`urn:google:locale`) und das Bild (`urn:google:picture`) von Ansprüchen aus der `locale` und `picture` Schlüssel in der Google-Benutzerdaten:</span><span class="sxs-lookup"><span data-stu-id="590e8-140">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="590e8-141">In <xref:Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync*>, <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) wird in die app signiert <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span><span class="sxs-lookup"><span data-stu-id="590e8-141">In <xref:Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync*>, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="590e8-142">Während des Anmeldeprozesses die <xref:Microsoft.AspNetCore.Identity.UserManager%601> speichern kann ein `ApplicationUser` Ansprüche für verfügbaren Benutzerdaten aus der <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span><span class="sxs-lookup"><span data-stu-id="590e8-142">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="590e8-143">In der Beispiel-app `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) legt das Gebietsschema (`urn:google:locale`) und das Bild (`urn:google:picture`) Ansprüche für die signierte in `ApplicationUser`, einschließlich einen Anspruch für <xref:System.Security.Claims.ClaimTypes.GivenName> :</span><span class="sxs-lookup"><span data-stu-id="590e8-143">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="590e8-144">Standardmäßig werden die Ansprüche des Benutzers im Authentifizierungscookie gespeichert.</span><span class="sxs-lookup"><span data-stu-id="590e8-144">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="590e8-145">Wenn das Authentifizierungscookie zu groß ist, können sie die app fehlschlägt, weil verursachen:</span><span class="sxs-lookup"><span data-stu-id="590e8-145">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="590e8-146">Der Browser erkennt, dass der Cookie-Header zu lang ist.</span><span class="sxs-lookup"><span data-stu-id="590e8-146">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="590e8-147">Die Gesamtgröße der Anforderung ist zu groß.</span><span class="sxs-lookup"><span data-stu-id="590e8-147">The overall size of the request is too large.</span></span>

<span data-ttu-id="590e8-148">Wenn eine große Menge von Benutzerdaten zum Verarbeiten von benutzeranforderungen erforderlich ist:</span><span class="sxs-lookup"><span data-stu-id="590e8-148">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="590e8-149">Beschränken Sie die Anzahl und Größe von benutzeransprüchen für die anforderungsverarbeitung ausgeführt werden, um nur das, was der app erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="590e8-149">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="590e8-150">Verwenden Sie eine benutzerdefinierte <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> für die Cookie-Authentifizierung-Middleware <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> auf Identität anforderungsübergreifend gespeichert.</span><span class="sxs-lookup"><span data-stu-id="590e8-150">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="590e8-151">Behalten Sie große Mengen an Informationen zur Identität auf dem Server und nur einen kleinen Sitzungsschlüssel für den Bezeichner an den Client gesendet.</span><span class="sxs-lookup"><span data-stu-id="590e8-151">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="590e8-152">Speichern Sie das Zugriffstoken</span><span class="sxs-lookup"><span data-stu-id="590e8-152">Save the access token</span></span>

<span data-ttu-id="590e8-153"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> definiert, ob die Zugriffs-und Aktualisierungstoken gespeichert werden sollen, in der <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> nach einer erfolgreichen Autorisierung.</span><span class="sxs-lookup"><span data-stu-id="590e8-153"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="590e8-154">`SaveTokens` nastaven NA hodnotu `false` werden standardmäßig die Größe des endgültigen Authentifizierungscookies zu reduzieren.</span><span class="sxs-lookup"><span data-stu-id="590e8-154">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="590e8-155">Die Beispiel-app wird der Wert der `SaveTokens` zu `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span><span class="sxs-lookup"><span data-stu-id="590e8-155">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="590e8-156">Wenn `OnPostConfirmationAsync` ausgeführt wird, speichern Sie das Zugriffstoken ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) vom externen Anbieter in der `ApplicationUser`des `AuthenticationProperties`.</span><span class="sxs-lookup"><span data-stu-id="590e8-156">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="590e8-157">Die Beispiel-app speichert das Zugriffstoken in `OnPostConfirmationAsync` (Registrierung neuer Benutzer) und `OnGetCallbackAsync` (zuvor registrierte Benutzer) in *Account/ExternalLogin.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="590e8-157">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="590e8-158">So fügen Sie zusätzliche benutzerdefinierte Token hinzu:</span><span class="sxs-lookup"><span data-stu-id="590e8-158">How to add additional custom tokens</span></span>

<span data-ttu-id="590e8-159">Veranschaulicht, wie ein benutzerdefiniertes Token an, hinzufügen, die als Teil der gespeichert wird `SaveTokens`, fügt die Beispiel-app eine <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> mit dem aktuellen <xref:System.DateTime> für eine [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) von `TicketCreated`:</span><span class="sxs-lookup"><span data-stu-id="590e8-159">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-28)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="590e8-160">Erstellen und Hinzufügen von Ansprüchen</span><span class="sxs-lookup"><span data-stu-id="590e8-160">Creating and adding claims</span></span>

<span data-ttu-id="590e8-161">Das Framework stellt häufig verwendete Aktionen und Erweiterungsmethoden zum Erstellen und Hinzufügen von Ansprüchen, die der Auflistung.</span><span class="sxs-lookup"><span data-stu-id="590e8-161">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="590e8-162">Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> und <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span><span class="sxs-lookup"><span data-stu-id="590e8-162">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="590e8-163">Benutzer können benutzerdefinierte Aktionen definieren, durch Ableiten von <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> und Implementierung der abstrakten <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> Methode.</span><span class="sxs-lookup"><span data-stu-id="590e8-163">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="590e8-164">Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="590e8-164">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="590e8-165">Entfernen von Anspruchs-Aktionen und Ansprüche</span><span class="sxs-lookup"><span data-stu-id="590e8-165">Removal of claim actions and claims</span></span>

<span data-ttu-id="590e8-166">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) entfernt alle Anspruch Aktionen für den angegebenen <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> aus der Auflistung.</span><span class="sxs-lookup"><span data-stu-id="590e8-166">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="590e8-167">[ClaimActionCollectionMapExtensions.DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) Löscht einen Anspruch, der den angegebenen <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> aus der Identität.</span><span class="sxs-lookup"><span data-stu-id="590e8-167">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="590e8-168"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> Dient in erster Linie mit [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) Protokoll generierten Ansprüche zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="590e8-168"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="590e8-169">Beispiel-app-Ausgabe</span><span class="sxs-lookup"><span data-stu-id="590e8-169">Sample app output</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="590e8-170">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="590e8-170">Additional resources</span></span>

* <span data-ttu-id="590e8-171">[ASPNET/AspNetCore engineering SocialSample app](https://github.com/aspnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample) &ndash; die verknüpften Beispiel-app befindet sich auf die [Aspnet/AspNetCore-GitHub-Repository](https://github.com/aspnet/AspNetCore) `master` engineering Branch.</span><span class="sxs-lookup"><span data-stu-id="590e8-171">[aspnet/AspNetCore engineering SocialSample app](https://github.com/aspnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample) &ndash; The linked sample app is on the [aspnet/AspNetCore GitHub repo's](https://github.com/aspnet/AspNetCore) `master` engineering branch.</span></span> <span data-ttu-id="590e8-172">Die `master` Branch enthält Code in der aktiven Entwicklung für die nächste Version von ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="590e8-172">The `master` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="590e8-173">Um eine Version der Beispiel-app für eine veröffentlichte Version von ASP.NET Core anzuzeigen, verwenden die **Branch** Dropdown-Liste, um einen releasebranch auszuwählen (z. B. `release/2.2`).</span><span class="sxs-lookup"><span data-stu-id="590e8-173">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/2.2`).</span></span>

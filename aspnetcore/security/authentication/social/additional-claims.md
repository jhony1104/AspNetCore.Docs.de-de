---
title: Persistente weitere Ansprüche und Token von externen Anbietern in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie zusätzliche Ansprüche und Token von externen Anbietern einrichten.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/social/additional-claims
ms.openlocfilehash: 6acc1d78bf5cc39fd69329bad1cff0fbe52d9358
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82769029"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a>Persistente weitere Ansprüche und Token von externen Anbietern in ASP.net Core

::: moniker range=">= aspnetcore-3.0"

Eine ASP.net Core-App kann zusätzliche Ansprüche und Token von externen Authentifizierungs Anbietern (z. b. Facebook, Google, Microsoft und Twitter) einrichten. Jeder Anbieter zeigt verschiedene Informationen über Benutzer auf seiner Plattform an, aber das Muster für das empfangen und Transformieren von Benutzerdaten in zusätzliche Ansprüche ist identisch.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Voraussetzungen

Entscheiden Sie, welche externen Authentifizierungs Anbieter in der App unterstützt werden sollen. Registrieren Sie die APP für jeden Anbieter, und erhalten Sie eine Client-ID und einen geheimen Client Schlüssel. Weitere Informationen finden Sie unter <xref:security/authentication/social/index>. Die Beispiel-App verwendet den [Google-Authentifizierungs Anbieter](xref:security/authentication/google-logins).

## <a name="set-the-client-id-and-client-secret"></a>Festlegen der Client-ID und des geheimen Client Schlüssels

Der OAuth-Authentifizierungs Anbieter richtet eine Vertrauensstellung mit einer App mithilfe einer Client-ID und eines geheimen Client Schlüssels ein. Die Werte für die Client-ID und den geheimen Client Schlüssel werden vom externen Authentifizierungs Anbieter für die App erstellt, wenn die APP beim Anbieter registriert ist. Jeder von der APP verwendete externe Anbieter muss unabhängig von der Client-ID des Anbieters und dem geheimen Client Schlüssel konfiguriert werden. Weitere Informationen finden Sie in den Themen zu externen Authentifizierungs Anbietern, die für Ihr Szenario gelten:

* [Facebook-Authentifizierung](xref:security/authentication/facebook-logins)
* [Google-Authentifizierung](xref:security/authentication/google-logins)
* [Microsoft-Authentifizierung](xref:security/authentication/microsoft-logins)
* [Twitter-Authentifizierung](xref:security/authentication/twitter-logins)
* [Andere Authentifizierungsanbieter](xref:security/authentication/otherlogins)
* [OpenIdConnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

Die Beispiel-App konfiguriert den Google-Authentifizierungs Anbieter mit einer Client-ID und einem geheimen Client Schlüssel, die von Google bereitgestellt werden:

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a>Einrichten des Authentifizierungs Bereichs

Geben Sie die Liste der Berechtigungen an, die vom Anbieter abgerufen werden <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>sollen, indem Sie angeben. Authentifizierungs Bereiche für allgemeine externe Anbieter werden in der folgenden Tabelle angezeigt.

| Anbieter  | Bereich                                                            |
| --------- | ---------------------------------------------------------------- |
| Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |

In der Beispiel- `userinfo.profile` APP wird der Bereich von Google automatisch durch das Framework hinzu <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> gefügt, wenn für <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>aufgerufen wird. Wenn die APP zusätzliche Bereiche benötigt, fügen Sie Sie den Optionen hinzu. Im folgenden Beispiel wird der Google `https://www.googleapis.com/auth/user.birthday.read` -Bereich hinzugefügt, um den Geburtstag eines Benutzers abzurufen:

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a>Zuordnen von Benutzerdaten Schlüsseln und Erstellen von Ansprüchen

Geben Sie in den Optionen des Anbieters in <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> den <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> JSON-Benutzerdaten des externen Anbieters einen oder für jeden Schlüssel/Unterschlüssel an, damit die APP-Identität bei der Anmeldung gelesen werden kann. Weitere Informationen zu Anspruchs Typen finden <xref:System.Security.Claims.ClaimTypes>Sie unter.

Die Beispiel-App erstellt Gebiets`urn:google:locale`Schema-()`urn:google:picture`und Bild () `locale` - `picture` Ansprüche aus den Schlüsseln und in Google User Data:

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`wird ein <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) bei der APP mit <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>signiert. Während des Anmeldevorgangs <xref:Microsoft.AspNetCore.Identity.UserManager%601> kann eine `ApplicationUser` Ansprüche für Benutzerdaten speichern, die im verfügbar sind. <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>

In der Beispiel-APP `OnPostConfirmationAsync` richtet (*Account/externzuweisung. cshtml. cs*`urn:google:locale`) die locale ()-und Image (`urn:google:picture`)-Ansprüche für die angemeldete ein `ApplicationUser`, einschließlich eines <xref:System.Security.Claims.ClaimTypes.GivenName>Anspruchs für:

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

Standardmäßig werden die Ansprüche eines Benutzers im Authentifizierungs Cookie gespeichert. Wenn das Authentifizierungs Cookie zu groß ist, kann dies dazu führen, dass die APP fehlschlägt:

* Der Browser erkennt, dass der Cookie-Header zu lang ist.
* Die Gesamtgröße der Anforderung ist zu groß.

Wenn eine große Menge an Benutzerdaten für die Verarbeitung von Benutzer Anforderungen erforderlich ist:

* Beschränken Sie die Anzahl und die Größe von Benutzer Ansprüchen für die Anforderungs Verarbeitung auf das, was die APP benötigt.
* Verwenden Sie eine <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> benutzerdefinierte für die cookeauthentifizierungs <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> -Middleware, um die Identität über Anforderungen hinweg zu speichern. Bewahren Sie große Mengen an Identitätsinformationen auf dem Server auf, und senden Sie nur einen kleinen Sitzungs-ID-Schlüssel an den Client.

## <a name="save-the-access-token"></a>Speichern des Zugriffs Tokens

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>definiert, ob Zugriffs-und Aktualisierungs Token nach einer <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> erfolgreichen Autorisierung in der gespeichert werden sollen. `SaveTokens`wird standardmäßig `false` auf festgelegt, um die Größe des endgültigen Authentifizierungs Cookies zu verringern.

Die Beispiel-App legt den Wert `SaveTokens` von `true` auf <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>in fest:

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

Wenn `OnPostConfirmationAsync` ausgeführt wird, speichern Sie das Zugriffs Token ([externzuweisung-Info. authenticationtokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) vom externen Anbieter in `ApplicationUser`der `AuthenticationProperties`.

Die Beispiel-App speichert das Zugriffs Token `OnPostConfirmationAsync` in (Neue Benutzerregistrierung) `OnGetCallbackAsync` und (zuvor registrierter Benutzer) in *Account/externzuzugriffgin. cshtml. cs*:

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a>Vorgehensweise beim Hinzufügen zusätzlicher benutzerdefinierter Token

Um zu veranschaulichen, wie Sie ein benutzerdefiniertes Token hinzufügen, das `SaveTokens`als Teil von gespeichert wird, <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> fügt die Beispiel <xref:System.DateTime> -APP einen mit `TicketCreated`dem aktuellen für einen [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) von hinzu:

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a>Erstellen und Hinzufügen von Ansprüchen

Das Framework bietet allgemeine Aktionen und Erweiterungs Methoden zum Erstellen und Hinzufügen von Ansprüchen zur Auflistung. Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> und <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.

Benutzer können benutzerdefinierte Aktionen definieren, indem <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> Sie von ableiten und <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> die abstrakte-Methode implementieren.

Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.

## <a name="removal-of-claim-actions-and-claims"></a>Entfernen von Anspruchs Aktionen und Ansprüchen

[Claimaktioncollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) entfernt alle Anspruchs Aktionen für den angegebenen <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> aus der Auflistung. [Claimaktioncollectionmapextensions. deleteclaim (claimaktioncollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) löscht einen Anspruch des angegebenen <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> aus der Identität. <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>wird hauptsächlich mit [OpenID Connect (oidc)](/azure/active-directory/develop/v2-protocols-oidc) verwendet, um vom Protokoll generierte Ansprüche zu entfernen.

## <a name="sample-app-output"></a>Beispiel-App-Ausgabe

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

Eine ASP.net Core-App kann zusätzliche Ansprüche und Token von externen Authentifizierungs Anbietern (z. b. Facebook, Google, Microsoft und Twitter) einrichten. Jeder Anbieter zeigt verschiedene Informationen über Benutzer auf seiner Plattform an, aber das Muster für das empfangen und Transformieren von Benutzerdaten in zusätzliche Ansprüche ist identisch.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Voraussetzungen

Entscheiden Sie, welche externen Authentifizierungs Anbieter in der App unterstützt werden sollen. Registrieren Sie die APP für jeden Anbieter, und erhalten Sie eine Client-ID und einen geheimen Client Schlüssel. Weitere Informationen finden Sie unter <xref:security/authentication/social/index>. Die Beispiel-App verwendet den [Google-Authentifizierungs Anbieter](xref:security/authentication/google-logins).

## <a name="set-the-client-id-and-client-secret"></a>Festlegen der Client-ID und des geheimen Client Schlüssels

Der OAuth-Authentifizierungs Anbieter richtet eine Vertrauensstellung mit einer App mithilfe einer Client-ID und eines geheimen Client Schlüssels ein. Die Werte für die Client-ID und den geheimen Client Schlüssel werden vom externen Authentifizierungs Anbieter für die App erstellt, wenn die APP beim Anbieter registriert ist. Jeder von der APP verwendete externe Anbieter muss unabhängig von der Client-ID des Anbieters und dem geheimen Client Schlüssel konfiguriert werden. Weitere Informationen finden Sie in den Themen zu externen Authentifizierungs Anbietern, die für Ihr Szenario gelten:

* [Facebook-Authentifizierung](xref:security/authentication/facebook-logins)
* [Google-Authentifizierung](xref:security/authentication/google-logins)
* [Microsoft-Authentifizierung](xref:security/authentication/microsoft-logins)
* [Twitter-Authentifizierung](xref:security/authentication/twitter-logins)
* [Andere Authentifizierungsanbieter](xref:security/authentication/otherlogins)
* [OpenIdConnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

Die Beispiel-App konfiguriert den Google-Authentifizierungs Anbieter mit einer Client-ID und einem geheimen Client Schlüssel, die von Google bereitgestellt werden:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a>Einrichten des Authentifizierungs Bereichs

Geben Sie die Liste der Berechtigungen an, die vom Anbieter abgerufen werden <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>sollen, indem Sie angeben. Authentifizierungs Bereiche für allgemeine externe Anbieter werden in der folgenden Tabelle angezeigt.

| Anbieter  | Bereich                                                            |
| --------- | ---------------------------------------------------------------- |
| Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |

In der Beispiel- `userinfo.profile` APP wird der Bereich von Google automatisch durch das Framework hinzu <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> gefügt, wenn für <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>aufgerufen wird. Wenn die APP zusätzliche Bereiche benötigt, fügen Sie Sie den Optionen hinzu. Im folgenden Beispiel wird der Google `https://www.googleapis.com/auth/user.birthday.read` -Bereich hinzugefügt, um den Geburtstag eines Benutzers abzurufen:

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a>Zuordnen von Benutzerdaten Schlüsseln und Erstellen von Ansprüchen

Geben Sie in den Optionen des Anbieters in <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> den <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> JSON-Benutzerdaten des externen Anbieters einen oder für jeden Schlüssel/Unterschlüssel an, damit die APP-Identität bei der Anmeldung gelesen werden kann. Weitere Informationen zu Anspruchs Typen finden <xref:System.Security.Claims.ClaimTypes>Sie unter.

Die Beispiel-App erstellt Gebiets`urn:google:locale`Schema-()`urn:google:picture`und Bild () `locale` - `picture` Ansprüche aus den Schlüsseln und in Google User Data:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`wird ein <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) bei der APP mit <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>signiert. Während des Anmeldevorgangs <xref:Microsoft.AspNetCore.Identity.UserManager%601> kann eine `ApplicationUser` Ansprüche für Benutzerdaten speichern, die im verfügbar sind. <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>

In der Beispiel-APP `OnPostConfirmationAsync` richtet (*Account/externzuweisung. cshtml. cs*`urn:google:locale`) die locale ()-und Image (`urn:google:picture`)-Ansprüche für die angemeldete ein `ApplicationUser`, einschließlich eines <xref:System.Security.Claims.ClaimTypes.GivenName>Anspruchs für:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

Standardmäßig werden die Ansprüche eines Benutzers im Authentifizierungs Cookie gespeichert. Wenn das Authentifizierungs Cookie zu groß ist, kann dies dazu führen, dass die APP fehlschlägt:

* Der Browser erkennt, dass der Cookie-Header zu lang ist.
* Die Gesamtgröße der Anforderung ist zu groß.

Wenn eine große Menge an Benutzerdaten für die Verarbeitung von Benutzer Anforderungen erforderlich ist:

* Beschränken Sie die Anzahl und die Größe von Benutzer Ansprüchen für die Anforderungs Verarbeitung auf das, was die APP benötigt.
* Verwenden Sie eine <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> benutzerdefinierte für die cookeauthentifizierungs <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> -Middleware, um die Identität über Anforderungen hinweg zu speichern. Bewahren Sie große Mengen an Identitätsinformationen auf dem Server auf, und senden Sie nur einen kleinen Sitzungs-ID-Schlüssel an den Client.

## <a name="save-the-access-token"></a>Speichern des Zugriffs Tokens

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>definiert, ob Zugriffs-und Aktualisierungs Token nach einer <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> erfolgreichen Autorisierung in der gespeichert werden sollen. `SaveTokens`wird standardmäßig `false` auf festgelegt, um die Größe des endgültigen Authentifizierungs Cookies zu verringern.

Die Beispiel-App legt den Wert `SaveTokens` von `true` auf <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>in fest:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

Wenn `OnPostConfirmationAsync` ausgeführt wird, speichern Sie das Zugriffs Token ([externzuweisung-Info. authenticationtokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) vom externen Anbieter in `ApplicationUser`der `AuthenticationProperties`.

Die Beispiel-App speichert das Zugriffs Token `OnPostConfirmationAsync` in (Neue Benutzerregistrierung) `OnGetCallbackAsync` und (zuvor registrierter Benutzer) in *Account/externzuzugriffgin. cshtml. cs*:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a>Vorgehensweise beim Hinzufügen zusätzlicher benutzerdefinierter Token

Um zu veranschaulichen, wie Sie ein benutzerdefiniertes Token hinzufügen, das `SaveTokens`als Teil von gespeichert wird, <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> fügt die Beispiel <xref:System.DateTime> -APP einen mit `TicketCreated`dem aktuellen für einen [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) von hinzu:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a>Erstellen und Hinzufügen von Ansprüchen

Das Framework bietet allgemeine Aktionen und Erweiterungs Methoden zum Erstellen und Hinzufügen von Ansprüchen zur Auflistung. Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> und <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.

Benutzer können benutzerdefinierte Aktionen definieren, indem <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> Sie von ableiten und <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> die abstrakte-Methode implementieren.

Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.

## <a name="removal-of-claim-actions-and-claims"></a>Entfernen von Anspruchs Aktionen und Ansprüchen

[Claimaktioncollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) entfernt alle Anspruchs Aktionen für den angegebenen <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> aus der Auflistung. [Claimaktioncollectionmapextensions. deleteclaim (claimaktioncollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) löscht einen Anspruch des angegebenen <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> aus der Identität. <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>wird hauptsächlich mit [OpenID Connect (oidc)](/azure/active-directory/develop/v2-protocols-oidc) verwendet, um vom Protokoll generierte Ansprüche zu entfernen.

## <a name="sample-app-output"></a>Beispiel-App-Ausgabe

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

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [dotnet/aspnetcore Engineering socialsample-App](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample) &ndash; die verknüpfte Beispiel-App befindet sich in der Engineering-Verzweigung des [dotnet/aspnetcore-GitHub-](https://github.com/dotnet/AspNetCore) `master` Repository. Die `master` Verzweigung enthält Code, der in der aktiven Entwicklung für die nächste Version von ASP.net Core enthalten ist. Um eine Version der Beispiel-App für eine veröffentlichte Version von ASP.net Core anzuzeigen, wählen Sie in der Dropdown Liste **Verzweigung** einen releasebranch aus `release/{X.Y}`(z. b.).

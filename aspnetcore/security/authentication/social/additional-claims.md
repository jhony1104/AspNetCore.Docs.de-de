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
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a>Persistente weitere Ansprüche und Token von externen Anbietern in ASP.net Core

Von [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-3.0"

Eine ASP.net Core-App kann zusätzliche Ansprüche und Token von externen Authentifizierungs Anbietern (z. b. Facebook, Google, Microsoft und Twitter) einrichten. Jeder Anbieter zeigt verschiedene Informationen über Benutzer auf seiner Plattform an, aber das Muster für das empfangen und Transformieren von Benutzerdaten in zusätzliche Ansprüche ist identisch.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Erforderliche Komponenten

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

Geben Sie die Liste der Berechtigungen an, die vom Anbieter abgerufen werden sollen, indem Sie die <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>angeben. Authentifizierungs Bereiche für allgemeine externe Anbieter werden in der folgenden Tabelle angezeigt.

| anbieter  | Gültigkeitsbereich                                                            |
| --------- | ---------------------------------------------------------------- |
| Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |

In der Beispiel-APP wird der `userinfo.profile` Bereich von Google automatisch durch das Framework hinzugefügt, wenn <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> auf dem <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>aufgerufen wird. Wenn die APP zusätzliche Bereiche benötigt, fügen Sie Sie den Optionen hinzu. Im folgenden Beispiel wird der Google `https://www.googleapis.com/auth/user.birthday.read`-Bereich hinzugefügt, um den Geburtstag eines Benutzers abzurufen:

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a>Zuordnen von Benutzerdaten Schlüsseln und Erstellen von Ansprüchen

Geben Sie in den Optionen des Anbieters für jeden Schlüssel/Unterschlüssel in den JSON-Benutzerdaten des externen Anbieters eine <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> oder <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> an, damit die APP-Identität bei der Anmeldung gelesen werden kann. Weitere Informationen zu Anspruchs Typen finden Sie unter <xref:System.Security.Claims.ClaimTypes>.

Die Beispiel-App erstellt die Ansprüche "locale (`urn:google:locale`)" und "Image" (`urn:google:picture`) aus den `locale` und `picture` Schlüsseln in Google User Data:

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`wird eine <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) mit <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>bei der App angemeldet. Während des Anmeldevorgangs können die <xref:Microsoft.AspNetCore.Identity.UserManager%601> eine `ApplicationUser` Ansprüche für Benutzerdaten speichern, die über die <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>verfügbar sind.

In der Beispiel-App richtet `OnPostConfirmationAsync` (*Account/externzuweisung. cshtml. cs*) die Gebiets Schema-(`urn:google:locale`) und Bild (`urn:google:picture`)-Ansprüche für die signierte `ApplicationUser`ein, einschließlich eines Anspruchs für <xref:System.Security.Claims.ClaimTypes.GivenName>:

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

Standardmäßig werden die Ansprüche eines Benutzers im Authentifizierungs Cookie gespeichert. Wenn das Authentifizierungs Cookie zu groß ist, kann dies dazu führen, dass die APP fehlschlägt:

* Der Browser erkennt, dass der Cookie-Header zu lang ist.
* Die Gesamtgröße der Anforderung ist zu groß.

Wenn eine große Menge an Benutzerdaten für die Verarbeitung von Benutzer Anforderungen erforderlich ist:

* Beschränken Sie die Anzahl und die Größe von Benutzer Ansprüchen für die Anforderungs Verarbeitung auf das, was die APP benötigt.
* Verwenden Sie eine benutzerdefinierte <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> für die <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> der cookeauthentifizierungs-Middleware, um die Identitäts übergreifende Identität zu speichern. Bewahren Sie große Mengen an Identitätsinformationen auf dem Server auf, und senden Sie nur einen kleinen Sitzungs-ID-Schlüssel an den Client.

## <a name="save-the-access-token"></a>Speichern des Zugriffs Tokens

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> definiert, ob Zugriffs-und Aktualisierungs Token nach einer erfolgreichen Autorisierung in der <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> gespeichert werden sollen. `SaveTokens` ist standardmäßig auf `false` festgelegt, um die Größe des endgültigen Authentifizierungs Cookies zu verringern.

In der Beispiel-APP wird der Wert von `SaveTokens` auf `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>festgelegt:

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

Wenn `OnPostConfirmationAsync` ausgeführt wird, speichern Sie das Zugriffs Token ([externzuweisung-Info. authenticationtokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) vom externen Anbieter im `AuthenticationProperties`der `ApplicationUser`.

Die Beispiel-App speichert das Zugriffs Token in `OnPostConfirmationAsync` (Neue Benutzerregistrierung) und `OnGetCallbackAsync` (zuvor registrierter Benutzer) in *Account/externzuzugriffgin. cshtml. cs*:

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a>Vorgehensweise beim Hinzufügen zusätzlicher benutzerdefinierter Token

Um zu veranschaulichen, wie Sie ein benutzerdefiniertes Token hinzufügen, das als Teil von `SaveTokens`gespeichert wird, fügt die Beispiel-App eine <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> mit dem aktuellen <xref:System.DateTime> für ein [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`hinzu:

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a>Erstellen und Hinzufügen von Ansprüchen

Das Framework bietet allgemeine Aktionen und Erweiterungs Methoden zum Erstellen und Hinzufügen von Ansprüchen zur Auflistung. Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> und <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.

Benutzer können benutzerdefinierte Aktionen definieren, indem Sie von <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> ableiten und die abstrakte <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*>-Methode implementieren.

Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.

## <a name="removal-of-claim-actions-and-claims"></a>Entfernen von Anspruchs Aktionen und Ansprüchen

[Claimaktioncollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) entfernt alle Anspruchs Aktionen für die angegebene <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> aus der Auflistung. [Claimaktioncollectionmapextensions. deleteclaim (claimaktioncollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) löscht einen Anspruch der angegebenen <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> aus der Identität. <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> wird hauptsächlich mit [OpenID Connect (oidc)](/azure/active-directory/develop/v2-protocols-oidc) verwendet, um vom Protokoll generierte Ansprüche zu entfernen.

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

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Erforderliche Komponenten

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

Geben Sie die Liste der Berechtigungen an, die vom Anbieter abgerufen werden sollen, indem Sie die <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>angeben. Authentifizierungs Bereiche für allgemeine externe Anbieter werden in der folgenden Tabelle angezeigt.

| anbieter  | Gültigkeitsbereich                                                            |
| --------- | ---------------------------------------------------------------- |
| Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |

In der Beispiel-APP wird der `userinfo.profile` Bereich von Google automatisch durch das Framework hinzugefügt, wenn <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> auf dem <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>aufgerufen wird. Wenn die APP zusätzliche Bereiche benötigt, fügen Sie Sie den Optionen hinzu. Im folgenden Beispiel wird der Google `https://www.googleapis.com/auth/user.birthday.read`-Bereich hinzugefügt, um den Geburtstag eines Benutzers abzurufen:

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a>Zuordnen von Benutzerdaten Schlüsseln und Erstellen von Ansprüchen

Geben Sie in den Optionen des Anbieters für jeden Schlüssel/Unterschlüssel in den JSON-Benutzerdaten des externen Anbieters eine <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> oder <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> an, damit die APP-Identität bei der Anmeldung gelesen werden kann. Weitere Informationen zu Anspruchs Typen finden Sie unter <xref:System.Security.Claims.ClaimTypes>.

Die Beispiel-App erstellt die Ansprüche "locale (`urn:google:locale`)" und "Image" (`urn:google:picture`) aus den `locale` und `picture` Schlüsseln in Google User Data:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`wird eine <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) mit <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>bei der App angemeldet. Während des Anmeldevorgangs können die <xref:Microsoft.AspNetCore.Identity.UserManager%601> eine `ApplicationUser` Ansprüche für Benutzerdaten speichern, die über die <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>verfügbar sind.

In der Beispiel-App richtet `OnPostConfirmationAsync` (*Account/externzuweisung. cshtml. cs*) die Gebiets Schema-(`urn:google:locale`) und Bild (`urn:google:picture`)-Ansprüche für die signierte `ApplicationUser`ein, einschließlich eines Anspruchs für <xref:System.Security.Claims.ClaimTypes.GivenName>:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

Standardmäßig werden die Ansprüche eines Benutzers im Authentifizierungs Cookie gespeichert. Wenn das Authentifizierungs Cookie zu groß ist, kann dies dazu führen, dass die APP fehlschlägt:

* Der Browser erkennt, dass der Cookie-Header zu lang ist.
* Die Gesamtgröße der Anforderung ist zu groß.

Wenn eine große Menge an Benutzerdaten für die Verarbeitung von Benutzer Anforderungen erforderlich ist:

* Beschränken Sie die Anzahl und die Größe von Benutzer Ansprüchen für die Anforderungs Verarbeitung auf das, was die APP benötigt.
* Verwenden Sie eine benutzerdefinierte <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> für die <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> der cookeauthentifizierungs-Middleware, um die Identitäts übergreifende Identität zu speichern. Bewahren Sie große Mengen an Identitätsinformationen auf dem Server auf, und senden Sie nur einen kleinen Sitzungs-ID-Schlüssel an den Client.

## <a name="save-the-access-token"></a>Speichern des Zugriffs Tokens

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> definiert, ob Zugriffs-und Aktualisierungs Token nach einer erfolgreichen Autorisierung in der <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> gespeichert werden sollen. `SaveTokens` ist standardmäßig auf `false` festgelegt, um die Größe des endgültigen Authentifizierungs Cookies zu verringern.

In der Beispiel-APP wird der Wert von `SaveTokens` auf `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>festgelegt:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

Wenn `OnPostConfirmationAsync` ausgeführt wird, speichern Sie das Zugriffs Token ([externzuweisung-Info. authenticationtokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) vom externen Anbieter im `AuthenticationProperties`der `ApplicationUser`.

Die Beispiel-App speichert das Zugriffs Token in `OnPostConfirmationAsync` (Neue Benutzerregistrierung) und `OnGetCallbackAsync` (zuvor registrierter Benutzer) in *Account/externzuzugriffgin. cshtml. cs*:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a>Vorgehensweise beim Hinzufügen zusätzlicher benutzerdefinierter Token

Um zu veranschaulichen, wie Sie ein benutzerdefiniertes Token hinzufügen, das als Teil von `SaveTokens`gespeichert wird, fügt die Beispiel-App eine <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> mit dem aktuellen <xref:System.DateTime> für ein [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`hinzu:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a>Erstellen und Hinzufügen von Ansprüchen

Das Framework bietet allgemeine Aktionen und Erweiterungs Methoden zum Erstellen und Hinzufügen von Ansprüchen zur Auflistung. Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> und <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.

Benutzer können benutzerdefinierte Aktionen definieren, indem Sie von <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> ableiten und die abstrakte <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*>-Methode implementieren.

Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.

## <a name="removal-of-claim-actions-and-claims"></a>Entfernen von Anspruchs Aktionen und Ansprüchen

[Claimaktioncollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) entfernt alle Anspruchs Aktionen für die angegebene <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> aus der Auflistung. [Claimaktioncollectionmapextensions. deleteclaim (claimaktioncollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) löscht einen Anspruch der angegebenen <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> aus der Identität. <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> wird hauptsächlich mit [OpenID Connect (oidc)](/azure/active-directory/develop/v2-protocols-oidc) verwendet, um vom Protokoll generierte Ansprüche zu entfernen.

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

## <a name="additional-resources"></a>Weitere Ressourcen

* [dotnet/aspnetcore Engineering socialsample-App](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample) &ndash; die verknüpfte Beispiel-App befindet sich im `master` Engineering-Branch des [dotnet/aspnetcore-GitHub-](https://github.com/dotnet/AspNetCore) Repository. Der `master` Branch enthält Code für die nächste Version von ASP.net Core unteraktive Entwicklung. Um eine Version der Beispiel-App für eine veröffentlichte Version von ASP.net Core anzuzeigen, wählen Sie in der Dropdown Liste **Verzweigung** einen releasebranch aus (z. b. `release/{X.Y}`).

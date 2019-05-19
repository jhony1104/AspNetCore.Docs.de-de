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
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a>Beibehalten von zusätzliche Ansprüche und Token von externen Anbietern in ASP.NET Core

Von [Luke Latham](https://github.com/guardrex)

ASP.NET Core-Apps kann zusätzliche Ansprüche und Token von externen Authentifizierungsanbietern, z. B. Facebook, Google, Microsoft und Twitter herstellen. Jeder Anbieter werden andere Informationen zu Benutzern auf der Plattform, aber die Muster zum Empfangen und Transformieren von Daten des Benutzers in zusätzliche Ansprüche ist das gleiche.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Vorraussetzungen

Entscheiden Sie, welche externen Authentifizierungsanbietern, um in der app zu unterstützen. Für jeden Anbieter registrieren der app, und erhalten eine Client-ID und den geheimen Clientschlüssel. Weitere Informationen finden Sie unter <xref:security/authentication/social/index>. Die beispielanwendung verwendet die [Google-Authentifizierungsanbieter](xref:security/authentication/google-logins).

## <a name="set-the-client-id-and-client-secret"></a>Legen Sie die Client-ID und geheimer Clientschlüssel

Der OAuth-Authentifizierungsanbieter richtet eine Vertrauensstellung mit einer app mithilfe einer Client-ID und den geheimen Clientschlüssel. Client-ID und des geheimen clientschlüssels werden erstellt, für die app durch den externen Authentifizierungsanbieter. wenn die app mit dem Anbieter registriert ist. Jeder externe Anbieter von der app verwendeten muss unabhängig voneinander mit Client-ID und clientgeheimnis des Anbieters konfiguriert werden. Weitere Informationen finden Sie unter den Themen der externe Authentifizierung-Anbieter, die für Ihr Szenario gelten:

* [Facebook-Authentifizierung](xref:security/authentication/facebook-logins)
* [Google-Authentifizierung](xref:security/authentication/google-logins)
* [Microsoft-Authentifizierung](xref:security/authentication/microsoft-logins)
* [Twitter-Authentifizierung](xref:security/authentication/twitter-logins)
* [Andere Authentifizierungsanbieter](xref:security/authentication/otherlogins)
* [OpenIdConnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

Die Beispiel-app wird der Google-Authentifizierungsanbieter konfiguriert, mit einer Client-ID und clientgeheimnis, die von Google bereitgestellt wird:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a>Den Authentifizierungsbereich einrichten

Geben Sie die Liste der Berechtigungen zum Abrufen des Anbieters durch Angabe der <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>. Authentifizierung-Bereiche für allgemeine externe Anbieter werden in der folgenden Tabelle angezeigt.

| Anbieter  | Bereich                                                            |
| --------- | ---------------------------------------------------------------- |
| Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |

In der Beispiel-app, Google `userinfo.profile` Bereich wird automatisch hinzugefügt, durch das Framework beim <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> aufgerufen wird, auf die <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>. Wenn die app zusätzliche Bereiche erfordert, fügen sie die Optionen hinzu. Im folgenden Beispiel das Google `https://www.googleapis.com/auth/user.birthday.read` Bereich wird hinzugefügt, um Geburtstag des Benutzers abzurufen:

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a>Ordnen Sie die Schlüssel für den Benutzer Daten und Erstellen von Ansprüchen

Geben Sie in den Optionen des Anbieters ein <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> oder <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> für jeden Schlüssel/Unterschlüssel in der externe Anbieter JSON-Eingabe der Benutzerdaten für den app-Identität bei der Anmeldung zu lesen. Weitere Informationen zu den Anspruchstypen, finden Sie unter <xref:System.Security.Claims.ClaimTypes>.

Die Beispiel-app erstellt Gebietsschema (`urn:google:locale`) und das Bild (`urn:google:picture`) von Ansprüchen aus der `locale` und `picture` Schlüssel in der Google-Benutzerdaten:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

In <xref:Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync*>, <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) wird in die app signiert <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>. Während des Anmeldeprozesses die <xref:Microsoft.AspNetCore.Identity.UserManager%601> speichern kann ein `ApplicationUser` Ansprüche für verfügbaren Benutzerdaten aus der <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.

In der Beispiel-app `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) legt das Gebietsschema (`urn:google:locale`) und das Bild (`urn:google:picture`) Ansprüche für die signierte in `ApplicationUser`, einschließlich einen Anspruch für <xref:System.Security.Claims.ClaimTypes.GivenName> :

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

Standardmäßig werden die Ansprüche des Benutzers im Authentifizierungscookie gespeichert. Wenn das Authentifizierungscookie zu groß ist, können sie die app fehlschlägt, weil verursachen:

* Der Browser erkennt, dass der Cookie-Header zu lang ist.
* Die Gesamtgröße der Anforderung ist zu groß.

Wenn eine große Menge von Benutzerdaten zum Verarbeiten von benutzeranforderungen erforderlich ist:

* Beschränken Sie die Anzahl und Größe von benutzeransprüchen für die anforderungsverarbeitung ausgeführt werden, um nur das, was der app erforderlich sind.
* Verwenden Sie eine benutzerdefinierte <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> für die Cookie-Authentifizierung-Middleware <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> auf Identität anforderungsübergreifend gespeichert. Behalten Sie große Mengen an Informationen zur Identität auf dem Server und nur einen kleinen Sitzungsschlüssel für den Bezeichner an den Client gesendet.

## <a name="save-the-access-token"></a>Speichern Sie das Zugriffstoken

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> definiert, ob die Zugriffs-und Aktualisierungstoken gespeichert werden sollen, in der <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> nach einer erfolgreichen Autorisierung. `SaveTokens` nastaven NA hodnotu `false` werden standardmäßig die Größe des endgültigen Authentifizierungscookies zu reduzieren.

Die Beispiel-app wird der Wert der `SaveTokens` zu `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

Wenn `OnPostConfirmationAsync` ausgeführt wird, speichern Sie das Zugriffstoken ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) vom externen Anbieter in der `ApplicationUser`des `AuthenticationProperties`.

Die Beispiel-app speichert das Zugriffstoken in `OnPostConfirmationAsync` (Registrierung neuer Benutzer) und `OnGetCallbackAsync` (zuvor registrierte Benutzer) in *Account/ExternalLogin.cshtml.cs*:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a>So fügen Sie zusätzliche benutzerdefinierte Token hinzu:

Veranschaulicht, wie ein benutzerdefiniertes Token an, hinzufügen, die als Teil der gespeichert wird `SaveTokens`, fügt die Beispiel-app eine <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> mit dem aktuellen <xref:System.DateTime> für eine [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) von `TicketCreated`:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-28)]

## <a name="creating-and-adding-claims"></a>Erstellen und Hinzufügen von Ansprüchen

Das Framework stellt häufig verwendete Aktionen und Erweiterungsmethoden zum Erstellen und Hinzufügen von Ansprüchen, die der Auflistung. Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> und <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.

Benutzer können benutzerdefinierte Aktionen definieren, durch Ableiten von <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> und Implementierung der abstrakten <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> Methode.

Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.

## <a name="removal-of-claim-actions-and-claims"></a>Entfernen von Anspruchs-Aktionen und Ansprüche

[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) entfernt alle Anspruch Aktionen für den angegebenen <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> aus der Auflistung. [ClaimActionCollectionMapExtensions.DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) Löscht einen Anspruch, der den angegebenen <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> aus der Identität. <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> Dient in erster Linie mit [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) Protokoll generierten Ansprüche zu entfernen.

## <a name="sample-app-output"></a>Beispiel-app-Ausgabe

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

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [ASPNET/AspNetCore engineering SocialSample app](https://github.com/aspnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample) &ndash; die verknüpften Beispiel-app befindet sich auf die [Aspnet/AspNetCore-GitHub-Repository](https://github.com/aspnet/AspNetCore) `master` engineering Branch. Die `master` Branch enthält Code in der aktiven Entwicklung für die nächste Version von ASP.NET Core. Um eine Version der Beispiel-app für eine veröffentlichte Version von ASP.NET Core anzuzeigen, verwenden die **Branch** Dropdown-Liste, um einen releasebranch auszuwählen (z. B. `release/2.2`).

---
title: Beibehalten von zusätzliche Ansprüche und Token von externen Anbietern in ASP.NET Core
author: guardrex
description: Erfahren Sie, wie zusätzliche Ansprüche und Token von externen Anbietern herzustellen.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2018
uid: security/authentication/social/additional-claims
ms.openlocfilehash: 49c323fab64bd4ea52dd1d8cf2e43a79d4d0d0dc
ms.sourcegitcommit: a1c43150ed46aa01572399e8aede50d4668745ca
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58327351"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a>Beibehalten von zusätzliche Ansprüche und Token von externen Anbietern in ASP.NET Core

Von [Luke Latham](https://github.com/guardrex)

ASP.NET Core-Apps kann zusätzliche Ansprüche und Token von externen Authentifizierungsanbietern, z. B. Facebook, Google, Microsoft und Twitter herstellen. Jeder Anbieter werden andere Informationen zu Benutzern auf der Plattform, aber die Muster zum Empfangen und Transformieren von Daten des Benutzers in zusätzliche Ansprüche ist das gleiche.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Vorraussetzungen

Entscheiden Sie, welche externen Authentifizierungsanbietern, um in der app zu unterstützen. Für jeden Anbieter registrieren der app, und erhalten eine Client-ID und den geheimen Clientschlüssel. Weitere Informationen finden Sie unter <xref:security/authentication/social/index>. Die [Beispiel-app](#sample-app-instructions) verwendet die [Google-Authentifizierungsanbieter](xref:security/authentication/google-logins).

## <a name="set-the-client-id-and-client-secret"></a>Legen Sie die Client-ID und geheimer Clientschlüssel

Der OAuth-Authentifizierungsanbieter richtet eine Vertrauensstellung mit einer app mithilfe einer Client-ID und den geheimen Clientschlüssel. Client-ID und des geheimen clientschlüssels werden erstellt, für die app durch den externen Authentifizierungsanbieter. wenn die app mit dem Anbieter registriert ist. Jeder externe Anbieter von der app verwendeten muss unabhängig voneinander mit Client-ID und clientgeheimnis des Anbieters konfiguriert werden. Weitere Informationen finden Sie unter den Themen der externe Authentifizierung-Anbieter, die für Ihr Szenario gelten:

* [Facebook-Authentifizierung](xref:security/authentication/facebook-logins)
* [Google-Authentifizierung](xref:security/authentication/google-logins)
* [Microsoft-Authentifizierung](xref:security/authentication/microsoft-logins)
* [Twitter-Authentifizierung](xref:security/authentication/twitter-logins)
* [Andere Authentifizierungsanbieter](xref:security/authentication/otherlogins)
* [OpenIdConnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

Die Beispiel-app wird der Google-Authentifizierungsanbieter konfiguriert, mit einer Client-ID und clientgeheimnis, die von Google bereitgestellt wird:

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,6)]

## <a name="establish-the-authentication-scope"></a>Den Authentifizierungsbereich einrichten

Geben Sie die Liste der Berechtigungen zum Abrufen des Anbieters durch Angabe der <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>. Authentifizierung-Bereiche für allgemeine externe Anbieter werden in der folgenden Tabelle angezeigt.

| Anbieter  | Bereich                                                            |
| --------- | ---------------------------------------------------------------- |
| Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/plus.login`                     |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |

Die Beispiel-app fügt Google `plus.login` Bereich Google +-Anmeldung Berechtigungen anfordern:

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=7)]

## <a name="map-user-data-keys-and-create-claims"></a>Ordnen Sie die Schlüssel für den Benutzer Daten und Erstellen von Ansprüchen

Geben Sie in den Optionen des Anbieters ein <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> für jeden Schlüssel in der externe Anbieter JSON-Eingabe der Benutzerdaten für den app-Identität bei der Anmeldung zu lesen. Weitere Informationen zu den Anspruchstypen, finden Sie unter <xref:System.Security.Claims.ClaimTypes>.

Die Beispiel-app erstellt ein <xref:System.Security.Claims.ClaimTypes.Gender> Anspruch von der `gender` -Schlüssels im Google-Benutzerdaten:

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=8)]

In <xref:Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync*>, <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) wird in die app signiert <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>. Während des Anmeldeprozesses die <xref:Microsoft.AspNetCore.Identity.UserManager%601> speichern kann ein `ApplicationUser` -Anspruch für verfügbaren Benutzerdaten aus der <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.

In der Beispiel-app `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) Richtet eine <xref:System.Security.Claims.ClaimTypes.Gender> -Anspruch für den angemeldeten in `ApplicationUser`:

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=30-31)]

## <a name="save-the-access-token"></a>Speichern Sie das Zugriffstoken

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> definiert, ob die Zugriffs-und Aktualisierungstoken gespeichert werden sollen, in der <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> nach einer erfolgreichen Autorisierung. `SaveTokens` nastaven NA hodnotu `false` werden standardmäßig die Größe des endgültigen Authentifizierungscookies zu reduzieren.

Die Beispiel-app wird der Wert der `SaveTokens` zu `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=9)]

Wenn `OnPostConfirmationAsync` ausgeführt wird, speichern Sie das Zugriffstoken ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) vom externen Anbieter in der `ApplicationUser`des `AuthenticationProperties`.

Die Beispiel-app speichert das Zugriffstoken in:

* `OnPostConfirmationAsync` &ndash; Führt für die Registrierung neuer Benutzer.
* `OnGetCallbackAsync` &ndash; Ausgeführt wird, wenn ein zuvor registrierter Benutzer bei der app anmeldet.

*Account/ExternalLogin.cshtml.cs*:

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=34-35)]

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnGetCallbackAsync&highlight=31-32)]

## <a name="how-to-add-additional-custom-tokens"></a>So fügen Sie zusätzliche benutzerdefinierte Token hinzu:

Veranschaulicht, wie ein benutzerdefiniertes Token an, hinzufügen, die als Teil der gespeichert wird `SaveTokens`, fügt die Beispiel-app eine <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> mit dem aktuellen <xref:System.DateTime> für eine [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) von `TicketCreated`:

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=10-21)]

## <a name="sample-app-instructions"></a>Beispiel-app-Anweisungen

Die Beispiel-app veranschaulicht, wie Sie:

* Rufen Sie das Geschlecht des Benutzers von Google und speichern Sie einen Geschlecht-Anspruch mit dem Wert.
* Das Google-Zugriffstoken des Benutzers Store `AuthenticationProperties`.

So verwenden Sie die Beispiel-app

1. Registrieren der app, und erhalten Sie eine gültige Client-ID und geheimer Clientschlüssel für die Google-Authentifizierung. Weitere Informationen finden Sie unter <xref:security/authentication/google-logins>.
1. Geben Sie den Client-ID und geheimer Clientschlüssel der App in der <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> von `Startup.ConfigureServices`.
1. Führen Sie die app aus, und fordern Sie die Seite Meine Ansprüche. Wenn der Benutzer angemeldet ist nicht, leitet die app an Google. Melden Sie sich mit Google. Google leitet den Benutzer zurück an die app (`/Home/MyClaims`). Der Benutzer wird authentifiziert, und Laden der Seite Meine Ansprüche. Der Gender-Anspruch ist unter **Benutzeransprüche** mit dem Wert von Google abgerufen wird. Das Zugriffstoken wird in der **Authentifizierungseigenschaften**.

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

---
title: Konfigurieren von ASP.net CoreIdentity
author: AdrienTorris
description: Verstehen Identity Sie ASP.net Core Standardwerte, und erfahren Sie, wie Sie Identity Eigenschaften für die Verwendung von benutzerdefinierten Werten konfigurieren.
ms.author: riande
ms.date: 02/11/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity-configuration
ms.openlocfilehash: 95c19b671602b45ba217dcb551110854cbbee359
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408967"
---
# <a name="configure-aspnet-core-identity"></a>Konfigurieren von ASP.net CoreIdentity

In ASP.net Core werden Identity Standardwerte für Einstellungen verwendet, z. b. Kenn Wort Richtlinie, Sperrung und Konfiguration von Cookies. Diese Einstellungen können in der-Klasse überschrieben werden `Startup` .

## <a name="identity-options"></a>IdentityOptionen

Die [identityoptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) -Klasse stellt die Optionen dar, die verwendet werden können, um das System zu konfigurieren Identity . `IdentityOptions`muss **nach** dem Aufrufen von oder festgelegt werden `AddIdentity` `AddDefaultIdentity` .

### <a name="claims-identity"></a>ClaimsIdentity

" [Identityoptions. ClaimsIdentity](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity) " gibt die " [claimsidentityoptions](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) " mit den Eigenschaften an, die in der folgenden Tabelle aufgeführt sind.

| Eigenschaft | Beschreibung | Standard |
| -------- | ----------- | :-----: |
| [Roleclaimtype](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.roleclaimtype) | Ruft den für einen Rollen Anspruch verwendeten Anspruchstyp ab oder legt ihn fest. | [ClaimTypes. Role](/dotnet/api/system.security.claims.claimtypes.role) |
| [Securitystampclaimtype](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.securitystampclaimtype) | Ruft den für den sicherheitstokenanspruch verwendeten Anspruchstyp ab oder legt ihn fest. | `AspNet.Identity.SecurityStamp` |
| [Useridclaimtype](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.useridclaimtype) | Ruft den für den benutzerbezeichneranspruch verwendeten Anspruchstyp ab oder legt ihn fest. | [ClaimTypes. NameIdentifier](/dotnet/api/system.security.claims.claimtypes.nameidentifier) |
| [Usernameclaimtype](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.usernameclaimtype) | Ruft den für den Benutzernamens Anspruch verwendeten Anspruchstyp ab oder legt ihn fest. | [ClaimTypes.Name](/dotnet/api/system.security.claims.claimtypes.name) |

### <a name="lockout"></a>Sperrung

Die Sperre wird in der [passwordsigninasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_) -Methode festgelegt:

[!code-csharp[](identity-configuration/sample/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=9)]

Der vorangehende Code basiert auf der `Login` Identity Vorlage. 

Sperrungs Optionen werden in festgelegt `StartUp.ConfigureServices` :

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_lock)]

Der vorangehende Code legt die [identityoptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) - [lockoutoptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) mit den Standardwerten fest.

Bei erfolgreicher Authentifizierung wird die Anzahl fehlerhafter Zugriffsversuche zurückgesetzt, und die Uhr wird zurückgesetzt.

[Identityoptions. Lockout](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout) gibt die [lockoutoptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) mit den in der Tabelle angezeigten Eigenschaften an.

| Eigenschaft | Beschreibung | Standard |
| -------- | ----------- | :-----: |
| ["Zuweisen"](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.allowedfornewusers) | Bestimmt, ob ein neuer Benutzer gesperrt werden kann. | `true` |
| [Defaultlockouttimespan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan) | Die Zeitspanne, für die ein Benutzer gesperrt wird, wenn eine Sperre auftritt. | 5 Minuten |
| [Maxfailedaccessversuchs](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) | Die Anzahl der fehlgeschlagenen Zugriffsversuche, bis ein Benutzer gesperrt ist, wenn die Sperre aktiviert ist. | 5 |

### <a name="password"></a>Kennwort

Standardmäßig Identity erfordert, dass Kenn Wörter einen Großbuchstaben, einen Kleinbuchstaben, eine Ziffer und ein nicht alphanumerisches Zeichen enthalten. Kenn Wörter müssen mindestens sechs Zeichen lang sein. [Passwordoptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) kann in festgelegt werden `Startup.ConfigureServices` .

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_pw)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-37,50-52)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-65,84)]

::: moniker-end

[Identityoptions. Password](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password) gibt die [passwordoptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) -Eigenschaft mit den Eigenschaften an, die in der Tabelle angezeigt werden.

::: moniker range=">= aspnetcore-2.0"

| Eigenschaft | Beschreibung | Standard |
| -------- | ----------- | :-----: |
| [Requirements-Ziffer](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | Erfordert eine Zahl zwischen 0-9 und dem Kennwort. | `true` |
| [Requirements dlength](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | Die Mindestlänge des Kennworts. | 6 |
| [Requirements lowercase](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | Erfordert einen Kleinbuchstaben im Kennwort. | `true` |
| [Anforderungs alphanumerisch](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | Erfordert ein nicht alphanumerisches Zeichen im Kennwort. | `true` |
| [Requirements duniquechars](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireduniquechars) | Gilt nur für ASP.net Core 2,0 oder höher.<br><br> Erfordert die Anzahl der unterschiedlichen Zeichen im Kennwort. | 1 |
| [Erforderlich (Großbuchstaben)](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | Erfordert ein Großbuchstaben im Kennwort. | `true` |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

| Eigenschaft | Beschreibung | Standard |
| -------- | ----------- | :-----: |
| [Requirements-Ziffer](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | Erfordert eine Zahl zwischen 0-9 und dem Kennwort. | `true` |
| [Requirements dlength](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | Die Mindestlänge des Kennworts. | 6 |
| [Requirements lowercase](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | Erfordert einen Kleinbuchstaben im Kennwort. | `true` |
| [Anforderungs alphanumerisch](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | Erfordert ein nicht alphanumerisches Zeichen im Kennwort. | `true` |
| [Erforderlich (Großbuchstaben)](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | Erfordert ein Großbuchstaben im Kennwort. | `true` |

::: moniker-end

### <a name="sign-in"></a>Anmeldung

Mit dem folgenden Code werden Einstellungen festgelegt `SignIn` (auf Standardwerte):

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_si)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-30,44-46,50-52)] 

::: moniker-end

[Identityoptions. SignIn](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin) gibt die [signinoptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) -Eigenschaft mit den in der Tabelle angezeigten Eigenschaften an.

| Eigenschaft | Beschreibung | Standard |
| -------- | ----------- | :-----: |
| ["Requirements confirmede Mail"](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedemail) | Erfordert eine bestätigte e-Mail-Adresse für die Anmeldung. | `false` |
| [Requirements confirmedphonenumber](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedphonenumber) | Erfordert eine bestätigte Telefonnummer, um sich anzumelden. | `false` |

### <a name="tokens"></a>Token

[Identityoptions. Tokens](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens) gibt die [tokenoptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) mit den Eigenschaften an, die in der Tabelle angezeigt werden.

|                                                        Eigenschaft                                                         |                                                                                      Beschreibung                                                                                      |
|-------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     [Authenticenortokenprovider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.authenticatortokenprovider)     |                                       Ruft den ab `AuthenticatorTokenProvider` , der zum Überprüfen von zweistufigen Anmeldungen mit einem Authentifikator verwendet wird, oder legt diesen fest.                                       |
|       [Changeemailstokenprovider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changeemailtokenprovider)       |                                     Ruft den `ChangeEmailTokenProvider` zum Generieren von Token ab, die in e-Mail-Bestätigungs-e-Mails verwendet werden.                                     |
| [Changephonenumberumkenprovider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changephonenumbertokenprovider) |                                      Ruft den ab `ChangePhoneNumberTokenProvider` , der zum Generieren von Token verwendet wird, die beim Ändern von Telefonnummern verwendet werden                                      |
| [Emailconfirmationstokenprovider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.emailconfirmationtokenprovider) |                                             Ruft den Tokenanbieter ab, der zum Generieren von Token verwendet wird, die in e-Mail-Konto Bestätigungs                                              |
|     [Passwordresetstokenprovider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.passwordresettokenprovider)     | Ruft den [iusertwofaktortokenprovider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) ab, der zum Generieren von Token verwendet wird, die in e-Mails zur Kenn Wort Zurücksetzung verwendet werden |
|                    [Providermap](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.providermap)                    |                Wird verwendet, um einen [benutzertokenanbieter](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor) mit dem Schlüssel zu erstellen, der als Name des Anbieters verwendet wird.                 |

### <a name="user"></a>Benutzer

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_user)]

[Identityoptions. User](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user) gibt die [User Options](/dotnet/api/microsoft.aspnetcore.identity.useroptions) mit den Eigenschaften an, die in der Tabelle angezeigt werden.

| Eigenschaft | Beschreibung | Standard |
| -------- | ----------- | :-----: |
| [Zuordnung von "Zuweisung"](/dotnet/api/microsoft.aspnetcore.identity.useroptions.allowedusernamecharacters) | Zulässige Zeichen im Benutzernamen. | abcdefghijklmnopqrstuvwxyz<br>ABCDEFGHIJKLMNOPQRSTUVWXYZ<br>0123456789<br>-.\_@+ |
| [Requirements-e-Mail](/dotnet/api/microsoft.aspnetcore.identity.useroptions.requireuniqueemail) | Erfordert, dass jeder Benutzer eine eindeutige e-Mail-Adresse hat. | `false` |

### <a name="cookie-settings"></a>Cookie-Einstellungen

Konfigurieren Sie das Cookie der app in `Startup.ConfigureServices` . " [Applireapplicationcookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__) " muss **nach** dem Aufrufen von oder aufgerufen werden `AddIdentity` `AddDefaultIdentity` .

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_cookie)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?name=snippet_configurecookie)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-59,72-80,84)]

::: moniker-end

Weitere Informationen finden Sie unter [cookieauthenticationoptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions).

## <a name="password-hasher-options"></a>Optionen für Kennwort Hasher

<xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions>Ruft Optionen für Kenn Wort Hashwert ab und legt Sie fest.

| Option | BESCHREIBUNG |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> | Der beim hashup neuer Kenn Wörter verwendete Kompatibilitätsmodus. Wird standardmäßig auf <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3> festgelegt. Das erste Byte eines Hash Kennworts, das als *Format Marker*bezeichnet wird, gibt die Version des Hash Algorithmus an, mit dem das Kennwort als Hash verwendet wird. Wenn ein Kennwort mit einem Hash überprüft wird, <xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*> wählt die Methode den richtigen Algorithmus basierend auf dem ersten Byte aus. Ein Client kann authentifiziert werden, unabhängig davon, welche Version des Algorithmus zum Hashen des Kennworts verwendet wurde. Das Festlegen des Kompatibilitätsmodus wirkt sich auf das hashup *neuer Kenn Wörter*aus. |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> | Die Anzahl der Iterationen, die beim Hashwert von Kenn Wörtern mithilfe von PBKDF2 verwendet werden. Dieser Wert wird nur verwendet, wenn <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> auf festgelegt ist <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3> . Der Wert muss eine positive ganze Zahl sein, und der Standardwert ist `10000` . |

Im folgenden Beispiel <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> wird auf in festgelegt `12000` `Startup.ConfigureServices` :

```csharp
// using Microsoft.AspNetCore.Identity;

services.Configure<PasswordHasherOptions>(option =>
{
    option.IterationCount = 12000;
});
```

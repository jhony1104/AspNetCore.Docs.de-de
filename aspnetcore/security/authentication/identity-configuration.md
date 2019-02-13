---
title: Konfigurieren von ASP.NET Core-Identität
author: AdrienTorris
description: Verstehen Sie ASP.NET Core Identity-Standardwerte, und erfahren Sie, wie so konfigurieren Sie die Identitätseigenschaften, um benutzerdefinierte Werte verwenden.
ms.author: riande
ms.date: 02/11/2019
uid: security/authentication/identity-configuration
ms.openlocfilehash: 3213f669cbfccdcda7cc7c0142b8101e696678e6
ms.sourcegitcommit: af8a6eb5375ef547a52ffae22465e265837aa82b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56159512"
---
# <a name="configure-aspnet-core-identity"></a>Konfigurieren von ASP.NET Core-Identität

ASP.NET Core Identity verwendet Standardwerte für Einstellungen wie z. B. Kennwortrichtlinien, kontosperrung und Cookie-Konfiguration. Diese Einstellungen können überschrieben werden, der `Startup` Klasse.

## <a name="identity-options"></a>Identity-Optionen

Die [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) -Klasse stellt die Optionen, die zum Konfigurieren des Identity-Systems verwendet werden können. `IdentityOptions` nutno nastavit **nach** Aufrufen `AddIdentity` oder `AddDefaultIdentity`.

### <a name="claims-identity"></a>Anspruchsbasierte Identität

[IdentityOptions.ClaimsIdentity](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity) gibt an, die [ClaimsIdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) mit den Eigenschaften, die in der folgenden Tabelle gezeigt.

| Eigenschaft | Beschreibung | Standard |
| -------- | ----------- | :-----: |
| [RoleClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.roleclaimtype) | Übernimmt oder bestimmt die für einen Rollenanspruch verwendeten Anspruchstyp. | [ClaimTypes.Role](/dotnet/api/system.security.claims.claimtypes.role) |
| [SecurityStampClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.securitystampclaimtype) | Übernimmt oder bestimmt die für den Stempel sicherheitsanspruch verwendeten Anspruchstyp. | `AspNet.Identity.SecurityStamp` |
| [UserIdClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.useridclaimtype) | Übernimmt oder bestimmt die für die Benutzer-ID-Anspruch verwendeten Anspruchstyp. | [ClaimTypes.NameIdentifier](/dotnet/api/system.security.claims.claimtypes.nameidentifier) |
| [UserNameClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.usernameclaimtype) | Übernimmt oder bestimmt den Anspruchstyp für den Namensanspruch des Benutzers verwendet. | [ClaimTypes.Name](/dotnet/api/system.security.claims.claimtypes.name) |

### <a name="lockout"></a>Kontosperrung

Sperre wird festgelegt, der [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_) Methode:

[!code-csharp[](identity-configuration/sample/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=9)]

Der vorangehende Code basiert auf der `Login` Identity-Vorlage. 

Sperre Optionen werden in festgelegt `StartUp.ConfigureServices`:

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_lock)]

Im obigen Code wird die [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) mit Standardwerten.

Eine erfolgreiche Authentifizierung setzt die Anzahl der fehlgeschlagenen Zugriffe Versuche und setzt die Uhr.

[IdentityOptions.Lockout](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout) gibt an, die [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) mit den Eigenschaften, die in der Tabelle dargestellt.

| Eigenschaft | Beschreibung | Standard |
| -------- | ----------- | :-----: |
| [AllowedForNewUsers](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.allowedfornewusers) | Bestimmt, ob ein neuer Benutzer gesperrt werden kann. | `true` |
| [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan) | Die Zeitspanne ist ein Benutzer gesperrt, wenn eine Sperre auftritt. | 5 Minuten |
| [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) | Die Anzahl fehlerhafter Zugriffsversuche, bis ein Benutzer gesperrt ist, wenn die Sperre aktiviert ist. | 5 |

### <a name="password"></a>Kennwort

Standardmäßig erfordert Identität an, dass die Kennwörter, einen Großbuchstaben, Kleinbuchstaben, eine Ziffer und ein nicht alphanumerisches Zeichen enthalten. Kennwörter müssen mindestens sechs Zeichen lang sein. [PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) kann festgelegt werden, `Startup.ConfigureServices`.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_pw)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-37,50-52)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-65,84)]

::: moniker-end

[IdentityOptions.Password](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password) gibt an, die [PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) mit den Eigenschaften, die in der Tabelle dargestellt.

::: moniker range=">= aspnetcore-2.0"

| Eigenschaft | Beschreibung | Standard |
| -------- | ----------- | :-----: |
| [RequireDigit](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | Erfordert eine Zahl zwischen 0-9, das Kennwort an. | `true` |
| [RequiredLength](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | Die Mindestlänge des Kennworts. | 6 |
| [RequireLowercase](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | Erfordert ein Kleinbuchstabe, das Kennwort an. | `true` |
| [RequireNonAlphanumeric](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | Ist ein nicht alphanumerisches Zeichen im Kennwort erforderlich. | `true` |
| [RequiredUniqueChars](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireduniquechars) | Gilt nur für ASP.NET Core 2.0 oder höher aus.<br><br> Ist die Anzahl der unterschiedlichen Zeichen im Kennwort erforderlich. | 1 |
| [RequireUppercase](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | Ist ein Großbuchstabe Zeichen im Kennwort erforderlich. | `true` |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

| Eigenschaft | Beschreibung | Standard |
| -------- | ----------- | :-----: |
| [RequireDigit](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | Erfordert eine Zahl zwischen 0-9, das Kennwort an. | `true` |
| [RequiredLength](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | Die Mindestlänge des Kennworts. | 6 |
| [RequireLowercase](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | Erfordert ein Kleinbuchstabe, das Kennwort an. | `true` |
| [RequireNonAlphanumeric](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | Ist ein nicht alphanumerisches Zeichen im Kennwort erforderlich. | `true` |
| [RequireUppercase](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | Ist ein Großbuchstabe Zeichen im Kennwort erforderlich. | `true` |

::: moniker-end

### <a name="sign-in"></a>Anmeldung

Im folgenden code wird `SignIn` Einstellungen (Standardwerte):

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_si)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-30,44-46,50-52)] 

::: moniker-end

[IdentityOptions.SignIn](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin) gibt an, die [SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) mit den Eigenschaften, die in der Tabelle dargestellt.

| Eigenschaft | Beschreibung | Standard |
| -------- | ----------- | :-----: |
| [RequireConfirmedEmail](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedemail) | Erfordert eine bestätigte e-Mail-Adresse für die Anmeldung an. | `false` |
| [RequireConfirmedPhoneNumber](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedphonenumber) | Erfordert eine bestätigte Telefonnummer für die Anmeldung an. | `false` |

### <a name="tokens"></a>tokens

[IdentityOptions.Tokens](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens) gibt an, die [TokenOptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) mit den Eigenschaften, die in der Tabelle dargestellt.


|                                                        Eigenschaft                                                         |                                                                                      Beschreibung                                                                                      |
|-------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     [AuthenticatorTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.authenticatortokenprovider)     |                                       Übernimmt oder bestimmt den `AuthenticatorTokenProvider` verwendet, um zwei-Faktor-Anmeldungen mit einem Authentifikator überprüfen.                                       |
|       [ChangeEmailTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changeemailtokenprovider)       |                                     Übernimmt oder bestimmt den `ChangeEmailTokenProvider` verwendet zum Generieren von Token, die in e-Mail-Adresse ändern Bestätigungs-e-Mails verwendet.                                     |
| [ChangePhoneNumberTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changephonenumbertokenprovider) |                                      Übernimmt oder bestimmt die `ChangePhoneNumberTokenProvider` verwendet zum Generieren von Tokens verwendet, wenn die Telefonnummern zu ändern.                                      |
| [EmailConfirmationTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.emailconfirmationtokenprovider) |                                             Übernimmt oder bestimmt den Tokenanbieter, der zum Generieren von Tokens, die im Konto Bestätigungs-e-Mails verwendet werden.                                              |
|     [PasswordResetTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.passwordresettokenprovider)     | Ruft ab oder legt die [IUserTwoFactorTokenProvider<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) verwendet zum Generieren von Token, die im Kennwort zurücksetzen der e-Mail-Nachrichten verwendet. |
|                    [ProviderMap](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.providermap)                    |                Zum Erstellen einer [Benutzer Tokenanbieter](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor) mit dem Schlüssel verwendet wird, wie der Name des Anbieters.                 |

### <a name="user"></a>Benutzer

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_user)]

[IdentityOptions.User](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user) gibt an, die [UserOptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions) mit den Eigenschaften, die in der Tabelle dargestellt.

| Eigenschaft | Beschreibung | Standard |
| -------- | ----------- | :-----: |
| [AllowedUserNameCharacters](/dotnet/api/microsoft.aspnetcore.identity.useroptions.allowedusernamecharacters) | Zulässige Zeichen des Benutzernamens. | abcdefghijklmnopqrstuvwxyz<br>ABCDEFGHIJKLMNOPQRSTUVWXYZ<br>0123456789<br>-.\_@+ |
| [RequireUniqueEmail](/dotnet/api/microsoft.aspnetcore.identity.useroptions.requireuniqueemail) | Müssen alle Benutzer eine eindeutige e-Mail-Adresse haben. | `false` |

### <a name="cookie-settings"></a>Cookie-Einstellungen

Konfigurieren Sie in der app-Cookie `Startup.ConfigureServices`. [ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__) muss aufgerufen werden, **nach** Aufrufen `AddIdentity` oder `AddDefaultIdentity`.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_cookie)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?name=snippet_configurecookie)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-59,72-80,84)]

::: moniker-end

Weitere Informationen finden Sie unter [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions).

## <a name="password-hasher-options"></a>Hasher-Kennwortoptionen

<xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions> Abrufen und Festlegen von Optionen für die Kennwort-hashing.

| Option | Beschreibung |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> | Der Kompatibilitätsmodus verwendet, wenn neue Kennwörter hashing. Wird standardmäßig auf <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3> festgelegt. Das erste Byte ein Hashwert des Kennworts, bezeichnet einen *Format Marker*, gibt die Version des Hashalgorithmus verwendet, um die kennworthashsynchronisierung. Bei der Überprüfung der ein Kennwort mit einem Hash, der <xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*> Methode wählt die korrekten Algorithmus basierend auf dem ersten Byte. Ein Client ist, unabhängig davon, ob authentifizieren können, von denen wurde Version des Algorithmus verwendet, um die kennworthashsynchronisierung. Einstellen des Kompatibilitätsmodus wirkt sich auf das hashing von *neue Kennwörter*. |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> | Die Anzahl der Iterationen, die beim hashing von Kennwörtern mit PBKDF2 verwendet werden soll. Dieser Wert wird nur verwendet, wenn die <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> nastaven NA hodnotu <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>. Der Wert muss eine positive ganze Zahl und der Standardwert ist `10000`. |

Im folgenden Beispiel die <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> nastaven NA hodnotu `12000` in `Startup.ConfigureServices`:

```csharp
// using Microsoft.AspNetCore.Identity;

services.Configure<PasswordHasherOptions>(option =>
{
    option.IterationCount = 12000;
});
```

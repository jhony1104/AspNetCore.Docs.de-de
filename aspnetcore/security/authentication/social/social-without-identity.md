---
title: Authentifizierung mit Facebook, Google und externem Anbieter ohne ASP.net CoreIdentity
author: rick-anderson
description: Eine Erläuterung der Verwendung von Facebook, Google, Twitter und der Kontobenutzer Authentifizierung ohne ASP.net Core Identity.
ms.author: riande
ms.date: 12/10/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: cc44eb83947540ca9a5a04ffad4fdb8522fab26a
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775738"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a>Verwenden Sie die Authentifizierung für den Social Sign-in-Anbieter ohne ASP.net CoreIdentity

Von [Kirk Larkin](https://twitter.com/serpent5) und [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

<xref:security/authentication/social/index>Beschreibt, wie Sie es Benutzern ermöglichen können, sich mithilfe von OAuth 2,0 mit Anmelde Informationen von externen Authentifizierungs Anbietern anzumelden. Die in diesem Thema beschriebene Vorgehensweise umfasst Identity ASP.net Core als Authentifizierungs Anbieter.

In diesem Beispiel wird veranschaulicht, wie ein externer Authentifizierungs **without** Anbieter ohne IdentityASP.net Core verwendet wird. Dies ist nützlich für apps, die nicht alle Features von ASP.net Core Identitybenötigen, aber trotzdem eine Integration in einen vertrauenswürdigen externen Authentifizierungs Anbieter erfordern.

In diesem Beispiel wird die [Google-Authentifizierung](xref:security/authentication/google-logins) zum Authentifizieren von Benutzern verwendet. Die Verwendung der Google-Authentifizierung verlagert viele der Komplexitäten bei der Verwaltung des Anmeldevorgangs an Google. Informationen zur Integration in einen anderen externen Authentifizierungs Anbieter finden Sie in den folgenden Themen:

* [Facebook-Authentifizierung](xref:security/authentication/facebook-logins)
* [Microsoft-Authentifizierung](xref:security/authentication/microsoft-logins)
* [Twitter-Authentifizierung](xref:security/authentication/twitter-logins)
* [Andere Anbieter](xref:security/authentication/otherlogins)

## <a name="configuration"></a>Konfiguration

Konfigurieren Sie `ConfigureServices` in der-Methode die Authentifizierungs Schemas der APP <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>mit <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>den Methoden <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> , und:

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

Der- <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>Befehl <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> Ruft die der APP auf. Das `DefaultScheme` ist das Standardschema, das von den `HttpContext` folgenden Authentifizierungs Erweiterungs Methoden verwendet wird:

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

Wenn `DefaultScheme` Sie die APP auf [cookieauthenticationdefaults. authenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") festlegen, wird die APP so konfiguriert, dass Cookies als Standardschema für diese Erweiterungs Methoden verwendet werden. Wenn <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> Sie die APP auf [googledefaults. authenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") festlegen, wird die APP für die Verwendung von Google als Standardschema für `ChallengeAsync`Aufrufe von konfiguriert. `DefaultChallengeScheme`überschreibt `DefaultScheme`. Weitere <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> Eigenschaften, die bei fest `DefaultScheme` legung überschreiben, finden Sie unter.

Rufen `Startup.Configure` `UseAuthentication` Sie in und `UseAuthorization` zwischen dem `UseRouting` Aufruf `UseEndpoints`von und auf. Dadurch wird die `HttpContext.User` -Eigenschaft festgelegt und die Autorisierungs Middleware für Anforderungen ausgeführt:

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

Weitere Informationen zu Authentifizierungs Schemas finden Sie unter [Authentifizierungs Konzepte](xref:security/authentication/index#authentication-concepts). Weitere Informationen zur Cookie-Authentifizierung finden <xref:security/authentication/cookie>Sie unter.

## <a name="apply-authorization"></a>Autorisierung anwenden

Testen Sie die Authentifizierungs Konfiguration der APP, indem `AuthorizeAttribute` Sie das-Attribut auf einen Controller, eine Aktion oder eine Seite anwenden. Der folgende Code schränkt den Zugriff auf die *Datenschutz* Seite auf authentifizierte Benutzer ein:

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>Abmelden

Zum Abmelden des aktuellen Benutzers und zum Löschen des Cookies müssen Sie [signoutasync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)aufrufen. Der folgende Code fügt der `Logout` *Index* Seite einen Seiten Handler hinzu:

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

Beachten Sie `SignOutAsync` , dass der-aufrufswert kein Authentifizierungsschema angibt. Die APP `DefaultScheme` von `CookieAuthenticationDefaults.AuthenticationScheme` wird als Fallback verwendet.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<xref:security/authentication/social/index>Beschreibt, wie Sie es Benutzern ermöglichen können, sich mithilfe von OAuth 2,0 mit Anmelde Informationen von externen Authentifizierungs Anbietern anzumelden. Die in diesem Thema beschriebene Vorgehensweise umfasst Identity ASP.net Core als Authentifizierungs Anbieter.

In diesem Beispiel wird veranschaulicht, wie ein externer Authentifizierungs **without** Anbieter ohne IdentityASP.net Core verwendet wird. Dies ist nützlich für apps, die nicht alle Features von ASP.net Core Identitybenötigen, aber trotzdem eine Integration in einen vertrauenswürdigen externen Authentifizierungs Anbieter erfordern.

In diesem Beispiel wird die [Google-Authentifizierung](xref:security/authentication/google-logins) zum Authentifizieren von Benutzern verwendet. Die Verwendung der Google-Authentifizierung verlagert viele der Komplexitäten bei der Verwaltung des Anmeldevorgangs an Google. Informationen zur Integration in einen anderen externen Authentifizierungs Anbieter finden Sie in den folgenden Themen:

* [Facebook-Authentifizierung](xref:security/authentication/facebook-logins)
* [Microsoft-Authentifizierung](xref:security/authentication/microsoft-logins)
* [Twitter-Authentifizierung](xref:security/authentication/twitter-logins)
* [Andere Anbieter](xref:security/authentication/otherlogins)

## <a name="configuration"></a>Konfiguration

Konfigurieren Sie `ConfigureServices` in der-Methode die Authentifizierungs Schemas der APP `AddAuthentication`mit `AddCookie`den Methoden `AddGoogle` , und:

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

Durch den [addauthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) -Rückruf wird das [defaultscheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)der APP festgelegt. Das `DefaultScheme` ist das Standardschema, das von den `HttpContext` folgenden Authentifizierungs Erweiterungs Methoden verwendet wird:

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

Wenn `DefaultScheme` Sie die APP auf [cookieauthenticationdefaults. authenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") festlegen, wird die APP so konfiguriert, dass Cookies als Standardschema für diese Erweiterungs Methoden verwendet werden. Wenn <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> Sie die APP auf [googledefaults. authenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") festlegen, wird die APP für die Verwendung von Google als Standardschema für `ChallengeAsync`Aufrufe von konfiguriert. `DefaultChallengeScheme`überschreibt `DefaultScheme`. Weitere <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> Eigenschaften, die bei fest `DefaultScheme` legung überschreiben, finden Sie unter.

Rufen Sie `Configure` in der-Methode `UseAuthentication` die-Methode auf, um die Authentifizierungs Middleware `HttpContext.User` aufzurufen, die die-Eigenschaft festlegt. Rufen Sie `UseAuthentication` die-Methode `UseMvcWithDefaultRoute` auf `UseMvc`, bevor Sie oder aufrufen:

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

Weitere Informationen zu Authentifizierungs Schemas finden Sie unter [Authentifizierungs Konzepte](xref:security/authentication/index#authentication-concepts). Weitere Informationen zur Cookie-Authentifizierung finden <xref:security/authentication/cookie>Sie unter.

## <a name="apply-authorization"></a>Autorisierung anwenden

Testen Sie die Authentifizierungs Konfiguration der APP, indem `AuthorizeAttribute` Sie das-Attribut auf einen Controller, eine Aktion oder eine Seite anwenden. Der folgende Code schränkt den Zugriff auf die *Datenschutz* Seite auf authentifizierte Benutzer ein:

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>Abmelden

Zum Abmelden des aktuellen Benutzers und zum Löschen des Cookies müssen Sie [signoutasync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)aufrufen. Der folgende Code fügt der `Logout` *Index* Seite einen Seiten Handler hinzu:

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

Beachten Sie `SignOutAsync` , dass der-aufrufswert kein Authentifizierungsschema angibt. Die APP `DefaultScheme` von `CookieAuthenticationDefaults.AuthenticationScheme` wird als Fallback verwendet.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end

---
title: Authentifizierung mit Facebook, Google und externem Anbieter ohne ASP.net Core Identität
author: rick-anderson
description: Eine Erläuterung der Verwendung von Facebook, Google, Twitter usw. Kontobenutzer Authentifizierung ohne ASP.net Core Identität.
ms.author: riande
ms.date: 11/19/2019
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: 680ea091dcc5ed7f94879b5d277e8be7e5abeb7b
ms.sourcegitcommit: f40c9311058c9b1add4ec043ddc5629384af6c56
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74289117"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a>Verwenden der Authentifizierung für soziale Anmelde Anbieter ohne ASP.net Core Identität

::: moniker range=">= aspnetcore-3.0"

in <xref:security/authentication/social/index> wird beschrieben, wie Sie es Benutzern ermöglichen können, sich mithilfe von OAuth 2,0 mit Anmelde Informationen von externen Authentifizierungs Anbietern anzumelden. Die in diesem Thema beschriebene Vorgehensweise umfasst ASP.net Core Identität als Authentifizierungs Anbieter.

In diesem Beispiel wird veranschaulicht, wie ein externer Authentifizierungs Anbieter **ohne** ASP.net Core Identität verwendet wird. Dies ist nützlich für apps, die nicht alle Features ASP.net Core Identität benötigen, aber trotzdem eine Integration in einen vertrauenswürdigen externen Authentifizierungs Anbieter erfordern.

In diesem Beispiel wird die [Google-Authentifizierung](xref:security/authentication/google-logins) zum Authentifizieren von Benutzern verwendet. Die Verwendung der Google-Authentifizierung verlagert viele der Komplexitäten bei der Verwaltung des Anmeldevorgangs an Google. Informationen zur Integration in einen anderen externen Authentifizierungs Anbieter finden Sie in den folgenden Themen:

* [Facebook-Authentifizierung](xref:security/authentication/facebook-logins)
* [Microsoft-Authentifizierung](xref:security/authentication/microsoft-logins)
* [Twitter-Authentifizierung](xref:security/authentication/twitter-logins)
* [Andere Anbieter](xref:security/authentication/otherlogins)

## <a name="configuration"></a>Konfiguration

Konfigurieren Sie in der `ConfigureServices`-Methode die Authentifizierungs Schemas der APP mit den Methoden <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>und <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*>:

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

Durch den <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>-Aufrufe werden die <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>der APP festgelegt. Der `DefaultScheme` ist das Standardschema, das von den folgenden `HttpContext` Authentifizierungs Erweiterungs Methoden verwendet wird:

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

Wenn Sie die `DefaultScheme` der APP auf [cookieauthenticationdefaults. authenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") festlegen, wird die APP so konfiguriert, dass Cookies als Standardschema für diese Erweiterungs Methoden verwendet werden. Wenn Sie die <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> der APP auf [googledefaults. authenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") festlegen, wird die APP so konfiguriert, dass Sie Google als Standardschema für Aufrufe `ChallengeAsync`verwendet. `DefaultChallengeScheme` überschreibt `DefaultScheme`. Weitere Eigenschaften, die `DefaultScheme` bei Festlegung überschreiben, finden Sie unter <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>.

Rufen Sie in `Startup.Configure``UseAuthentication` auf, und `UseAuthorization` Sie zwischen Aufrufen von `UseRouting` und `UseEndpoints`. Dadurch wird die `HttpContext.User`-Eigenschaft festgelegt und die Autorisierungs Middleware für Anforderungen ausgeführt:

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

Weitere Informationen zu Authentifizierungs Schemas und zur Cookie-Authentifizierung finden Sie unter <xref:security/authentication/cookie>.

## <a name="apply-authorization"></a>Autorisierung anwenden

Testen Sie die Authentifizierungs Konfiguration der APP, indem Sie das `AuthorizeAttribute`-Attribut auf einen Controller, eine Aktion oder eine Seite anwenden. Der folgende Code schränkt den Zugriff auf die *Datenschutz* Seite auf authentifizierte Benutzer ein:

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>Abmelden

Zum Abmelden des aktuellen Benutzers und zum Löschen des Cookies müssen Sie [signoutasync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)aufrufen. Der folgende Code fügt der *Index* Seite einen `Logout` Seiten Handler hinzu:

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

Beachten Sie, dass der `SignOutAsync`-Aufrufe kein Authentifizierungsschema angibt. Die `DefaultScheme` des `CookieAuthenticationDefaults.AuthenticationScheme` der APP wird als Fallback verwendet.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

in <xref:security/authentication/social/index> wird beschrieben, wie Sie es Benutzern ermöglichen können, sich mithilfe von OAuth 2,0 mit Anmelde Informationen von externen Authentifizierungs Anbietern anzumelden. Die in diesem Thema beschriebene Vorgehensweise umfasst ASP.net Core Identität als Authentifizierungs Anbieter.

In diesem Beispiel wird veranschaulicht, wie ein externer Authentifizierungs Anbieter **ohne** ASP.net Core Identität verwendet wird. Dies ist nützlich für apps, die nicht alle Features ASP.net Core Identität benötigen, aber trotzdem eine Integration in einen vertrauenswürdigen externen Authentifizierungs Anbieter erfordern.

In diesem Beispiel wird die [Google-Authentifizierung](xref:security/authentication/google-logins) zum Authentifizieren von Benutzern verwendet. Die Verwendung der Google-Authentifizierung verlagert viele der Komplexitäten bei der Verwaltung des Anmeldevorgangs an Google. Informationen zur Integration in einen anderen externen Authentifizierungs Anbieter finden Sie in den folgenden Themen:

* [Facebook-Authentifizierung](xref:security/authentication/facebook-logins)
* [Microsoft-Authentifizierung](xref:security/authentication/microsoft-logins)
* [Twitter-Authentifizierung](xref:security/authentication/twitter-logins)
* [Andere Anbieter](xref:security/authentication/otherlogins)

## <a name="configuration"></a>Konfiguration

Konfigurieren Sie in der `ConfigureServices`-Methode die Authentifizierungs Schemas der APP mit den Methoden `AddAuthentication`, `AddCookie`und `AddGoogle`:

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

Durch den [addauthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) -Rückruf wird das [defaultscheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)der APP festgelegt. Der `DefaultScheme` ist das Standardschema, das von den folgenden `HttpContext` Authentifizierungs Erweiterungs Methoden verwendet wird:

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

Wenn Sie die `DefaultScheme` der APP auf [cookieauthenticationdefaults. authenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") festlegen, wird die APP so konfiguriert, dass Cookies als Standardschema für diese Erweiterungs Methoden verwendet werden. Wenn Sie die <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> der APP auf [googledefaults. authenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") festlegen, wird die APP so konfiguriert, dass Sie Google als Standardschema für Aufrufe `ChallengeAsync`verwendet. `DefaultChallengeScheme` überschreibt `DefaultScheme`. Weitere Eigenschaften, die `DefaultScheme` bei Festlegung überschreiben, finden Sie unter <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>.

Rufen Sie in der `Configure`-Methode die `UseAuthentication`-Methode auf, um die Authentifizierungs Middleware aufzurufen, die die `HttpContext.User`-Eigenschaft festlegt. Rufen Sie die `UseAuthentication`-Methode auf, bevor Sie `UseMvcWithDefaultRoute` oder `UseMvc`aufrufen:

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

Weitere Informationen zu Authentifizierungs Schemas und zur Cookie-Authentifizierung finden Sie unter <xref:security/authentication/cookie>.

## <a name="apply-authorization"></a>Autorisierung anwenden

Testen Sie die Authentifizierungs Konfiguration der APP, indem Sie das `AuthorizeAttribute`-Attribut auf einen Controller, eine Aktion oder eine Seite anwenden. Der folgende Code schränkt den Zugriff auf die *Datenschutz* Seite auf authentifizierte Benutzer ein:

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>Abmelden

Zum Abmelden des aktuellen Benutzers und zum Löschen des Cookies müssen Sie [signoutasync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)aufrufen. Der folgende Code fügt der *Index* Seite einen `Logout` Seiten Handler hinzu:

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

Beachten Sie, dass der `SignOutAsync`-Aufrufe kein Authentifizierungsschema angibt. Die `DefaultScheme` des `CookieAuthenticationDefaults.AuthenticationScheme` der APP wird als Fallback verwendet.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end

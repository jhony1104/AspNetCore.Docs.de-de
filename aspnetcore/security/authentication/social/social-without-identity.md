---
title: Facebook, Google und externe Anbieter-Authentifizierung ohne ASP.NET Core Identity
author: rick-anderson
description: Eine Erläuterung der Verwendung von Facebook, Google, Twitter, Benutzer usw.-Kontoauthentifizierung ohne ASP.NET Core Identity.
ms.author: riande
ms.date: 07/04/2019
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: e67da513fef1ce453110c465b08e9c7965e71df5
ms.sourcegitcommit: d6e51c60439f03a8992bda70cc982ddb15d3f100
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67557649"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a>Verwenden Sie die Anmeldung Anbieter sozialer Authentifizierung ohne ASP.NET Core Identity

<xref:security/authentication/social/index> Beschreibt, wie Benutzer sich mithilfe von OAuth 2.0 mit den Anmeldeinformationen von externen Authentifizierungsanbietern anmelden zu aktivieren. In diesem Thema beschriebene Ansatz umfasst die ASP.NET Core Identity als Authentifizierungsanbieter.

Dieses Beispiel veranschaulicht, wie einen externer Authentifizierungsanbieter **ohne** ASP.NET Core Identity. Dies ist nützlich für apps, die nicht alle Funktionen von ASP.NET Core Identity erfordern, jedoch benötigen Sie bei der Integration mit einem vertrauenswürdigen externen Authentifizierungsanbieter.

In diesem Beispiel wird [Google-Authentifizierung](xref:security/authentication/google-logins) zum Authentifizieren von Benutzern. Mithilfe von Google verlagert Authentifizierung einen Großteil der Komplexität der Verwaltung der Anmeldevorgang an Google. Um mit einer anderen externen Authentifizierungsanbieter zu integrieren, finden Sie in den folgenden Themen:

* [Facebook-Authentifizierung](xref:security/authentication/facebook-logins)
* [Microsoft-Authentifizierung](xref:security/authentication/microsoft-logins)
* [Twitter-Authentifizierung](xref:security/authentication/twitter-logins)
* [Andere Anbieter](xref:security/authentication/otherlogins)

## <a name="configuration"></a>Konfiguration

In der `ConfigureServices` -Methode, konfigurieren Sie die app Authentifizierungsschemas mit dem `AddAuthentication`, `AddCookie` und `AddGoogle` Methoden:

[!code-csharp[](social-without-identity/sample/Startup.cs?name=snippet1)]

Der Aufruf von [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) für die App die [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme). Die `DefaultScheme` ist das Standardschema verwendet wird, durch die folgenden `HttpContext` Erweiterungsmethoden für die Authentifizierung:

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

Festlegen der app `DefaultScheme` zu [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") konfiguriert die app aus, um Cookies als das Standardschema für diese besonderen Erweiterungsmethoden zu verwenden. Festlegen der app <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> zu [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") konfiguriert die app zur Verwendung von Google als das Standardschema für Aufrufe von `ChallengeAsync`. `DefaultChallengeScheme` überschreibt `DefaultScheme`. Finden Sie unter <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> für zusätzliche Eigenschaften, die außer Kraft setzen `DefaultScheme` festgelegt.

In der `Configure` -Methode, rufen die `UseAuthentication` Middleware für die Authentifizierung, die festlegt, aufzurufenden Methode der `HttpContext.User` Eigenschaft. Rufen Sie die `UseAuthentication` Methode vor dem Aufruf `UseMvcWithDefaultRoute` oder `UseMvc`:

[!code-csharp[](social-without-identity/sample/Startup.cs?name=snippet2)]

Weitere Informationen zu Authentifizierungsschemas und Cookie-Authentifizierung finden Sie unter <xref:security/authentication/cookie>.

## <a name="applying-basic-authorization"></a>Einfache Autorisierung anwenden

Testen Sie die Authentifizierungskonfiguration der app, die durch Anwenden der `AuthorizeAttribute` -Attribut auf einen Controller, Aktion oder Seite. Der folgende Code beschränkt den Zugriff auf die *Datenschutz* Seite, um Benutzer, die authentifiziert wurden:

[!code-csharp[](social-without-identity/sample/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>Abmelden

Um den aktuellen Benutzer abmelden, und ihre Cookies zu löschen, rufen Sie [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signoutasync?view=aspnetcore-2.0). Der folgende Code Fügt eine `Logout` Seitenhandler, der die *Index* Seite:

[!code-csharp[](social-without-identity/sample/Pages/Index.cshtml.cs?name=snippet&highlight=7-11)]

Beachten Sie, dass der Aufruf von `SignOutAsync` gibt nicht an ein anderes Authentifizierungsschema. Der app `DefaultScheme` von `CookieAuthenticationDefaults.AuthenticationScheme` als ein Fallback verwendet wird.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

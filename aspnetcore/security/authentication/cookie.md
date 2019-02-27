---
title: Verwenden der Cookieauthentifizierung ohne ASP.NET Core Identity
author: rick-anderson
description: Eine Erläuterung der verwenden der Cookieauthentifizierung ohne ASP.NET Core Identity
ms.author: riande
ms.date: 02/25/2019
uid: security/authentication/cookie
ms.openlocfilehash: 29370a3ff25469b34edc2a71e00601cf6ecc00ca
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/27/2019
ms.locfileid: "56899285"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a>Verwenden der Cookieauthentifizierung ohne ASP.NET Core Identity

Durch [Rick Anderson](https://twitter.com/RickAndMSFT) und [Luke Latham](https://github.com/guardrex)

Wie Sie in den früheren Authentifizierungsthemen gesehen haben [ASP.NET Core Identity](xref:security/authentication/identity) ist ein vollständige, voll ausgestattete Authentication-Anbieter zum Erstellen und Verwalten von Anmeldungen. Möglicherweise möchten jedoch Ihre eigene Logik für die benutzerdefinierte Authentifizierung mit gelegentlich cookiebasierte Authentifizierung zu verwenden. Sie können als eigenständige Authentifizierungsanbieter ohne ASP.NET Core Identity cookiebasierte Authentifizierung.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Zu Demonstrationszwecken in der Beispiel-app ist das Benutzerkonto für den hypothetischen Benutzer Maria Rodriguez, in der app hartcodiert. Verwenden Sie den Benutzernamen des e-Mail-Adresse "maria.rodriguez@contoso.com" und eines Kennworts zum Anmelden des Benutzers. Der Benutzer wird authentifiziert, der `AuthenticateUser` -Methode in der die *Pages/Account/Login.cshtml.cs* Datei. Im Real-World-Beispiel würde der Benutzer für eine Datenbank authentifiziert werden.

Weitere Informationen zur Migration cookiebasierte Authentifizierung von ASP.NET Core 1.x zu 2.0 finden Sie unter [Migrieren von Authentifizierungs- und Identitätseinstellungen nach ASP.NET Core 2.0-Thema (cookiebasierte Authentifizierung)](xref:migration/1x-to-2x/identity-2x#cookie-based-authentication).

Um ASP.NET Core Identity verwenden zu können, finden Sie unter den [Einführung in die Identität](xref:security/authentication/identity) Thema.

## <a name="configuration"></a>Konfiguration

::: moniker range=">= aspnetcore-2.0"

Wenn die app verwenden, nicht die [Microsoft.AspNetCore.App metapaket](xref:fundamentals/metapackage-app), erstellen Sie einen Paketverweis in die Projektdatei für die [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) Paket (Version 2.1.0 oder weiter unten).

In der `ConfigureServices` -Methode, erstellen Sie den Authentifizierungs-Middleware-Dienst, mit der `AddAuthentication` und `AddCookie` Methoden:

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

`AuthenticationScheme` die an `AddAuthentication` legt das Authentifizierungsschema "Standard" für die app fest. `AuthenticationScheme` ist nützlich, wenn mehrere Instanzen der Cookie-Authentifizierung vorhanden sind, und Sie möchten [autorisieren mit einem bestimmten Schema](xref:security/authorization/limitingidentitybyscheme). Festlegen der `AuthenticationScheme` zu `CookieAuthenticationDefaults.AuthenticationScheme` "Cookies" der Wert für das Schema enthält. Sie können einen beliebigen Zeichenfolgenwert angeben, der das Schema unterscheidet.

Der app-Authentifizierungsschema unterscheidet sich von der app Cookie-Authentifizierungsschema. Wenn ein Cookie-Authentifizierungsschema bereitgestellt ist nicht <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, verwendet [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies").

Des Authentifizierungscookies <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> -Eigenschaftensatz auf `true` standardmäßig. Authentifizierungscookies sind zulässig, wenn ein Besucher der Website, die Datensammlung zugestimmt wurde nicht. Weitere Informationen finden Sie unter <xref:security/gdpr#essential-cookies>.

In der `Configure` -Methode ist, die `UseAuthentication` Middleware für die Authentifizierung, die festlegt, aufzurufenden Methode der `HttpContext.User` Eigenschaft. Rufen Sie die `UseAuthentication` Methode vor dem Aufruf `UseMvcWithDefaultRoute` oder `UseMvc`:

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

**AddCookie-Optionen**

Die [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions?view=aspnetcore-2.0) Klasse wird verwendet, um die Optionen für die Authentifizierung-Anbieter konfigurieren.

| Option | Beschreibung |
| ------ | ----------- |
| [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath?view=aspnetcore-2.0) | Gibt den Pfad zu einer 302 gefunden (URL-Umleitung) liefern die Auslösung von `HttpContext.ForbidAsync`. Der Standardwert ist `/Account/AccessDenied`. |
| [ClaimsIssuer](/dotnet/api/microsoft.aspnetcore.authentication.authenticationschemeoptions.claimsissuer?view=aspnetcore-2.0) | Der Aussteller für die Verwendung der [Aussteller](/dotnet/api/system.security.claims.claim.issuer) Eigenschaft für alle Ansprüche, die von dem Cookie Authentication-Dienst erstellt. |
| [Cookie.Domain](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.domain?view=aspnetcore-2.0) | Der Domänenname, in dem das Cookie bereitgestellt wird. Standardmäßig ist dies der Hostname der Anforderung. Der Browser sendet das Cookie nur in Anforderungen auf einen entsprechenden Hostnamen. Sie möchten möglicherweise passen Sie diese Option, um Cookies zu einem beliebigen Host in der Domäne verfügen. Wenn z. B. Domäne des Cookies auf `.contoso.com` verfügbar macht `contoso.com`, `www.contoso.com`, und `staging.www.contoso.com`. |
| [Cookie.HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly?view=aspnetcore-2.0) | Ein Flag, das angibt, ob das Cookie nur Server zugegriffen werden soll. Durch Ändern dieses Werts auf `false` clientseitige Skripts auf Cookies zugreifen können und möglicherweise öffnen Sie Ihre app zum Diebstahl von Cookies muss Ihrer app eine [Cross-Site-scripting (XSS)](xref:security/cross-site-scripting) Sicherheitsrisiko. Der Standardwert ist `true`. |
| [Cookie.Name](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.name?view=aspnetcore-2.0) | Legt den Namen des Cookies. |
| [Cookie.Path](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.path?view=aspnetcore-2.0) | Zum Isolieren von apps auf dem gleichen Hostnamen verwendet. Wenn Sie eine app mit haben `/app1` und Cookies für diese app zu beschränken, legen Sie die `CookiePath` Eigenschaft `/app1`. Auf diese Weise das Cookie ist nur verfügbar für Anforderungen an `/app1` und alle Apps, darunter. |
| [Cookie.SameSite](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.samesite?view=aspnetcore-2.0) | Gibt an, ob der Browser Cookies, die auf Anforderungen der gleichen Website nur angefügt werden darf (`SameSiteMode.Strict`) oder Cross-Site-Anforderungen, die mithilfe von sicheren HTTP-Methoden und Anforderungen der gleichen Website (`SameSiteMode.Lax`). Bei Festlegung auf `SameSiteMode.None`, der Wert der Cookie-Header nicht festgelegt. Beachten Sie, dass [Richtlinie Cookiemiddleware](#cookie-policy-middleware) möglicherweise Überschreiben des Werts, den Sie bereitstellen. Um OAuth-Authentifizierung zu unterstützen, der Standardwert ist `SameSiteMode.Lax`. Weitere Informationen finden Sie unter [OAuth-Authentifizierung, die aufgrund von Richtlinien für SameSite-Cookies unterteilt](https://github.com/aspnet/Security/issues/1231). |
| [Cookie.SecurePolicy](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.securepolicy?view=aspnetcore-2.0) | Ein Flag, das angibt, ob das Cookie erstellt auf HTTPS beschränkt werden soll (`CookieSecurePolicy.Always`), HTTP oder HTTPS (`CookieSecurePolicy.None`), oder das gleiche Protokoll wie die Anforderung (`CookieSecurePolicy.SameAsRequest`). Der Standardwert ist `CookieSecurePolicy.SameAsRequest`. |
| [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.dataprotectionprovider?view=aspnetcore-2.0) | Legt die `DataProtectionProvider` dient zum Erstellen der standardmäßigen `TicketDataFormat`. Wenn die `TicketDataFormat` Eigenschaft festgelegt ist, die `DataProtectionProvider` Option wird nicht verwendet. Wenn nicht angegeben, wird die app Datenschutz-Standardanbieter verwendet. |
| [Ereignisse](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.events?view=aspnetcore-2.0) | Der Handler ruft Methoden für den Anbieter, der die app-Steuerung an bestimmten Punkten für die Verarbeitung zu ermöglichen. Wenn `Events` sind nicht angegeben wird, wird eine Standardinstanz bereitgestellt wird, die keine Aktion ausführt, wenn die Methoden aufgerufen werden. |
| [EventsType](/dotnet/api/microsoft.aspnetcore.authentication.authenticationschemeoptions.eventstype?view=aspnetcore-2.0) | Als den Diensttyp verwendet, um das Abrufen der `Events` Instanz statt mit der Eigenschaft. |
| [ExpireTimeSpan](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.expiretimespan?view=aspnetcore-2.0) | Die `TimeSpan` nach dem in Cookies gespeicherten Authentifizierungsticket abläuft. `ExpireTimeSpan` wird hinzugefügt, dass die aktuelle Uhrzeit aus, um die Ablaufzeit für das Ticket zu erstellen. Die `ExpiredTimeSpan` Wert ist immer in das verschlüsselte Authentifizierungsticket, die vom Server überprüft wird. Es kann auch wechseln Sie in der [Set-Cookie](https://tools.ietf.org/html/rfc6265#section-4.1) Header jedoch nur, wenn `IsPersistent` festgelegt ist. Festzulegende `IsPersistent` zu `true`, konfigurieren die [AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.authentication.authenticationproperties) übergeben `SignInAsync`. Der Standardwert von `ExpireTimeSpan` beträgt 14 Tage. |
| [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath?view=aspnetcore-2.0) | Gibt den Pfad zu einer 302 gefunden (URL-Umleitung) liefern die Auslösung von `HttpContext.ChallengeAsync`. Die aktuelle URL, die den Statuscode 401 generiert wird hinzugefügt, um die `LoginPath` als ein Abfragezeichenfolgen-Parameter mit dem Namen, indem Sie die `ReturnUrlParameter`. Einmal eine Anforderung an die `LoginPath` eine neue Identität Anmeldung gewährt der `ReturnUrlParameter` Wert wird verwendet, um den Browser zurück an die URL umgeleitet wird, die den ursprünglichen Statuscode nicht autorisiert verursacht hat. Der Standardwert ist `/Account/Login`. |
| [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath?view=aspnetcore-2.0) | Wenn die `LogoutPath` wird bereitgestellt, um den Handler auf, und klicken Sie dann eine Anforderung an diesen Pfad leitet basierend auf den Wert der `ReturnUrlParameter`. Der Standardwert ist `/Account/Logout`. |
| [ReturnUrlParameter](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.returnurlparameter?view=aspnetcore-2.0) | Legt den Namen des Abfragezeichenfolgen-Parameters, der durch den Handler für eine Antwort mit dem 302 gefunden (URL-Umleitung) angefügt wird. `ReturnUrlParameter` wird verwendet, wenn eine Anforderung eingeht, auf die `LoginPath` oder `LogoutPath` den Browser die ursprüngliche URL zurückgegeben, nachdem die an- bzw. Abmeldung Aktion ausgeführt wird. Der Standardwert ist `ReturnUrl`. |
| [SessionStore](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.sessionstore?view=aspnetcore-2.0) | Ein optionaler Container verwendet, um die Identität anforderungsübergreifend gespeichert. Wenn verwendet, wird nur ein Sitzungsbezeichner an den Client gesendet. `SessionStore` kann verwendet werden, um potenzielle Probleme mit großen Identitäten zu verringern. |
| [SlidingExpiration](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.slidingexpiration?view=aspnetcore-2.0) | Ein Flag, das angibt, ob ein neues Cookie mit einer aktualisierten Ablaufzeit dynamisch ausgegeben werden soll. Dies kann bei jeder Anforderung auftreten, in dem das aktuelle Cookie Ablaufdatum mehr als 50 % abgelaufen ist. Das neue Ablaufdatum nach unten verschoben werden das aktuelle Datum plus dem `ExpireTimespan`. Ein [absolute Cookie Ablaufzeit](xref:security/authentication/cookie#absolute-cookie-expiration) kann festgelegt werden, mithilfe der `AuthenticationProperties` -Klasse beim Aufrufen von `SignInAsync`. Eine absolute Ablaufzeit kann verbessern Sie die Sicherheit Ihrer App durch Einschränken der Menge der Zeit, die das Authentifizierungscookie gültig ist. Der Standardwert ist `true`. |
| [TicketDataFormat](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.ticketdataformat?view=aspnetcore-2.0) | Die `TicketDataFormat` wird zum Schützen und Aufheben des Schutzes der Identität und andere Eigenschaften, die im Cookiewert gespeichert sind. Wenn nicht angegeben ist, eine `TicketDataFormat` wurde mit der [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.dataprotectionprovider?view=aspnetcore-2.0). |
| [Überprüfen](/dotnet/api/microsoft.aspnetcore.authentication.authenticationschemeoptions.validate?view=aspnetcore-2.0) | Methode, die überprüft, ob die Optionen gültig sind. |

Legen Sie `CookieAuthenticationOptions` in der Dienstkonfiguration für die Authentifizierung in der `ConfigureServices` Methode:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

ASP.NET Core 1.x verwendet Cookies [Middleware](xref:fundamentals/middleware/index) , der einen Benutzerprinzipal in einem verschlüsselten Cookie serialisiert. Bei nachfolgenden Anforderungen, der das Cookie wird überprüft, und der Prinzipal neu erstellt und zugewiesen wird die `HttpContext.User` Eigenschaft.

Installieren Sie die [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) NuGet-Paket in Ihrem Projekt. Dieses Paket enthält die Cookie-Middleware.

Verwenden der `UseCookieAuthentication` -Methode in der die `Configure` -Methode in Ihrer *"Startup.cs"* -Datei, bevor `UseMvc` oder `UseMvcWithDefaultRoute`:

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions()
{
    AccessDeniedPath = "/Account/Forbidden/",
    AuthenticationScheme = CookieAuthenticationDefaults.AuthenticationScheme,
    AutomaticAuthenticate = true,
    AutomaticChallenge = true,
    LoginPath = "/Account/Unauthorized/"
});
```

**CookieAuthenticationOptions-Optionen**

Die [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions?view=aspnetcore-1.1) Klasse wird verwendet, um die Optionen für die Authentifizierung-Anbieter konfigurieren.

| Option | Beschreibung |
| ------ | ----------- |
| [AuthenticationScheme](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.authenticationscheme?view=aspnetcore-1.1) | Legt das Authentifizierungsschema fest. `AuthenticationScheme` ist nützlich, wenn mehrere Instanzen der Authentifizierung vorhanden sind, und Sie möchten [autorisieren mit einem bestimmten Schema](xref:security/authorization/limitingidentitybyscheme). Festlegen der `AuthenticationScheme` zu `CookieAuthenticationDefaults.AuthenticationScheme` "Cookies" der Wert für das Schema enthält. Sie können einen beliebigen Zeichenfolgenwert angeben, der das Schema unterscheidet. |
| [AutomaticAuthenticate](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.automaticauthenticate?view=aspnetcore-1.1) | Legt einen Wert aus, um anzugeben, dass die Cookie-Authentifizierung sollte für jede Anforderung ausgeführt und versucht wird, um zu überprüfen, und rekonstruieren serialisierte Prinzipal, dem Sie erstellt. |
| [AutomaticChallenge](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.automaticchallenge?view=aspnetcore-1.1) | True gibt an, behandelt die authentifizierungsmiddleware automatische Herausforderungen. Wenn "false", die authentifizierungsmiddleware nur Antworten, wenn dies ausdrücklich angegeben ändert die `AuthenticationScheme`. |
| [ClaimsIssuer](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.claimsissuer?view=aspnetcore-1.1) | Der Aussteller für die Verwendung der [Aussteller](/dotnet/api/system.security.claims.claim.issuer) Eigenschaft für alle Ansprüche, die durch die cookieauthentifizierungs-Middleware erstellt. |
| [CookieDomain](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiedomain?view=aspnetcore-1.1) | Der Domänenname, in dem das Cookie bereitgestellt wird. Standardmäßig ist dies der Hostname der Anforderung. Der Browser dient nur Cookies, das einen entsprechenden Hostnamen an. Sie möchten möglicherweise passen Sie diese Option, um Cookies zu einem beliebigen Host in der Domäne verfügen. Wenn z. B. Domäne des Cookies auf `.contoso.com` verfügbar macht `contoso.com`, `www.contoso.com`, und `staging.www.contoso.com`. |
| [CookieHttpOnly](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiehttponly?view=aspnetcore-1.1) | Ein Flag, das angibt, ob das Cookie nur Server zugegriffen werden soll. Durch Ändern dieses Werts auf `false` clientseitige Skripts auf Cookies zugreifen können und möglicherweise öffnen Sie Ihre app zum Diebstahl von Cookies muss Ihrer app eine [Cross-Site-scripting (XSS)](xref:security/cross-site-scripting) Sicherheitsrisiko. Der Standardwert ist `true`. |
| [CookiePath](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiepath?view=aspnetcore-1.1) | Zum Isolieren von apps auf dem gleichen Hostnamen verwendet. Wenn Sie eine app mit haben `/app1` und Cookies für diese app zu beschränken, legen Sie die `CookiePath` Eigenschaft `/app1`. Auf diese Weise das Cookie ist nur verfügbar für Anforderungen an `/app1` und alle Apps, darunter. |
| [CookieSecure](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiesecure?view=aspnetcore-1.1) | Ein Flag, das angibt, ob das Cookie erstellt auf HTTPS beschränkt werden soll (`CookieSecurePolicy.Always`), HTTP oder HTTPS (`CookieSecurePolicy.None`), oder das gleiche Protokoll wie die Anforderung (`CookieSecurePolicy.SameAsRequest`). Der Standardwert ist `CookieSecurePolicy.SameAsRequest`. |
| [Beschreibung](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.description?view=aspnetcore-1.1) | Weitere Informationen zum Authentifizierungstyp, der für die app zur Verfügung gestellt wird. |
| [ExpireTimeSpan](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.expiretimespan?view=aspnetcore-1.1) | Die `TimeSpan` nach dem das Authentifizierungsticket abläuft. Es wird auf die aktuelle Uhrzeit aus, um die Ablaufzeit für das Ticket zu erstellen, hinzugefügt. Mit `ExpireTimeSpan`, müssen Sie festlegen, `IsPersistent` zu `true` in die `AuthenticationProperties` übergeben `SignInAsync`. Der Standardwert beträgt 14 Tage. |
| [SlidingExpiration](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.slidingexpiration?view=aspnetcore-1.1) | Ein Flag, der angibt, ob das Cookie-Ablaufdatum wird, wenn mehr als die Hälfte der zurückgesetzt der `ExpireTimeSpan` Intervall ist verstrichen. Die neue Exipiration Zeit nach unten verschoben werden das aktuelle Datum plus dem `ExpireTimespan`. Ein [absolute Cookie Ablaufzeit](xref:security/authentication/cookie#absolute-cookie-expiration) kann festgelegt werden, mithilfe der `AuthenticationProperties` -Klasse beim Aufrufen von `SignInAsync`. Eine absolute Ablaufzeit kann verbessern Sie die Sicherheit Ihrer App durch Einschränken der Menge der Zeit, die das Authentifizierungscookie gültig ist. Der Standardwert ist `true`. |

Legen Sie `CookieAuthenticationOptions` für die Cookieauthentifizierungs-Middleware in der `Configure` Methode:

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    ...
});
```

::: moniker-end

## <a name="cookie-policy-middleware"></a>Richtlinie Cookie-Middleware

[Richtlinie Cookiemiddleware](/dotnet/api/microsoft.aspnetcore.cookiepolicy.cookiepolicymiddleware) Cookie Funktionen in einer app ermöglicht. Hinzufügen der Middleware zur Verarbeitungspipeline app ist die Reihenfolge, die vertrauliche; Sie wirkt sich nur auf Komponenten, die danach in der Pipeline registriert.

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

 Die [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) bereitgestellt, um die Cookie-Middleware für Richtlinie können Sie globale Eigenschaften der Cookie-Verarbeitung und Hook in von Cookie-Verarbeitungshandler zu steuern, wenn Cookies angefügt oder gelöscht werden.

| Eigenschaft | Beschreibung |
| -------- | ----------- |
| [HttpOnly](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.httponly) | Wirkt sich auf, ob Cookies "HttpOnly" sein müssen, die ein Flag, der angibt, wenn das Cookie nur Server zugegriffen werden soll. Der Standardwert ist `HttpOnlyPolicy.None`. |
| [MinimumSameSitePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.minimumsamesitepolicy) | Wirkt sich auf das Cookie des SameSite-Attribut (siehe unten). Der Standardwert ist `SameSiteMode.Lax`. Diese Option ist für ASP.NET Core 2.0 und höher verfügbar. |
| [OnAppendCookie](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.onappendcookie) | Aufgerufen, wenn ein Cookie angefügt wird. |
| [OnDeleteCookie](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.ondeletecookie) | Wird aufgerufen, wenn ein Cookie gelöscht wird. |
| [Sichern](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.secure) | Wirkt sich auf, ob Cookies sicher sein müssen. Der Standardwert ist `CookieSecurePolicy.None`. |

**MinimumSameSitePolicy** (ASP.NET Core 2.0 und höher nur)

Der Standardwert `MinimumSameSitePolicy` Wert `SameSiteMode.Lax` um OAuth2-Authentifizierung zu ermöglichen. Genau eine SameSite-Richtlinie erzwingen `SameSiteMode.Strict`legen die `MinimumSameSitePolicy`. Auch wenn diese Einstellung OAuth2 und andere Cross-Origin-Authentifizierungsschemas unterbrochen wird, erhöht sie die Sicherheitsstufe Cookie für andere Typen von apps, die Verarbeitung von Cross-Origin-Anforderungen nicht benötigen.

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

Die Richtlinie Cookie-Middleware-Einstellung für `MinimumSameSitePolicy` kann die Einstellung für beeinflussen `Cookie.SameSite` in `CookieAuthenticationOptions` gemäß der folgenden Matrix.

| MinimumSameSitePolicy | Cookie.SameSite | Resultierende Cookie.SameSite-Einstellung |
| --------------------- | --------------- | --------------------------------- |
| SameSiteMode.None     | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Lax      | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Lax<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Strict   | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Strict<br>SameSiteMode.Strict<br>SameSiteMode.Strict |

## <a name="create-an-authentication-cookie"></a>Erstellen Sie ein Authentifizierungscookie

Um ein Cookie mit Benutzerinformationen zu erstellen, müssen Sie erstellen eine ["ClaimsPrincipal"](/dotnet/api/system.security.claims.claimsprincipal). Die Benutzerinformationen serialisiert und im Cookie gespeichert. 

::: moniker range=">= aspnetcore-2.0"

Erstellen Sie eine ["ClaimsIdentity"](/dotnet/api/system.security.claims.claimsidentity) mit ggf. erforderlichen [Anspruch](/dotnet/api/system.security.claims.claim)s, und rufen [SignInAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signinasync?view=aspnetcore-2.0) zum Anmelden des Benutzers:

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Rufen Sie [SignInAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1.signinasync?view=aspnetcore-1.1) zum Anmelden des Benutzers:

```csharp
await HttpContext.Authentication.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity));
```

::: moniker-end

`SignInAsync` erstellt ein verschlüsseltes Cookie aus, und die aktuelle Antwort hinzugefügt. Wenn Sie nicht angeben einer `AuthenticationScheme`, wird das Standardschema verwendet.

Darüber hinaus ist die Verschlüsselung verwendet ASP.NET Core [den Datenschutz](xref:security/data-protection/using-data-protection#security-data-protection-getting-started) System. Wenn Sie die app auf mehrere Computer, den Lastenausgleich für apps oder mit einer Webfarm hosten, müssen Sie [Schutz von Daten konfigurieren](xref:security/data-protection/configuration/overview) und mit dem Schlüsselbund für den gleichen app-Bezeichner.

## <a name="sign-out"></a>Abmelden

::: moniker range=">= aspnetcore-2.0"

Um den aktuellen Benutzer abmelden, und ihre Cookies zu löschen, rufen Sie [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signoutasync?view=aspnetcore-2.0):

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Um den aktuellen Benutzer abmelden, und ihre Cookies zu löschen, rufen Sie [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1.signoutasync?view=aspnetcore-1.1):

```csharp
await HttpContext.Authentication.SignOutAsync(
    CookieAuthenticationDefaults.AuthenticationScheme);
```

::: moniker-end

Wenn Sie nicht verwenden `CookieAuthenticationDefaults.AuthenticationScheme` (oder "Cookies") als das Schema (z. B. "ContosoCookie"), geben Sie das Schema, das Sie beim Konfigurieren des Authentifizierungsanbieters verwendet. Andernfalls wird das Standardschema verwendet.

## <a name="react-to-back-end-changes"></a>Reagieren Sie auf Änderungen der Back-end

Sobald ein Cookie erstellt wurde, wird es die einzige Quelle der Identität. Auch wenn Sie einen Benutzer in Ihrem Back-End-Systemen deaktiviert haben, das Cookie-Authentifizierungssystem hat keine Kenntnis davon aus, und ein Benutzer bleibt angemeldet, solange ihre Cookie gültig ist.

Die [ValidatePrincipal](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationevents.validateprincipal) Ereignis in ASP.NET Core 2.x oder [ValidateAsync](/dotnet/api/microsoft.aspnetcore.identity.isecuritystampvalidator.validateasync?view=aspnetcore-1.1) -Methode in der ASP.NET Core 1.x abfangen und überschreiben die Überprüfung der Identität ein Cookie verwendet werden können. Dieser Ansatz verringert das Risiko von gesperrten Benutzer auf die app zugreifen.

Ein Ansatz für die gültigkeitsprüfung basiert zu verfolgen, wenn die Datenbank geändert wurde. Wenn die Datenbank geändert wurde, nachdem der Benutzer Cookies ausgegeben wurde, ist es nicht erforderlich, die der Benutzer erneut authentifizieren, wenn ihre Cookie noch gültig ist. Zu diesem Szenario wird die Datenbank zu implementieren, die Implementierung in `IUserRepository` in diesem Beispiel speichert ein `LastChanged` Wert. Wenn alle Benutzer in der Datenbank aktualisiert wird die `LastChanged` Wert wird auf die aktuelle Uhrzeit festgelegt.

Um ein Cookie für ungültig erklären, wenn die Änderungen in der Datenbank auf Grundlage der `LastChanged` Wert, erstellen Sie das Cookie mit einem `LastChanged` Anspruch mit dem aktuellen `LastChanged` Wert aus der Datenbank:

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

::: moniker range=">= aspnetcore-2.0"

Implementieren Sie eine Außerkraftsetzung für die `ValidatePrincipal` Ereignis schreiben, eine Methode mit der folgenden Signatur in einer Klasse, die Sie ableiten [CookieAuthenticationEvents](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationevents):

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

Ein Beispiel sieht folgendermaßen aus:

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

Registrieren Sie die Ereignisse-Instanz während der Cookie-Service-Registrierung in der `ConfigureServices` Methode. Geben Sie eine Bereichsbezogene Service-Registrierung für Ihre `CustomCookieAuthenticationEvents` Klasse:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Implementieren Sie eine Außerkraftsetzung für die `ValidateAsync` Ereignis schreiben, eine Methode mit der folgenden Signatur:

```csharp
ValidateAsync(CookieValidatePrincipalContext)
```

ASP.NET Core Identity implementiert diese Überprüfung als Teil seiner [SecurityStampValidator](/dotnet/api/microsoft.aspnetcore.identity.securitystampvalidator-1.validateasync). Ein Beispiel sieht folgendermaßen aus:

```csharp
public static class LastChangedValidator
{
    public static async Task ValidateAsync(CookieValidatePrincipalContext context)
    {
        // Pull database from registered DI services.
        var userRepository = 
            context.HttpContext.RequestServices
                .GetRequiredService<IUserRepository>();
        var userPrincipal = context.Principal;

        // Look for the last changed claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

Registrieren Sie das Ereignis während der Konfiguration der Cookie-Authentifizierung in der `Configure` Methode:

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    Events = new CookieAuthenticationEvents
    {
        OnValidatePrincipal = LastChangedValidator.ValidateAsync
    }
});
```

::: moniker-end

Betrachten Sie eine Situation, in dem der Name des Benutzers aktualisiert &mdash; eine Entscheidung, die Sicherheit in irgendeiner Weise nicht beeinträchtigt. Wenn Sie den Benutzerprinzipal zerstörungsfrei aktualisieren möchten, rufen Sie `context.ReplacePrincipal` und legen Sie die `context.ShouldRenew` Eigenschaft `true`.

> [!WARNING]
> Die hier beschriebene Vorgehensweise wird bei jeder Anforderung ausgelöst. Dies kann zu einer Leistungseinbuße für die app führen.

## <a name="persistent-cookies"></a>Beständige cookies

Sie sollten die Cookies, das über Browsersitzungen hinweg beizubehalten. Diese Persistenz sollte nur mit expliziten benutzerzustimmung mit "Erinnerung" das Kontrollkästchen für die Anmeldung oder ein ähnlicher Mechanismus aktiviert werden. 

Der folgende Codeausschnitt erstellt eine Identität und die entsprechenden Cookie, das über den Browser Closures zurückgegriffen werden kann. Alle gleitenden ablaufeinstellungen, die zuvor konfiguriert haben, werden berücksichtigt. Wenn das Cookie abläuft, während der Browser geschlossen wird, löscht der Browser das Cookie an, nachdem er neu gestartet wurde.

::: moniker range=">= aspnetcore-2.0"

```csharp
await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

Die [AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.authentication.authenticationproperties?view=aspnetcore-2.0) Klasse befindet sich in der `Microsoft.AspNetCore.Authentication` Namespace.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
await HttpContext.Authentication.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

Die [AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.http.authentication.authenticationproperties?view=aspnetcore-1.1) Klasse befindet sich in der `Microsoft.AspNetCore.Http.Authentication` Namespace.

::: moniker-end

## <a name="absolute-cookie-expiration"></a>Absolute cookieablauf

Sie können festlegen, dass eine absolute Ablaufzeit mit `ExpiresUtc`. Sie müssen auch festlegen, um ein permanentes Cookie zu erstellen, `IsPersistent`; andernfalls das Cookie wird mit einer Lebensdauer sitzungsbasierte erstellt und kann entweder vor dem ablaufen, oder nach die Authentifizierung ticket, das sie enthält. Beim `ExpiresUtc` wird festgelegt, `SignInAsync`, er überschreibt den Wert von der `ExpireTimeSpan` Option `CookieAuthenticationOptions`, sofern festgelegt.

Der folgende Codeausschnitt erstellt eine Identität und die entsprechenden Cookie, das in der Regel 20 Minuten dauert. Dies ignoriert alle gleitenden ablaufeinstellungen, die zuvor konfiguriert haben.

::: moniker range=">= aspnetcore-2.0"

```csharp
await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
await HttpContext.Authentication.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Auth 2.0 Änderungen / Migration-Ankündigung](https://github.com/aspnet/Announcements/issues/262)
* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [Überprüfen der Richtlinie der richtlinienbasierten-Funktion](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>

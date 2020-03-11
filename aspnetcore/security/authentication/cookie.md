---
title: Verwenden der Cookie-Authentifizierung ohne ASP.net Core Identität
author: rick-anderson
description: Erfahren Sie, wie Sie die Cookie-Authentifizierung ohne ASP.net Core Identität verwenden.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
uid: security/authentication/cookie
ms.openlocfilehash: 64f881441a7a7f9a5529cb6ee5ce81142ccd69e6
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653419"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a>Verwenden der Cookie-Authentifizierung ohne ASP.net Core Identität

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.net Core Identity ist ein vollständiger Authentifizierungs Anbieter mit vollem Funktionsumfang zum Erstellen und warten von Anmeldungen. Es kann jedoch ein cookiebasierter Authentifizierungs Anbieter ohne ASP.net Core Identität verwendet werden. Weitere Informationen finden Sie unter <xref:security/authentication/identity>.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Zu Demonstrationszwecken in der Beispiel-APP ist das Benutzerkonto für den hypothetischen Benutzer Maria Rodriguez in der APP hart codiert. Verwenden Sie die **e-Mail-** Adresse `maria.rodriguez@contoso.com` und jedes Kennwort, um den Benutzer anzumelden. Der Benutzer wird in der `AuthenticateUser`-Methode in der Datei *pages/Account/Login. cshtml. cs* authentifiziert. In einem realen Beispiel wird der Benutzer für eine-Datenbank authentifiziert.

## <a name="configuration"></a>Konfiguration

Wenn die APP das [Metapaket Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app)nicht verwendet, erstellen Sie einen Paket Verweis in der Projektdatei für das Paket [Microsoft. aspnetcore. Authentication. Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .

Erstellen Sie in der `Startup.ConfigureServices`-Methode die Authentifizierungs-Middleware-Dienste mit den Methoden <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> und <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>:

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> an `AddAuthentication` wird das Standard Authentifizierungsschema für die APP festgelegt. `AuthenticationScheme` ist nützlich, wenn mehrere Instanzen der Cookie-Authentifizierung vorhanden sind und Sie die [Autorisierung mit einem bestimmten Schema durch](xref:security/authorization/limitingidentitybyscheme)laufen möchten. Beim Festlegen der `AuthenticationScheme` auf [cookieauthenticationdefaults. authenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) wird der Wert "Cookies" für das Schema bereitstellt. Sie können einen beliebigen Zeichen folgen Wert angeben, der das Schema unterscheidet.

Das Authentifizierungsschema der APP unterscheidet sich vom Cookie-Authentifizierungsschema der app. Wenn kein cookieauthentifizierungsschema für <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>bereitgestellt wird, wird `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies") verwendet.

Die <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential>-Eigenschaft des Authentifizierungs Cookies ist standardmäßig auf `true` festgelegt. Authentifizierungs Cookies sind zulässig, wenn ein Website Besucher der Datensammlung nicht zugestimmt hat. Weitere Informationen finden Sie unter <xref:security/gdpr#essential-cookies>.

In `Startup.Configure``UseAuthentication` und `UseAuthorization`, um die `HttpContext.User`-Eigenschaft festzulegen und die Autorisierungs Middleware für Anforderungen auszuführen. Rufen Sie die Methoden `UseAuthentication` und `UseAuthorization` auf, bevor Sie `UseEndpoints`aufrufen:

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

Die <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>-Klasse wird verwendet, um die Authentifizierungs Anbieter Optionen zu konfigurieren.

Legen Sie `CookieAuthenticationOptions` in der Dienst Konfiguration für die Authentifizierung in der `Startup.ConfigureServices`-Methode fest:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a>Cookie-Richtlinien Middleware

[Cookingrichtlinienmiddleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) ermöglicht Cookingrichtlinienfunktionen. Das Hinzufügen der Middleware zur APP-Verarbeitungs Pipeline ist die Reihenfolge,&mdash;Sie nur auf Downstreamkomponenten wirkt, die in der Pipeline registriert sind.

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

Verwenden Sie <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions>, die der cookierichtlinienmiddleware bereitgestellt werden, um globale Merkmale der Cookieverarbeitung zu steuern und in cookieverarbeitungs Handler zu stecken, wenn Cookies angehängt oder gelöscht

Der Standard <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> Wert ist `SameSiteMode.Lax`, um die OAuth2-Authentifizierung zuzulassen. Legen Sie die `MinimumSameSitePolicy`fest, um eine Richtlinie für denselben Standort `SameSiteMode.Strict`streng zu erzwingen. Obwohl diese Einstellung OAuth2 und andere Ursprungs übergreifende Authentifizierungs Schemas unterbricht, erhöht Sie die Sicherheitsstufe für andere Typen von apps, die sich nicht auf die Ursprungs übergreifende Anforderungs Verarbeitung stützen.

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

Die Middleware-Einstellung der Cookie-Richtlinie für `MinimumSameSitePolicy` kann die Einstellung `Cookie.SameSite` in `CookieAuthenticationOptions` Einstellungen entsprechend der folgenden Matrix beeinflussen.

| MinimumSameSitePolicy | Cookie.SameSite | Resultierende Cookie. SameSite-Einstellung |
| --------------------- | --------------- | --------------------------------- |
| SameSiteMode.None     | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Lax      | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Lax<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Strict   | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Strict<br>SameSiteMode.Strict<br>SameSiteMode.Strict |

## <a name="create-an-authentication-cookie"></a>Erstellen eines Authentifizierungs Cookies

Erstellen Sie eine <xref:System.Security.Claims.ClaimsPrincipal>, um ein Cookie mit Benutzerinformationen zu erstellen. Die Benutzerinformationen werden serialisiert und im Cookie gespeichert. 

Erstellen Sie eine <xref:System.Security.Claims.ClaimsIdentity> mit allen erforderlichen <xref:System.Security.Claims.Claim>s, und rufen Sie <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> auf, um den Benutzer anzumelden:

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

`SignInAsync` erstellt ein verschlüsseltes Cookie und fügt es der aktuellen Antwort hinzu. Wenn `AuthenticationScheme` nicht angegeben ist, wird das Standardschema verwendet.

ASP.net Core- [Datenschutz](xref:security/data-protection/using-data-protection) System wird für die Verschlüsselung verwendet. Konfigurieren Sie für eine APP, die auf mehreren Computern gehostet wird, den Lastenausgleich über apps oder eine Webfarm, den [Datenschutz](xref:security/data-protection/configuration/overview) so, dass derselbe Schlüsselbund und der APP-Bezeichner verwendet werden.

## <a name="sign-out"></a>Abmelden

Um den aktuellen Benutzer zu abmelden und das Cookie zu löschen, wenden Sie <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>an:

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

Wenn `CookieAuthenticationDefaults.AuthenticationScheme` (oder "Cookies") nicht als Schema (z. b. "contosocookie") verwendet wird, geben Sie das Schema an, das beim Konfigurieren des Authentifizierungs Anbieters verwendet wird. Andernfalls wird das Standardschema verwendet.

## <a name="react-to-back-end-changes"></a>Reagieren auf Back-End-Änderungen

Nachdem ein Cookie erstellt wurde, ist das Cookie die einzige Quelle der Identität. Wenn ein Benutzerkonto in Back-End-Systemen deaktiviert ist:

* Das cookieauthentifizierungssystem der APP verarbeitet weiterhin Anforderungen auf der Grundlage des Authentifizierungs Cookies.
* Der Benutzer bleibt bei der App angemeldet, solange das Authentifizierungs Cookie gültig ist.

Das <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> Ereignis kann verwendet werden, um die Überprüfung der Cookie-Identität abzufangen und zu überschreiben. Das Überprüfen des Cookies bei jeder Anforderung verringert das Risiko, dass widerrufene Benutzer auf die App zugreifen.

Ein Ansatz für die Cookievalidierung basiert darauf, wann die Benutzerdatenbank geändert wird. Wenn die Datenbank nicht geändert wurde, weil das Cookie des Benutzers ausgestellt wurde, ist es nicht erforderlich, den Benutzer erneut zu authentifizieren, wenn das Cookie noch gültig ist. In der Beispiel-APP wird die Datenbank in `IUserRepository` implementiert und speichert einen `LastChanged` Wert. Wenn ein Benutzer in der Datenbank aktualisiert wird, wird der `LastChanged` Wert auf die aktuelle Zeit festgelegt.

Erstellen Sie das Cookie mit einem `LastChanged`-Anspruch, der den aktuellen `LastChanged` Wert aus der Datenbank enthält, um ein Cookie für ungültig zu erklären, wenn sich die Datenbank auf der Grundlage des `LastChanged` Werts ändert:

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

Um eine außer Kraft setzung für das `ValidatePrincipal`-Ereignis zu implementieren, schreiben Sie eine-Methode mit der folgenden Signatur in eine Klasse, die von <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>abgeleitet wird:

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

Im folgenden finden Sie eine Beispiel Implementierung von `CookieAuthenticationEvents`:

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

Registrieren Sie die Ereignis Instanz während der Registrierung des Cookie-Dienstanbieter in der `Startup.ConfigureServices` Methode. Stellen Sie eine Bereichs bezogene [Dienst Registrierung](xref:fundamentals/dependency-injection#service-lifetimes) für Ihre `CustomCookieAuthenticationEvents`-Klasse bereit:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

Stellen Sie sich eine Situation vor, in der der Name des Benutzers&mdash;eine Entscheidung aktualisiert wird, die die Sicherheit in keiner Weise beeinflusst. Wenn Sie den Benutzer Prinzipal nicht dedestruktiv aktualisieren möchten, müssen Sie `context.ReplacePrincipal` aufrufen und die `context.ShouldRenew`-Eigenschaft auf `true`festlegen.

> [!WARNING]
> Der hier beschriebene Ansatz wird bei jeder Anforderung ausgelöst. Das Überprüfen von Authentifizierungs Cookies für alle Benutzer bei jeder Anforderung kann zu einer hohen Leistungs Einbuße für die APP führen.

## <a name="persistent-cookies"></a>Persistente Cookies

Möglicherweise möchten Sie, dass das Cookie über Browsersitzungen hinweg beibehalten wird. Diese Persistenz sollte nur mit der expliziten Zustimmung des Benutzers mit dem Kontrollkästchen "merken" bei der Anmeldung oder einem ähnlichen Mechanismus aktiviert werden. 

Der folgende Code Ausschnitt erstellt eine Identität und entsprechendes Cookie, das bei Browser Abschlüssen überlebt. Alle zuvor konfigurierten gleitenden Ablauf Einstellungen werden berücksichtigt. Wenn das Cookie abläuft, während der Browser geschlossen wird, löscht der Browser das Cookie, sobald es neu gestartet wird.

Legen Sie <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> auf `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>fest:

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-cookie-expiration"></a>Absoluter Cookieablauf

Eine absolute Ablaufzeit kann mit <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>festgelegt werden. Um ein dauerhaftes Cookie zu erstellen, müssen `IsPersistent` ebenfalls festgelegt werden. Andernfalls wird das Cookie mit einer Sitzungs basierten Lebensdauer erstellt und kann entweder vor oder nach dem Authentifizierungs Ticket ablaufen, das es enthält. Wenn `ExpiresUtc` festgelegt ist, wird der Wert der <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan>-Option von <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>überschrieben, sofern festgelegt.

Der folgende Code Ausschnitt erstellt eine Identität und entsprechendes Cookie, das 20 Minuten lang dauert. Dadurch werden alle zuvor konfigurierten gleitenden Ablauf Einstellungen ignoriert.

```csharp
// using Microsoft.AspNetCore.Authentication;

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

::: moniker range="< aspnetcore-3.0"

ASP.net Core Identity ist ein vollständiger Authentifizierungs Anbieter mit vollem Funktionsumfang zum Erstellen und warten von Anmeldungen. Es kann jedoch ein cookiebasierter Authentifizierungs Anbieter ohne ASP.net Core Identität verwendet werden. Weitere Informationen finden Sie unter <xref:security/authentication/identity>.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Zu Demonstrationszwecken in der Beispiel-APP ist das Benutzerkonto für den hypothetischen Benutzer Maria Rodriguez in der APP hart codiert. Verwenden Sie die **e-Mail-** Adresse `maria.rodriguez@contoso.com` und jedes Kennwort, um den Benutzer anzumelden. Der Benutzer wird in der `AuthenticateUser`-Methode in der Datei *pages/Account/Login. cshtml. cs* authentifiziert. In einem realen Beispiel wird der Benutzer für eine-Datenbank authentifiziert.

## <a name="configuration"></a>Konfiguration

Wenn die APP das [Metapaket Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app)nicht verwendet, erstellen Sie einen Paket Verweis in der Projektdatei für das Paket [Microsoft. aspnetcore. Authentication. Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .

Erstellen Sie in der `Startup.ConfigureServices`-Methode den Authentifizierungs-Middleware-Dienst mit den Methoden <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> und <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>:

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> an `AddAuthentication` wird das Standard Authentifizierungsschema für die APP festgelegt. `AuthenticationScheme` ist nützlich, wenn mehrere Instanzen der Cookie-Authentifizierung vorhanden sind und Sie die [Autorisierung mit einem bestimmten Schema durch](xref:security/authorization/limitingidentitybyscheme)laufen möchten. Beim Festlegen der `AuthenticationScheme` auf [cookieauthenticationdefaults. authenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) wird der Wert "Cookies" für das Schema bereitstellt. Sie können einen beliebigen Zeichen folgen Wert angeben, der das Schema unterscheidet.

Das Authentifizierungsschema der APP unterscheidet sich vom Cookie-Authentifizierungsschema der app. Wenn kein cookieauthentifizierungsschema für <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>bereitgestellt wird, wird `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies") verwendet.

Die <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential>-Eigenschaft des Authentifizierungs Cookies ist standardmäßig auf `true` festgelegt. Authentifizierungs Cookies sind zulässig, wenn ein Website Besucher der Datensammlung nicht zugestimmt hat. Weitere Informationen finden Sie unter <xref:security/gdpr#essential-cookies>.

Rufen Sie in der `Startup.Configure`-Methode die `UseAuthentication`-Methode auf, um die Authentifizierungs Middleware aufzurufen, die die `HttpContext.User`-Eigenschaft festlegt. Rufen Sie die `UseAuthentication`-Methode auf, bevor Sie `UseMvcWithDefaultRoute` oder `UseMvc`aufrufen:

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

Die <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>-Klasse wird verwendet, um die Authentifizierungs Anbieter Optionen zu konfigurieren.

Legen Sie `CookieAuthenticationOptions` in der Dienst Konfiguration für die Authentifizierung in der `Startup.ConfigureServices`-Methode fest:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a>Cookie-Richtlinien Middleware

[Cookingrichtlinienmiddleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) ermöglicht Cookingrichtlinienfunktionen. Das Hinzufügen der Middleware zur APP-Verarbeitungs Pipeline ist die Reihenfolge,&mdash;Sie nur auf Downstreamkomponenten wirkt, die in der Pipeline registriert sind.

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

Verwenden Sie <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions>, die der cookierichtlinienmiddleware bereitgestellt werden, um globale Merkmale der Cookieverarbeitung zu steuern und in cookieverarbeitungs Handler zu stecken, wenn Cookies angehängt oder gelöscht

Der Standard <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> Wert ist `SameSiteMode.Lax`, um die OAuth2-Authentifizierung zuzulassen. Legen Sie die `MinimumSameSitePolicy`fest, um eine Richtlinie für denselben Standort `SameSiteMode.Strict`streng zu erzwingen. Obwohl diese Einstellung OAuth2 und andere Ursprungs übergreifende Authentifizierungs Schemas unterbricht, erhöht Sie die Sicherheitsstufe für andere Typen von apps, die sich nicht auf die Ursprungs übergreifende Anforderungs Verarbeitung stützen.

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

Die Middleware-Einstellung der Cookie-Richtlinie für `MinimumSameSitePolicy` kann die Einstellung `Cookie.SameSite` in `CookieAuthenticationOptions` Einstellungen entsprechend der folgenden Matrix beeinflussen.

| MinimumSameSitePolicy | Cookie.SameSite | Resultierende Cookie. SameSite-Einstellung |
| --------------------- | --------------- | --------------------------------- |
| SameSiteMode.None     | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Lax      | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Lax<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Strict   | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Strict<br>SameSiteMode.Strict<br>SameSiteMode.Strict |

## <a name="create-an-authentication-cookie"></a>Erstellen eines Authentifizierungs Cookies

Erstellen Sie eine <xref:System.Security.Claims.ClaimsPrincipal>, um ein Cookie mit Benutzerinformationen zu erstellen. Die Benutzerinformationen werden serialisiert und im Cookie gespeichert. 

Erstellen Sie eine <xref:System.Security.Claims.ClaimsIdentity> mit allen erforderlichen <xref:System.Security.Claims.Claim>s, und rufen Sie <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> auf, um den Benutzer anzumelden:

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

`SignInAsync` erstellt ein verschlüsseltes Cookie und fügt es der aktuellen Antwort hinzu. Wenn `AuthenticationScheme` nicht angegeben ist, wird das Standardschema verwendet.

ASP.net Core- [Datenschutz](xref:security/data-protection/using-data-protection) System wird für die Verschlüsselung verwendet. Konfigurieren Sie für eine APP, die auf mehreren Computern gehostet wird, den Lastenausgleich über apps oder eine Webfarm, den [Datenschutz](xref:security/data-protection/configuration/overview) so, dass derselbe Schlüsselbund und der APP-Bezeichner verwendet werden.

## <a name="sign-out"></a>Abmelden

Um den aktuellen Benutzer zu abmelden und das Cookie zu löschen, wenden Sie <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>an:

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

Wenn `CookieAuthenticationDefaults.AuthenticationScheme` (oder "Cookies") nicht als Schema (z. b. "contosocookie") verwendet wird, geben Sie das Schema an, das beim Konfigurieren des Authentifizierungs Anbieters verwendet wird. Andernfalls wird das Standardschema verwendet.

## <a name="react-to-back-end-changes"></a>Reagieren auf Back-End-Änderungen

Nachdem ein Cookie erstellt wurde, ist das Cookie die einzige Quelle der Identität. Wenn ein Benutzerkonto in Back-End-Systemen deaktiviert ist:

* Das cookieauthentifizierungssystem der APP verarbeitet weiterhin Anforderungen auf der Grundlage des Authentifizierungs Cookies.
* Der Benutzer bleibt bei der App angemeldet, solange das Authentifizierungs Cookie gültig ist.

Das <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> Ereignis kann verwendet werden, um die Überprüfung der Cookie-Identität abzufangen und zu überschreiben. Das Überprüfen des Cookies bei jeder Anforderung verringert das Risiko, dass widerrufene Benutzer auf die App zugreifen.

Ein Ansatz für die Cookievalidierung basiert darauf, wann die Benutzerdatenbank geändert wird. Wenn die Datenbank nicht geändert wurde, weil das Cookie des Benutzers ausgestellt wurde, ist es nicht erforderlich, den Benutzer erneut zu authentifizieren, wenn das Cookie noch gültig ist. In der Beispiel-APP wird die Datenbank in `IUserRepository` implementiert und speichert einen `LastChanged` Wert. Wenn ein Benutzer in der Datenbank aktualisiert wird, wird der `LastChanged` Wert auf die aktuelle Zeit festgelegt.

Erstellen Sie das Cookie mit einem `LastChanged`-Anspruch, der den aktuellen `LastChanged` Wert aus der Datenbank enthält, um ein Cookie für ungültig zu erklären, wenn sich die Datenbank auf der Grundlage des `LastChanged` Werts ändert:

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

Um eine außer Kraft setzung für das `ValidatePrincipal`-Ereignis zu implementieren, schreiben Sie eine-Methode mit der folgenden Signatur in eine Klasse, die von <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>abgeleitet wird:

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

Im folgenden finden Sie eine Beispiel Implementierung von `CookieAuthenticationEvents`:

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

Registrieren Sie die Ereignis Instanz während der Registrierung des Cookie-Dienstanbieter in der `Startup.ConfigureServices` Methode. Stellen Sie eine Bereichs bezogene [Dienst Registrierung](xref:fundamentals/dependency-injection#service-lifetimes) für Ihre `CustomCookieAuthenticationEvents`-Klasse bereit:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

Stellen Sie sich eine Situation vor, in der der Name des Benutzers&mdash;eine Entscheidung aktualisiert wird, die die Sicherheit in keiner Weise beeinflusst. Wenn Sie den Benutzer Prinzipal nicht dedestruktiv aktualisieren möchten, müssen Sie `context.ReplacePrincipal` aufrufen und die `context.ShouldRenew`-Eigenschaft auf `true`festlegen.

> [!WARNING]
> Der hier beschriebene Ansatz wird bei jeder Anforderung ausgelöst. Das Überprüfen von Authentifizierungs Cookies für alle Benutzer bei jeder Anforderung kann zu einer hohen Leistungs Einbuße für die APP führen.

## <a name="persistent-cookies"></a>Persistente Cookies

Möglicherweise möchten Sie, dass das Cookie über Browsersitzungen hinweg beibehalten wird. Diese Persistenz sollte nur mit der expliziten Zustimmung des Benutzers mit dem Kontrollkästchen "merken" bei der Anmeldung oder einem ähnlichen Mechanismus aktiviert werden. 

Der folgende Code Ausschnitt erstellt eine Identität und entsprechendes Cookie, das bei Browser Abschlüssen überlebt. Alle zuvor konfigurierten gleitenden Ablauf Einstellungen werden berücksichtigt. Wenn das Cookie abläuft, während der Browser geschlossen wird, löscht der Browser das Cookie, sobald es neu gestartet wird.

Legen Sie <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> auf `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>fest:

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-cookie-expiration"></a>Absoluter Cookieablauf

Eine absolute Ablaufzeit kann mit <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>festgelegt werden. Um ein dauerhaftes Cookie zu erstellen, müssen `IsPersistent` ebenfalls festgelegt werden. Andernfalls wird das Cookie mit einer Sitzungs basierten Lebensdauer erstellt und kann entweder vor oder nach dem Authentifizierungs Ticket ablaufen, das es enthält. Wenn `ExpiresUtc` festgelegt ist, wird der Wert der <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan>-Option von <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>überschrieben, sofern festgelegt.

Der folgende Code Ausschnitt erstellt eine Identität und entsprechendes Cookie, das 20 Minuten lang dauert. Dadurch werden alle zuvor konfigurierten gleitenden Ablauf Einstellungen ignoriert.

```csharp
// using Microsoft.AspNetCore.Authentication;

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

## <a name="additional-resources"></a>Weitere Ressourcen

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [Richtlinien basierte Rollen Überprüfungen](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>

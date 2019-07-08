---
title: Verwenden der Cookieauthentifizierung ohne ASP.NET Core Identity
author: rick-anderson
description: Informationen Sie zum Verwenden der Cookieauthentifizierung ohne ASP.NET Core Identity.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/07/2019
uid: security/authentication/cookie
ms.openlocfilehash: bbba2e77f806e1ed30bb734763cdbaedc1471d62
ms.sourcegitcommit: 91cc1f07ef178ab709ea42f8b3a10399c970496e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/08/2019
ms.locfileid: "67622745"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a>Verwenden der Cookieauthentifizierung ohne ASP.NET Core Identity

Durch [Rick Anderson](https://twitter.com/RickAndMSFT) und [Luke Latham](https://github.com/guardrex)

ASP.NET Core Identity ist eine vollständige, voll ausgestattete Authentication-Anbieter zum Erstellen und Verwalten von Anmeldungen. Allerdings kann ein Authentifizierungsanbieter für cookiebasierte Authentifizierung ohne ASP.NET Core Identity verwendet werden. Weitere Informationen finden Sie unter <xref:security/authentication/identity>.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Zu Demonstrationszwecken in der Beispiel-app ist das Benutzerkonto für den hypothetischen Benutzer Maria Rodriguez, in der app hartcodiert. Verwenden der **-e-Mail** Benutzernamen `maria.rodriguez@contoso.com` und eines Kennworts zum Anmelden des Benutzers. Der Benutzer wird authentifiziert, der `AuthenticateUser` -Methode in der die *Pages/Account/Login.cshtml.cs* Datei. Im Real-World-Beispiel würde der Benutzer für eine Datenbank authentifiziert werden.

## <a name="configuration"></a>Konfiguration

Wenn die app verwenden, nicht die [Microsoft.AspNetCore.App metapaket](xref:fundamentals/metapackage-app), erstellen Sie einen Paketverweis in die Projektdatei für die [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) Paket.

In der `Startup.ConfigureServices` -Methode, erstellen Sie den Authentifizierungs-Middleware-Dienst, mit der <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> und <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> Methoden:

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> die an `AddAuthentication` legt das Authentifizierungsschema "Standard" für die app fest. `AuthenticationScheme` ist nützlich, wenn mehrere Instanzen der Cookie-Authentifizierung vorhanden sind, und Sie möchten [autorisieren mit einem bestimmten Schema](xref:security/authorization/limitingidentitybyscheme). Festlegen der `AuthenticationScheme` zu [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) "Cookies" der Wert für das Schema enthält. Sie können einen beliebigen Zeichenfolgenwert angeben, der das Schema unterscheidet.

Der app-Authentifizierungsschema unterscheidet sich von der app Cookie-Authentifizierungsschema. Wenn ein Cookie-Authentifizierungsschema bereitgestellt ist nicht <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, verwendet `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").

Des Authentifizierungscookies <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> -Eigenschaftensatz auf `true` standardmäßig. Authentifizierungscookies sind zulässig, wenn ein Besucher der Website, die Datensammlung zugestimmt wurde nicht. Weitere Informationen finden Sie unter <xref:security/gdpr#essential-cookies>.

In der `Startup.Configure` -Methode, rufen die `UseAuthentication` Middleware für die Authentifizierung, die festlegt, aufzurufenden Methode der `HttpContext.User` Eigenschaft. Rufen Sie die `UseAuthentication` Methode vor dem Aufruf `UseMvcWithDefaultRoute` oder `UseMvc`:

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

Die <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> Klasse wird verwendet, um die Optionen für die Authentifizierung-Anbieter konfigurieren.

Legen Sie `CookieAuthenticationOptions` in der Dienstkonfiguration für die Authentifizierung in der `Startup.ConfigureServices` Methode:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a>Richtlinie Cookie-Middleware

[Richtlinie Cookiemiddleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) Cookie Funktionen ermöglicht. Hinzufügen der Middleware zur Verarbeitungspipeline app ist die Reihenfolge, die vertrauliche&mdash;sie gilt nur für Downstreamkomponenten in der Pipeline registriert.

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

Verwendung <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> bereitgestellt, um die Cookie-Richtlinie-Middleware zu globale Eigenschaften der Cookie-Verarbeitung und Hook in von Cookie-Verarbeitungshandler zu steuern, wenn Cookies angefügt oder gelöscht werden.

Der Standardwert <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> Wert `SameSiteMode.Lax` um OAuth2-Authentifizierung zu ermöglichen. Genau eine SameSite-Richtlinie erzwingen `SameSiteMode.Strict`legen die `MinimumSameSitePolicy`. Auch wenn diese Einstellung OAuth2 und andere Cross-Origin-Authentifizierungsschemas unterbrochen wird, erhöht sie die Sicherheitsstufe Cookie für andere Typen von apps, die Verarbeitung von Cross-Origin-Anforderungen nicht benötigen.

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

Die Richtlinie Cookie-Middleware-Einstellung für `MinimumSameSitePolicy` kann die Einstellung der beeinflussen `Cookie.SameSite` in `CookieAuthenticationOptions` gemäß der folgenden Matrix.

| MinimumSameSitePolicy | Cookie.SameSite | Resultierende Cookie.SameSite-Einstellung |
| --------------------- | --------------- | --------------------------------- |
| SameSiteMode.None     | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Lax      | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Lax<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Strict   | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Strict<br>SameSiteMode.Strict<br>SameSiteMode.Strict |

## <a name="create-an-authentication-cookie"></a>Erstellen Sie ein Authentifizierungscookie

Um ein Cookie mit Benutzerinformationen zu erstellen, erstellen Sie eine <xref:System.Security.Claims.ClaimsPrincipal>. Die Benutzerinformationen serialisiert und im Cookie gespeichert. 

Erstellen Sie eine <xref:System.Security.Claims.ClaimsIdentity> mit ggf. erforderlichen <xref:System.Security.Claims.Claim>s, und rufen <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> zum Anmelden des Benutzers:

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

`SignInAsync` erstellt ein verschlüsseltes Cookie aus, und die aktuelle Antwort hinzugefügt. Wenn `AuthenticationScheme` nicht angegeben ist, wird das Standardschema verwendet.

ASP.NET Core [den Datenschutz](xref:security/data-protection/using-data-protection) System für die Verschlüsselung verwendet wird. Laden Sie für eine app auf mehreren Computern gehostet, Lastenausgleich zwischen verschiedenen apps oder mithilfe einer Webfarm [Schutz von Daten konfigurieren](xref:security/data-protection/configuration/overview) und mit dem Schlüsselbund für den gleichen app-Bezeichner.

## <a name="sign-out"></a>Abmelden

Um den aktuellen Benutzer abmelden, und ihre Cookies zu löschen, rufen Sie <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

Wenn `CookieAuthenticationDefaults.AuthenticationScheme` (oder "Cookies") wird nicht verwendet werden, da das Schema (z. B. "ContosoCookie"), geben Sie das Schema, das verwendet wird, wenn Sie den Authentifizierungsanbieter konfigurieren. Andernfalls wird das Standardschema verwendet.

## <a name="react-to-back-end-changes"></a>Reagieren Sie auf Änderungen der Back-end

Sobald ein Cookie erstellt wurde, ist das Cookie die einzige Quelle der Identität an. Wenn ein Benutzerkonto in Back-End-Systemen deaktiviert wird:

* Der app Cookie-Authentifizierungssystem her, die zum Verarbeiten von Anforderungen basierend auf das Authentifizierungscookie wird fortgesetzt.
* Der Benutzer bleibt mit Vorzeichen in die app, solange das Authentifizierungscookie gültig ist.

Die <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> -Ereignis kann zum Abfangen und überschreiben die Überprüfung der Identität ein Cookie verwendet werden. Überprüfen das Cookie bei jeder Anforderung verringert das Risiko der gesperrten Benutzer Zugriff auf die app.

Ein Ansatz für die gültigkeitsprüfung basiert zu verfolgen, wenn die Benutzerdatenbank geändert wird. Wenn die Datenbank geändert wurde, nachdem der Benutzer Cookies ausgegeben wurde, ist es nicht erforderlich, die der Benutzer erneut authentifizieren, wenn ihre Cookie noch gültig ist. In der Beispiel-app wird in die Datenbank implementiert `IUserRepository` und speichert eine `LastChanged` Wert. Wenn ein Benutzer in der Datenbank aktualisiert wird die `LastChanged` Wert wird auf die aktuelle Uhrzeit festgelegt.

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

Implementieren Sie eine Außerkraftsetzung für die `ValidatePrincipal` Ereignis schreiben, eine Methode mit der folgenden Signatur in eine abgeleitete Klasse <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

Im folgenden finden Sie eine beispielimplementierung von `CookieAuthenticationEvents`:

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

Registrieren Sie die Ereignisse-Instanz während der Cookie-Service-Registrierung in der `Startup.ConfigureServices` Methode. Geben Sie einen [begrenzt dienstregistrierung](xref:fundamentals/dependency-injection#service-lifetimes) für Ihre `CustomCookieAuthenticationEvents` Klasse:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

Betrachten Sie eine Situation, in dem der Name des Benutzers aktualisiert&mdash;eine Entscheidung, die Sicherheit in irgendeiner Weise nicht beeinträchtigt. Wenn Sie den Benutzerprinzipal zerstörungsfrei aktualisieren möchten, rufen Sie `context.ReplacePrincipal` und legen Sie die `context.ShouldRenew` Eigenschaft `true`.

> [!WARNING]
> Die hier beschriebene Vorgehensweise wird bei jeder Anforderung ausgelöst. Überprüfen des Authentifizierungscookies für alle Benutzer für jede Anforderung kann dazu führen, dass eine große Leistungseinbuße für die app.

## <a name="persistent-cookies"></a>Beständige cookies

Sie sollten die Cookies, das über Browsersitzungen hinweg beizubehalten. Diese Persistenz sollte nur mit expliziten benutzerzustimmung mit "Erinnerung" das Kontrollkästchen bei der Anmeldung oder ein ähnlicher Mechanismus aktiviert werden. 

Der folgende Codeausschnitt erstellt eine Identität und die entsprechenden Cookie, das über den Browser Closures zurückgegriffen werden kann. Alle gleitenden ablaufeinstellungen, die zuvor konfiguriert haben, werden berücksichtigt. Wenn das Cookie abläuft, während der Browser geschlossen wird, löscht der Browser das Cookie an, nachdem er neu gestartet wurde.

Legen Sie <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> zu `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:

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

## <a name="absolute-cookie-expiration"></a>Absolute cookieablauf

Eine absolute Ablaufzeit kann festgelegt werden, mit <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>. Erstellen Sie ein dauerhaftes Cookie, `IsPersistent` müssen ebenfalls festgelegt werden. Andernfalls wird das Cookie wird mit einer Lebensdauer sitzungsbasierte erstellt und kann ablaufen, entweder vor oder nach das Authentifizierungsticket, das es enthält. Wenn `ExpiresUtc` festgelegt ist, wird er überschreibt den Wert von der <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> Option <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, sofern festgelegt.

Der folgende Codeausschnitt erstellt eine Identität und die entsprechenden Cookie, das in der Regel 20 Minuten dauert. Dies ignoriert alle gleitenden ablaufeinstellungen, die zuvor konfiguriert haben.

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

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [Überprüfen der Richtlinie der richtlinienbasierten-Funktion](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>

---
title: Multi-Factor Authentication in ASP.net Core
author: damienbod
description: Erfahren Sie, wie Sie die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) in einer ASP.net Core-App einrichten.
monikerRange: '>= aspnetcore-3.1'
ms.author: rick-anderson
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Identity
uid: security/authentication/mfa
ms.openlocfilehash: 6220688d53f0718ca5be5f63dd5d9539d37e2391
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/18/2020
ms.locfileid: "79520146"
---
# <a name="multi-factor-authentication-in-aspnet-core"></a>Multi-Factor Authentication in ASP.net Core

Von [Damien Bowder](https://github.com/damienbod)

Die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) ist ein Prozess, bei dem ein Benutzer während eines Anmelde Ereignisses für zusätzliche Identifikationsformen angefordert wird. Diese Aufforderung kann darin bestehen, einen Code von einem Mobiltelefon einzugeben, eine FIDO2-Taste zu verwenden oder eine Fingerabdruck Überprüfung bereitzustellen. Wenn Sie eine zweite Form der Authentifizierung benötigen, wird die Sicherheit verbessert. Der zusätzliche Faktor ist nicht leicht von einem Angreifer abgerufen oder dupliziert.

In diesem Artikel werden die folgenden Bereiche behandelt:

* Was ist MFA und welche MFA-Flows werden empfohlen?
* Konfigurieren von MFA für Verwaltungs Seiten mithilfe von ASP.net Core Identity
* MFA-Anmelde Anforderung an OpenID Connect-Server senden
* Erzwingen, dass ASP.net Core OpenID Connect-Client MFA erfordert

## <a name="mfa-2fa"></a>MFA, 2FA

MFA erfordert mindestens zwei Arten von Beweisen für eine Identität, z. b. etwas, das Sie wissen, etwas, das Sie besitzen, oder die biometrische Validierung für den Benutzer.

Die zweistufige Authentifizierung (2FA) ist wie eine Teilmenge der MFA, aber der Unterschied, dass MFA zwei oder mehr Faktoren erfordern kann, um die Identität zu belegen.

### <a name="mfa-totp-time-based-one-time-password-algorithm"></a>MFA TOTP (zeitbasierter Algorithmus für einmal Kennwort)

MFA mit TOTP ist eine unterstützte Implementierung, die ASP.net Core Identityverwendet. Dies kann in Verbindung mit jeder kompatiblen Authentifikator-App verwendet werden, einschließlich:

* Microsoft Authenticator-App
* Google Authenticator-App

Weitere Implementierungsdetails finden Sie unter folgendem Link:

[Aktivieren der QR-Code Generierung für TOTP Authenticator-apps in ASP.net Core](xref:security/authentication/identity-enable-qrcodes)

### <a name="mfa-fido2-or-passwordless"></a>MFA FIDO2 oder passworeless

FIDO2 ist zurzeit:

* Die sicherste Methode zum Erreichen von MFA.
* Der einzige MFA-Flow, der vor Phishingangriffen schützt.

Derzeit unterstützt ASP.net Core FIDO2 nicht direkt. FIDO2 kann für MFA-oder passworeless-Flows verwendet werden.

Azure Active Directory bietet Unterstützung für FIDO2-und passworeless-Flows. Weitere Informationen finden Sie unter [passworeless Authentication Options for Azure Active Directory](/azure/active-directory/authentication/concept-authentication-passwordless).

### <a name="mfa-sms"></a>MFA-SMS

MFA mit SMS erhöht die Sicherheit im Vergleich zur Kenn Wort Authentifizierung (einzelner Faktor). Die Verwendung von SMS als zweiten Faktor ist jedoch nicht mehr empfehlenswert. Für diese Art der Implementierung sind zu viele bekannte Angriffsvektoren vorhanden.

[NIST-Richtlinien](https://pages.nist.gov/800-63-3/sp800-63b.html)

## <a name="configure-mfa-for-administration-pages-using-aspnet-core-opno-locidentity"></a>Konfigurieren von MFA für Verwaltungs Seiten mithilfe von ASP.net Core Identity

MFA kann für Benutzer erzwungen werden, um auf sensible Seiten innerhalb einer ASP.net Core Identity-App zuzugreifen. Dies kann bei apps nützlich sein, bei denen unterschiedliche Zugriffsebenen für die unterschiedlichen Identitäten vorhanden sind. Beispielsweise können Benutzer die Profildaten möglicherweise mithilfe einer Kenn Wort Anmeldung anzeigen, aber ein Administrator muss MFA verwenden, um auf die Verwaltungs Seiten zuzugreifen.

### <a name="extend-the-login-with-an-mfa-claim"></a>Erweitern der Anmeldung mit einem MFA-Anspruch

Der Democode ist das Setup mithilfe von ASP.net Core mit Identity und Razor pages. Die `AddIdentity`-Methode wird anstelle `AddDefaultIdentity` eines verwendet, sodass eine `IUserClaimsPrincipalFactory`-Implementierung verwendet werden kann, um nach einer erfolgreichen Anmeldung Ansprüche zur Identität hinzuzufügen.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));
    
    services.AddIdentity<IdentityUser, IdentityRole>(
            options => options.SignIn.RequireConfirmedAccount = false)
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddSingleton<IEmailSender, EmailSender>();
    services.AddScoped<IUserClaimsPrincipalFactory<IdentityUser>, 
        AdditionalUserClaimsPrincipalFactory>();

    services.AddAuthorization(options =>
        options.AddPolicy("TwoFactorEnabled",
            x => x.RequireClaim("amr", "mfa")));

    services.AddRazorPages();
}
```

Die `AdditionalUserClaimsPrincipalFactory`-Klasse fügt dem Benutzer Ansprüche den `amr` Anspruch nur nach einer erfolgreichen Anmeldung hinzu. Der Wert des Anspruchs wird aus der Datenbank gelesen. Der Anspruch wird hier hinzugefügt, da der Benutzer nur auf die höhere geschützte Ansicht zugreifen sollte, wenn die Identität mit MFA angemeldet ist. Wenn die Daten Bank Sicht direkt aus der Datenbank gelesen wird, anstatt den Anspruch zu verwenden, ist es möglich, ohne MFA direkt nach Aktivierung der MFA auf die Ansicht zuzugreifen.

```csharp
using Microsoft.AspNetCore.Identity;
using Microsoft.Extensions.Options;
using System.Collections.Generic;
using System.Security.Claims;
using System.Threading.Tasks;

namespace IdentityStandaloneMfa
{
    public class AdditionalUserClaimsPrincipalFactory : 
        UserClaimsPrincipalFactory<IdentityUser, IdentityRole>
    {
        public AdditionalUserClaimsPrincipalFactory( 
            UserManager<IdentityUser> userManager,
            RoleManager<IdentityRole> roleManager, 
            IOptions<IdentityOptions> optionsAccessor) 
            : base(userManager, roleManager, optionsAccessor)
        {
        }

        public async override Task<ClaimsPrincipal> CreateAsync(IdentityUser user)
        {
            var principal = await base.CreateAsync(user);
            var identity = (ClaimsIdentity)principal.Identity;

            var claims = new List<Claim>();

            if (user.TwoFactorEnabled)
            {
                claims.Add(new Claim("amr", "mfa"));
            }
            else
            {
                claims.Add(new Claim("amr", "pwd"));
            }

            identity.AddClaims(claims);
            return principal;
        }
    }
}
```

Da sich die Identity-Dienst Einrichtung in der `Startup`-Klasse geändert hat, müssen die Layouts der Identity aktualisiert werden. Gerüstbau der Identity Seiten in der app. Definieren Sie das Layout in der Datei *Identity/Account/Manage/_Layout. cshtml* .

```cshtml
@{
    Layout = "/Pages/Shared/_Layout.cshtml";
}
```

Weisen Sie das Layout auch für alle Seiten verwalten auf den Identity Seiten zu:

```cshtml
@{
    Layout = "_Layout.cshtml";
}
```

### <a name="validate-the-mfa-requirement-in-the-administration-page"></a>Überprüfen der MFA-Anforderung auf der Verwaltungsseite

Die Razor page "Administration" überprüft, ob der Benutzer sich mithilfe von MFA angemeldet hat. In der `OnGet`-Methode wird die Identität verwendet, um auf die Benutzer Ansprüche zuzugreifen. Der `amr`-Anspruch wird auf den Wert `mfa`geprüft. Wenn die Identität diesen Anspruch fehlt oder `false`, wird die Seite an die Seite MFA aktivieren umgeleitet. Dies ist möglich, da sich der Benutzer bereits angemeldet hat, aber ohne MFA.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;

namespace IdentityStandaloneMfa
{
    public class AdminModel : PageModel
    {
        public IActionResult OnGet()
        {
            var claimTwoFactorEnabled = 
                User.Claims.FirstOrDefault(t => t.Type == "amr");

            if (claimTwoFactorEnabled != null && 
                "mfa".Equals(claimTwoFactorEnabled.Value))
            {
                // You logged in with MFA, do the administrative stuff
            }
            else
            {
                return Redirect(
                    "/Identity/Account/Manage/TwoFactorAuthentication");
            }

            return Page();
        }
    }
}
```

### <a name="ui-logic-to-toggle-user-login-information"></a>UI-Logik zum Umschalten von Benutzer Anmelde Informationen

Beim Start wurde eine Autorisierungs Richtlinie hinzugefügt. Die Richtlinie erfordert den `amr` Anspruch mit dem Wert `mfa`.

```csharp
services.AddAuthorization(options =>
    options.AddPolicy("TwoFactorEnabled",
        x => x.RequireClaim("amr", "mfa")));
```

Diese Richtlinie kann dann in der Ansicht `_Layout` verwendet werden, um das Menü **Admin** mit der folgenden Warnung anzuzeigen oder auszublenden:

```cshtml
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Identity
@inject SignInManager<IdentityUser> SignInManager
@inject UserManager<IdentityUser> UserManager
@inject IAuthorizationService AuthorizationService
```

Wenn die Identität mit MFA angemeldet wurde, wird das Menü **Admin** ohne die QuickInfo-Warnung angezeigt. Wenn sich der Benutzer ohne MFA angemeldet hat, wird das Menü **Admin (nicht aktiviert)** zusammen mit der QuickInfo angezeigt, die den Benutzer darüber informiert (die Warnung wird erläutert).

```cshtml
@if (SignInManager.IsSignedIn(User))
{
    @if ((AuthorizationService.AuthorizeAsync(User, "TwoFactorEnabled")).Result.Succeeded)
    {
        <li class="nav-item">
            <a class="nav-link text-dark" asp-area="" asp-page="/Admin">Admin</a>
        </li>
    }
    else
    {
        <li class="nav-item">
            <a class="nav-link text-dark" asp-area="" asp-page="/Admin" 
               id="tooltip-demo"  
               data-toggle="tooltip" 
               data-placement="bottom" 
               title="MFA is NOT enabled. This is required for the Admin Page. If you have activated MFA, then logout, login again.">
                Admin (Not Enabled)
            </a>
        </li>
    }
}
```

Wenn sich der Benutzer ohne MFA anmeldet, wird die Warnung angezeigt:

![MFA-Administrator Authentifizierung](mfa/_static/identitystandalonemfa_01.png)

Wenn Sie auf den Link **Admin** klicken, wird der Benutzer zur MFA-Aktivierungs Ansicht umgeleitet:

![Administrator aktiviert die MFA-Authentifizierung](mfa/_static/identitystandalonemfa_02.png)

## <a name="send-mfa-sign-in-requirement-to-openid-connect-server"></a>MFA-Anmelde Anforderung an OpenID Connect-Server senden 

Der `acr_values`-Parameter kann verwendet werden, um den `mfa` erforderlichen Wert vom Client an den Server in einer Authentifizierungsanforderung zu übergeben.

> [!NOTE]
> Der `acr_values`-Parameter muss auf dem Open ID Connect-Server behandelt werden, damit dies funktioniert.

### <a name="openid-connect-aspnet-core-client"></a>OpenID Connect-ASP.net Core Client

Der ASP.net Core Razor Pages Open ID Connect-Client-App verwendet die `AddOpenIdConnect`-Methode, um sich beim Open ID Connect-Server anzumelden. Der `acr_values`-Parameter wird mit dem `mfa` Wert festgelegt und mit der Authentifizierungsanforderung gesendet. Der `OpenIdConnectEvents` wird zum Hinzufügen dieses verwendet.

Informationen zu den empfohlenen `acr_values`-Parameterwerten finden Sie unter [Referenzwerte für die Authentifizierungsmethode](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08).

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(options =>
    {
        options.DefaultScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme =
            OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.SignInScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.Authority = "<OpenID Connect server URL>";
        options.RequireHttpsMetadata = true;
        options.ClientId = "<OpenID Connect client ID>";
        options.ClientSecret = "<>";
        // Code with PKCE can also be used here
        options.ResponseType = "code id_token";
        options.Scope.Add("profile");
        options.Scope.Add("offline_access");
        options.SaveTokens = true;
        options.Events = new OpenIdConnectEvents
        {
            OnRedirectToIdentityProvider = context =>
            {
                context.ProtocolMessage.SetParameter("acr_values", "mfa");
                return Task.FromResult(0);
            }
        };
    });
```

### <a name="example-openid-connect-identityserver-4-server-with-aspnet-core-opno-locidentity"></a>Beispiel für einen OpenID Connect identityserver 4-Server mit ASP.net Core Identity

Auf dem OpenID Connect-Server, der mit ASP.net Core Identity mit MVC-Ansichten implementiert wird, wird eine neue Ansicht mit dem Namen *ErrorEnable2FA. cshtml* erstellt. Die Ansicht:

* Zeigt an, ob das Identity aus einer APP stammt, die MFA erfordert, der Benutzer dies jedoch nicht in Identityaktiviert hat.
* Informiert den Benutzer und fügt einen Link hinzu, um diesen zu aktivieren.

```cshtml
@{
    ViewData["Title"] = "ErrorEnable2FA";
}

<h1>The client application requires you to have MFA enabled. Enable this, try login again.</h1>

<br />

You can enable MFA to login here:

<br />

<a asp-controller="Manage" asp-action="TwoFactorAuthentication">Enable MFA</a>
```

In der `Login`-Methode wird die `IIdentityServerInteractionService` Schnittstellen Implementierung `_interaction` für den Zugriff auf die Open ID Connect-Anforderungs Parameter verwendet. Auf den `acr_values`-Parameter wird mit der `AcrValues`-Eigenschaft zugegriffen. Da der Client dies mit `mfa` Satz gesendet hat, kann dies aktiviert werden.

Wenn MFA erforderlich ist und für den Benutzer in ASP.net Core Identity MFA aktiviert ist, wird die Anmeldung fortgesetzt. Wenn für den Benutzer keine MFA aktiviert ist, wird der Benutzer zur benutzerdefinierten Ansicht *ErrorEnable2FA. cshtml*umgeleitet. Anschließend ASP.net Core Identity den Benutzer signiert.

```csharp
//
// POST: /Account/Login
[HttpPost]
[AllowAnonymous]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Login(LoginInputModel model)
{
    var returnUrl = model.ReturnUrl;
    var context = 
        await _interaction.GetAuthorizationContextAsync(returnUrl);
    var requires2Fa = 
        context?.AcrValues.Count(t => t.Contains("mfa")) >= 1;

    var user = await _userManager.FindByNameAsync(model.Email);
    if (user != null && !user.TwoFactorEnabled && requires2Fa)
    {
        return RedirectToAction(nameof(ErrorEnable2FA));
    }

    // code omitted for brevity
```

Die `ExternalLoginCallback`-Methode funktioniert wie die lokale Identity Anmeldung. Die `AcrValues`-Eigenschaft wird auf den `mfa` Wert geprüft. Wenn der `mfa` Wert vorhanden ist, wird MFA erzwungen, bevor die Anmeldung abgeschlossen ist (z. b. an die `ErrorEnable2FA` Ansicht umgeleitet).

```csharp
//
// GET: /Account/ExternalLoginCallback
[HttpGet]
[AllowAnonymous]
public async Task<IActionResult> ExternalLoginCallback(
    string returnUrl = null,
    string remoteError = null)
{
    var context =
        await _interaction.GetAuthorizationContextAsync(returnUrl);
    var requires2Fa =
        context?.AcrValues.Count(t => t.Contains("mfa")) >= 1;

    if (remoteError != null)
    {
        ModelState.AddModelError(
            string.Empty,
            _sharedLocalizer["EXTERNAL_PROVIDER_ERROR", 
            remoteError]);
        return View(nameof(Login));
    }
    var info = await _signInManager.GetExternalLoginInfoAsync();

    if (info == null)
    {
        return RedirectToAction(nameof(Login));
    }

    var email = info.Principal.FindFirstValue(ClaimTypes.Email);

    if (!string.IsNullOrEmpty(email))
    {
        var user = await _userManager.FindByNameAsync(email);
        if (user != null && !user.TwoFactorEnabled && requires2Fa)
        {
            return RedirectToAction(nameof(ErrorEnable2FA));
        }
    }

    // Sign in the user with this external login provider if the user already has a login.
    var result = await _signInManager
        .ExternalLoginSignInAsync(
            info.LoginProvider, 
            info.ProviderKey, 
            isPersistent: 
            false);

    // code omitted for brevity
```

Wenn der Benutzer bereits angemeldet ist, wird die Client-App:

* Überprüft weiterhin den `amr` Anspruch.
* Die MFA kann mit einem Link zum ASP.net Core Identity Ansicht eingerichtet werden.

![acr_values-1](mfa/_static/acr_values-1.png)

## <a name="force-aspnet-core-openid-connect-client-to-require-mfa"></a>Erzwingen, dass ASP.net Core OpenID Connect-Client MFA erfordert

Dieses Beispiel zeigt, wie eine ASP.net Core Razor Page-APP, die OpenID Connect zum Anmelden verwendet, erfordern kann, dass sich Benutzer mithilfe von MFA authentifiziert haben.

Um die MFA-Anforderung zu überprüfen, wird eine `IAuthorizationRequirement` Anforderung erstellt. Diese wird den Seiten mithilfe einer Richtlinie hinzugefügt, die MFA erfordert.

```csharp
using Microsoft.AspNetCore.Authorization;
 
namespace AspNetCoreRequireMfaOidc
{
    public class RequireMfa : IAuthorizationRequirement{}
}
```

Eine `AuthorizationHandler` wird implementiert, die den `amr`-Anspruch verwendet und auf den Wert `mfa`prüft. Der `amr` wird in der `id_token` einer erfolgreichen Authentifizierung zurückgegeben und kann viele verschiedene Werte aufweisen, wie in der Spezifikation der [Referenzwerte der Authentifizierungsmethode](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) definiert.

Der zurückgegebene Wert hängt von der Authentifizierung der Identität und der Open ID Connect-Server Implementierung ab.

Der `AuthorizationHandler` verwendet die `RequireMfa` Anforderung und überprüft den `amr` Anspruch. Der OpenID Connect-Server kann mit IdentityServer4 mit ASP.net Core Identityimplementiert werden. Wenn ein Benutzer sich mit TOTP anmeldet, wird der `amr` Anspruch mit einem MFA-Wert zurückgegeben. Wenn eine andere OpenID Connect-Server Implementierung oder ein anderer MFA-Typ verwendet wird, kann der `amr` Anspruch einen anderen Wert aufweisen. Der Code muss erweitert werden, um dies ebenfalls zu akzeptieren.

```csharp
using Microsoft.AspNetCore.Authorization;
using System;
using System.Linq;
using System.Threading.Tasks;

namespace AspNetCoreRequireMfaOidc
{
    public class RequireMfaHandler : AuthorizationHandler<RequireMfa>
    {
        protected override Task HandleRequirementAsync(
            AuthorizationHandlerContext context, 
            RequireMfa requirement)
        {
            if (context == null)
                throw new ArgumentNullException(nameof(context));
            if (requirement == null)
                throw new ArgumentNullException(nameof(requirement));

            var amrClaim =
                context.User.Claims.FirstOrDefault(t => t.Type == "amr");

            if (amrClaim != null && amrClaim.Value == Amr.Mfa)
            {
                context.Succeed(requirement);
            }

            return Task.CompletedTask;
        }
    }
}
```

In der `Startup.ConfigureServices`-Methode wird die `AddOpenIdConnect`-Methode als Standard Aufforderungs Schema verwendet. Der Autorisierungs Handler, der zum Überprüfen des `amr` Anspruchs verwendet wird, wird der Inversion des Steuerelement Containers hinzugefügt. Anschließend wird eine Richtlinie erstellt, mit der die `RequireMfa` Anforderung hinzugefügt wird.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.ConfigureApplicationCookie(options =>
        options.Cookie.SecurePolicy =
            CookieSecurePolicy.Always);

    services.AddSingleton<IAuthorizationHandler, RequireMfaHandler>();

    services.AddAuthentication(options =>
    {
        options.DefaultScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme =
            OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.SignInScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.Authority = "https://localhost:44352";
        options.RequireHttpsMetadata = true;
        options.ClientId = "AspNetCoreRequireMfaOidc";
        options.ClientSecret = "AspNetCoreRequireMfaOidcSecret";
        options.ResponseType = "code id_token";
        options.Scope.Add("profile");
        options.Scope.Add("offline_access");
        options.SaveTokens = true;
    });

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireMfa", policyIsAdminRequirement =>
        {
            policyIsAdminRequirement.Requirements.Add(new RequireMfa());
        });
    });

    services.AddRazorPages();
}
```

Diese Richtlinie wird dann bei Bedarf auf der Razor-Seite verwendet. Die Richtlinie könnte auch global für die gesamte app hinzugefügt werden.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Microsoft.Extensions.Logging;

namespace AspNetCoreRequireMfaOidc.Pages
{
    [Authorize(Policy= "RequireMfa")]
    public class IndexModel : PageModel
    {
        private readonly ILogger<IndexModel> _logger;

        public IndexModel(ILogger<IndexModel> logger)
        {
            _logger = logger;
        }

        public void OnGet()
        {
        }
    }
}
```

Wenn der Benutzer ohne MFA authentifiziert wird, hat der `amr` Anspruch wahrscheinlich einen `pwd` Wert. Die Anforderung wird nicht autorisiert, auf die Seite zuzugreifen. Wenn Sie die Standardwerte verwenden, wird der Benutzer auf die Seite *Account/AccessDenied* umgeleitet. Dieses Verhalten kann geändert werden, oder Sie können hier eine eigene benutzerdefinierte Logik implementieren. In diesem Beispiel wird ein Link hinzugefügt, sodass der gültige Benutzer MFA für sein Konto einrichten kann.

```cshtml
@page
@model AspNetCoreRequireMfaOidc.AccessDeniedModel
@{
    ViewData["Title"] = "AccessDenied";
    Layout = "~/Pages/Shared/_Layout.cshtml";
}

<h1>AccessDenied</h1>

You require MFA to login here

<a href="https://localhost:44352/Manage/TwoFactorAuthentication">Enable MFA</a>
```

Jetzt können nur Benutzer, die sich mit MFA authentifizieren, auf die Seite oder Website zugreifen. Wenn verschiedene MFA-Typen verwendet werden oder wenn 2FA okay ist, weist der `amr` Anspruch unterschiedliche Werte auf und muss ordnungsgemäß verarbeitet werden. Verschiedene Open ID Connect-Server geben auch andere Werte für diesen Anspruch zurück und folgen möglicherweise nicht der Spezifikation der [Referenzwerte der Authentifizierungsmethode](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) .

Bei der Anmeldung ohne MFA (z. b. nur mit einem Kennwort):

* Der `amr` hat den `pwd` Wert:

    ![require_mfa_oidc_02. png](mfa/_static/require_mfa_oidc_02.png)

* Zugriff verweigert:

    ![require_mfa_oidc_03. png](mfa/_static/require_mfa_oidc_03.png)

Alternativ dazu können Sie sich mit OTP bei Identityanmelden:

![require_mfa_oidc_01. png](mfa/_static/require_mfa_oidc_01.png)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Aktivieren der QR-Code Generierung für TOTP Authenticator-apps in ASP.net Core](xref:security/authentication/identity-enable-qrcodes)
* [Nicht verarbeitlose Authentifizierungs Optionen für Azure Active Directory](/azure/active-directory/authentication/concept-authentication-passwordless)
* [FIDO2 .NET-Bibliothek für FIDO2/webauthn-Nachweis und-unter Verwendung von .net](https://github.com/abergs/fido2-net-lib)
* [Webauthn großartig](https://github.com/herrjemand/awesome-webauthn)

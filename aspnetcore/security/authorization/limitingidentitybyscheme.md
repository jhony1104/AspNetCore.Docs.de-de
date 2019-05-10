---
title: Autorisieren Sie mit einem bestimmten Schema in ASP.NET Core
author: rick-anderson
description: In diesem Artikel wird erläutert, wie Identität für ein bestimmtes Schema beschränkt, bei der Verwendung mehrerer Authentifizierungsmethoden.
ms.author: riande
ms.date: 10/22/2018
uid: security/authorization/limitingidentitybyscheme
ms.openlocfilehash: 778bb61f472ab2e76f85da5999d3c79238188f19
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897337"
---
# <a name="authorize-with-a-specific-scheme-in-aspnet-core"></a>Autorisieren Sie mit einem bestimmten Schema in ASP.NET Core

In einigen Szenarien, z. B. Single Page Applications (SPAs) ist es üblich, mehrere Authentifizierungsmethoden zu verwenden. Beispielsweise kann die app cookiebasierte Authentifizierung für die Anmeldung und JWT-Bearer-Authentifizierung für JavaScript-Anforderungen verwenden. In einigen Fällen möglicherweise die app mehrere Instanzen eines Handlers für die Authentifizierung. Z. B. wird zwei Cookie-Handler, von denen einer eine grundlegende Identität enthält, und eine erstellt, wenn ein Multi-Factor Authentication (MFA) ausgelöst wurde. MFA kann ausgelöst werden, da der Benutzer einen Vorgang angefordert, der zusätzliche Sicherheit erfordert.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Ein betroffenes Authentifizierungsschema heißt, wenn der Authentifizierungsdienst während der Authentifizierung konfiguriert ist. Zum Beispiel:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication()
        .AddCookie(options => {
            options.LoginPath = "/Account/Unauthorized/";
            options.AccessDeniedPath = "/Account/Forbidden/";
        })
        .AddJwtBearer(options => {
            options.Audience = "http://localhost:5001/";
            options.Authority = "http://localhost:5000/";
        });
```

Im vorangehenden Code wurden zwei authentifizierungshandler hinzugefügt: eine für Cookies und eine für trägertoken.

>[!NOTE]
>Die Angabe der Standard-Schema führt zu den `HttpContext.User` -Eigenschaft, die auf dieser Identität festgelegt wird. Wenn Sie dieses Verhalten nicht möchten, deaktivieren Sie es durch Aufrufen der parameterlose Form `AddAuthentication`.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Authentifizierungsschemas heißen, wenn Authentifizierung-Middleware, die während der Authentifizierung konfiguriert sind. Zum Beispiel:

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
{
    // Code omitted for brevity

    app.UseCookieAuthentication(new CookieAuthenticationOptions()
    {
        AuthenticationScheme = "Cookie",
        LoginPath = "/Account/Unauthorized/",
        AccessDeniedPath = "/Account/Forbidden/",
        AutomaticAuthenticate = false
    });
    
    app.UseJwtBearerAuthentication(new JwtBearerOptions()
    {
        AuthenticationScheme = "Bearer",
        AutomaticAuthenticate = false,
        Audience = "http://localhost:5001/",
        Authority = "http://localhost:5000/",
        RequireHttpsMetadata = false
    });
```

Im vorangehenden Code wurden zwei Authentifizierung-Middleware hinzugefügt: eine für Cookies und eine für trägertoken.

>[!NOTE]
>Die Angabe der Standard-Schema führt zu den `HttpContext.User` -Eigenschaft, die auf dieser Identität festgelegt wird. Wenn Sie dieses Verhalten nicht möchten, deaktivieren Sie es durch Festlegen der `AuthenticationOptions.AutomaticAuthenticate` Eigenschaft `false`.

---

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a>Wählen das Schema mit dem Authorize-Attribut

Die app zum Zeitpunkt der Autorisierung gibt an, dass der Handler, der verwendet werden. Wählen Sie den Handler für die app wird mit dem durch Übergeben einer durch Trennzeichen getrennte Liste der Authentifizierungsschemas autorisiert `[Authorize]`. Die `[Authorize]` Attribut gibt an, das Authentifizierungsschema oder Schemas verwenden, unabhängig davon, ob ein Standardwert konfiguriert ist. Zum Beispiel:

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
[Authorize(AuthenticationSchemes = AuthSchemes)]
public class MixedController : Controller
    // Requires the following imports:
    // using Microsoft.AspNetCore.Authentication.Cookies;
    // using Microsoft.AspNetCore.Authentication.JwtBearer;
    private const string AuthSchemes =
        CookieAuthenticationDefaults.AuthenticationScheme + "," +
        JwtBearerDefaults.AuthenticationScheme;
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
[Authorize(ActiveAuthenticationSchemes = AuthSchemes)]
public class MixedController : Controller
    // Requires the following imports:
    // using Microsoft.AspNetCore.Authentication.Cookies;
    // using Microsoft.AspNetCore.Authentication.JwtBearer;
    private const string AuthSchemes =
        CookieAuthenticationDefaults.AuthenticationScheme + "," +
        JwtBearerDefaults.AuthenticationScheme;
```

---

Im vorherigen Beispiel sowohl die Cookie-als auch das Bearer-Handler ausgeführt und haben die Möglichkeit, zu erstellen, und fügen eine Identität für den aktuellen Benutzer. Führt durch Angabe eines Schemas nur an, der entsprechende Handler.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
[Authorize(AuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
[Authorize(ActiveAuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

---

Im vorangehenden Code ausgeführt wird nur der Handler mit dem Schema "Bearer" aus. Cookiebasierte Identitäten werden ignoriert.

## <a name="selecting-the-scheme-with-policies"></a>Wählen das Schema mit Richtlinien

Wenn, geben Sie die gewünschten Schemas in möchten [Richtlinie](xref:security/authorization/policies), Sie können festlegen, die `AuthenticationSchemes` Auflistung bei Ihrer Richtlinie hinzufügen:

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("Over18", policy =>
    {
        policy.AuthenticationSchemes.Add(JwtBearerDefaults.AuthenticationScheme);
        policy.RequireAuthenticatedUser();
        policy.Requirements.Add(new MinimumAgeRequirement());
    });
});
```

Im vorherigen Beispiel wird die Richtlinie "Over18" nur ausgeführt, mit der Identität, die vom Handler "Bearer" erstellt werden. Verwenden Sie die Richtlinie durch Festlegen der `[Authorize]` des Attributs `Policy` Eigenschaft:

```csharp
[Authorize(Policy = "Over18")]
public class RegistrationController : Controller
```

::: moniker range=">= aspnetcore-2.0"

## <a name="use-multiple-authentication-schemes"></a>Verwenden Sie mehrere Authentifizierungsschemas

Einige apps müssen möglicherweise mehrere Arten von Authentifizierung zu unterstützen. Ihre app kann z. B. Benutzer aus Azure Active Directory und aus einer Benutzerdatenbank authentifizieren. Ein weiteres Beispiel ist eine app, die Benutzer aus Active Directory Federation Services und Azure Active Directory B2C authentifiziert. In diesem Fall sollte die app ein JWT-Bearer-Token von mehreren Ausstellern akzeptieren.

Fügen Sie alle Authentifizierungsschemas, die Sie übernehmen möchten. Im folgenden code wird beispielsweise `Startup.ConfigureServices` fügt zwei JWT-Bearer-Authentifizierungsschemas mit unterschiedliche Aussteller:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
        .AddJwtBearer(options =>
        {
            options.Audience = "https://localhost:5000/";
            options.Authority = "https://localhost:5000/identity/";
        })
        .AddJwtBearer("AzureAD", options =>
        {
            options.Audience = "https://localhost:5000/";
            options.Authority = "https://login.microsoftonline.com/eb971100-6f99-4bdc-8611-1bc8edd7f436/";
        });
}
```

> [!NOTE]
> Nur ein JWT-Bearer-Authentifizierung registriert ist, mit dem Standard-Authentifizierungsschema `JwtBearerDefaults.AuthenticationScheme`. Zusätzlicher Authentifizierung muss mit einem eindeutigen Authentifizierungsschema registriert werden.

Der nächste Schritt ist die Standardrichtlinie für die Autorisierung zum Akzeptieren von beide Authentifizierungsschemas zu aktualisieren. Zum Beispiel:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthorization(options =>
    {
        var defaultAuthorizationPolicyBuilder = new AuthorizationPolicyBuilder(
            JwtBearerDefaults.AuthenticationScheme,
            "AzureAD");
        defaultAuthorizationPolicyBuilder = 
            defaultAuthorizationPolicyBuilder.RequireAuthenticatedUser();
        options.DefaultPolicy = defaultAuthorizationPolicyBuilder.Build();
    });
}
```

Wie die Standardrichtlinie für die Autorisierung überschrieben wird, ist es möglich, verwenden die `[Authorize]` Attribut in Controllern. Der Controller akzeptiert Anforderungen klicken Sie dann mit der von der ersten oder zweiten Aussteller ausgestellte JWT.

::: moniker-end

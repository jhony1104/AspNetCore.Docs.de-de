---
title: Migrieren von Authentifizierungs- und Identitätseinstellungen nach ASP.NET Core 2.0
author: scottaddie
description: In diesem Artikel wird beschrieben, die am häufigsten verwendeten Schritte zum Migrieren von ASP.NET Core 1.x-Authentifizierung und Identifizierung zu ASP.NET Core 2.0.
ms.author: scaddie
ms.date: 12/18/2018
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: d11d41c82236436096660a24df81a3df4da0fb8e
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58265358"
---
# <a name="migrate-authentication-and-identity-to-aspnet-core-20"></a>Migrieren von Authentifizierungs- und Identitätseinstellungen nach ASP.NET Core 2.0

Durch [Scott Addie](https://github.com/scottaddie) und [Hao Kung](https://github.com/HaoK)

ASP.NET Core 2.0 wurde ein neues Modell für die Authentifizierung und [Identität](xref:security/authentication/identity) das vereinfacht die Konfiguration mithilfe von Diensten. ASP.NET Core 1.x-Anwendungen, die Authentifizierungs- oder Identitätsfunktionen verwenden können aktualisiert werden, um das neue Modell zu verwenden, wie unten beschrieben.

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a>Authentifizierungsmiddleware und Dienste

In 1.x-Projekten ist die Authentifizierung über Middleware konfiguriert. Eine Middleware-Methode wird für jede Authentifizierungsschema aufgerufen, die Sie unterstützen möchten.

Im folgenden 1.x-Beispiel konfiguriert die Facebook-Authentifizierung mit Identität in *"Startup.cs"*:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory)
{
    app.UseIdentity();
    app.UseFacebookAuthentication(new FacebookOptions {
        AppId = Configuration["auth:facebook:appid"],
        AppSecret = Configuration["auth:facebook:appsecret"]
    });
}
```

In 2.0-Projekten ist die Authentifizierung über Dienste konfiguriert. Jede Authentifizierungsschema registriert ist, der `ConfigureServices` -Methode der *"Startup.cs"*. Die `UseIdentity` Methode wird durch ersetzt `UseAuthentication`.

Im folgenden Beispiel für die 2.0 konfiguriert Facebook-Authentifizierung mit Identitäten in *"Startup.cs"*:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();

    // If you want to tweak Identity cookies, they're no longer part of IdentityOptions.
    services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory) {
    app.UseAuthentication();
}
```

Die `UseAuthentication` Methode fügt eine einzelne Komponente, die für die automatische Authentifizierung und die Behandlung von Anforderungen der Remoteauthentifizierung zuständig ist. Er ersetzt alle einzelnen middlewarekomponenten mit einer einzelnen, gemeinsamen middlewarekomponente.

Im folgenden finden Sie Anweisungen für jede wichtige Authentifizierungsschema 2.0.

### <a name="cookie-based-authentication"></a>Cookie-basierte Authentifizierung

Wählen Sie eine der folgenden beiden Optionen aus, und stellen Sie die erforderlichen Änderungen im *"Startup.cs"*:

1. Cookies verwenden, mit der Identität
    - Ersetzen Sie dies `UseIdentity` mit `UseAuthentication` in die `Configure` Methode:

        ```csharp
        app.UseAuthentication();
        ```

    - Rufen Sie die `AddIdentity` -Methode in der die `ConfigureServices` Methode, um die Cookie-Authentifizierungsdienste hinzuzufügen.
    - Rufen Sie optional die `ConfigureApplicationCookie` oder `ConfigureExternalCookie` -Methode in der die `ConfigureServices` Methode, um die Identity-Cookie-Einstellungen zu optimieren.

        ```csharp
        services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();

        services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
        ```

2. Cookies verwenden, ohne Identity
    - Ersetzen Sie die `UseCookieAuthentication` -Methodenaufruf in der `Configure` -Methode mit `UseAuthentication`:

        ```csharp
        app.UseAuthentication();
        ```

    - Rufen Sie die `AddAuthentication` und `AddCookie` Methoden in der `ConfigureServices` Methode:

        ```csharp
        // If you don't want the cookie to be automatically authenticated and assigned to HttpContext.User,
        // remove the CookieAuthenticationDefaults.AuthenticationScheme parameter passed to AddAuthentication.
        services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
                .AddCookie(options =>
                {
                    options.LoginPath = "/Account/LogIn";
                    options.LogoutPath = "/Account/LogOff";
                });
        ```

### <a name="jwt-bearer-authentication"></a>JWT-Bearer-Authentifizierung

Nehmen Sie folgende Änderungen in *"Startup.cs"*:
- Ersetzen Sie die `UseJwtBearerAuthentication` -Methodenaufruf in der `Configure` -Methode mit `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- Rufen Sie die `AddJwtBearer` -Methode in der die `ConfigureServices` Methode:

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    Dieser Codeausschnitt nicht Identität verwenden, damit das Standardschema soll, übergeben festgelegt werden `JwtBearerDefaults.AuthenticationScheme` auf die `AddAuthentication` Methode.

### <a name="openid-connect-oidc-authentication"></a>OpenID Connect (OIDC) Authentifizierung

Nehmen Sie folgende Änderungen in *"Startup.cs"*:

- Ersetzen Sie die `UseOpenIdConnectAuthentication` -Methodenaufruf in der `Configure` -Methode mit `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- Rufen Sie die `AddOpenIdConnect` -Methode in der die `ConfigureServices` Methode:

    ```csharp
    services.AddAuthentication(options =>
    {
        options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.Authority = Configuration["auth:oidc:authority"];
        options.ClientId = Configuration["auth:oidc:clientid"];
    });
    ```

### <a name="facebook-authentication"></a>Facebook-Authentifizierung

Nehmen Sie folgende Änderungen in *"Startup.cs"*:
- Ersetzen Sie die `UseFacebookAuthentication` -Methodenaufruf in der `Configure` -Methode mit `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- Rufen Sie die `AddFacebook` -Methode in der die `ConfigureServices` Methode:

    ```csharp
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a>Google-Authentifizierung

Nehmen Sie folgende Änderungen in *"Startup.cs"*:
- Ersetzen Sie die `UseGoogleAuthentication` -Methodenaufruf in der `Configure` -Methode mit `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- Rufen Sie die `AddGoogle` -Methode in der die `ConfigureServices` Methode:

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options =>
            {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });
    ```

### <a name="microsoft-account-authentication"></a>Microsoft-Account-Authentifizierung

Nehmen Sie folgende Änderungen in *"Startup.cs"*:
- Ersetzen Sie die `UseMicrosoftAccountAuthentication` -Methodenaufruf in der `Configure` -Methode mit `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- Rufen Sie die `AddMicrosoftAccount` -Methode in der die `ConfigureServices` Methode:

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options =>
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ```

### <a name="twitter-authentication"></a>Twitter-Authentifizierung

Nehmen Sie folgende Änderungen in *"Startup.cs"*:
- Ersetzen Sie die `UseTwitterAuthentication` -Methodenaufruf in der `Configure` -Methode mit `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- Rufen Sie die `AddTwitter` -Methode in der die `ConfigureServices` Methode:

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options =>
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a>Festlegen von Standardschemas für die Authentifizierung

In 1.x die `AutomaticAuthenticate` und `AutomaticChallenge` Eigenschaften der [authenticationoptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) Basisklasse wurden auf einem einzelnen Authentifizierungsschema festgelegt werden soll. Gab es keine gute Möglichkeit, dies durchzusetzen.

In 2.0 wurden diese beiden Eigenschaften als Eigenschaften für die einzelnen entfernt `AuthenticationOptions` Instanz. Sie können konfiguriert werden, der `AddAuthentication` Methodenaufruf innerhalb der `ConfigureServices` -Methode der *"Startup.cs"*:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme);
```

Im obigen Codeausschnitt ist das Standardschema festgelegt ist, um `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").

Verwenden Sie alternativ eine überladene Version der dem `AddAuthentication` Methode, um mehr als eine Eigenschaft festzulegen. Im folgenden Beispiel überladene Methode das Standardschema festgelegt ist, um `CookieAuthenticationDefaults.AuthenticationScheme`. Das Authentifizierungsschema kann auch angegeben werden, innerhalb des einzelnen `[Authorize]` Attribute oder Autorisierungsrichtlinien.

```csharp
services.AddAuthentication(options =>
{
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

Definieren Sie eine Standard-Schema in 2.0, wenn eine der folgenden Bedingungen zutrifft:
- Sie möchten die Benutzer automatisch angemeldet werden
- Sie verwenden die `[Authorize]` Attribut- oder Autorisierung Richtlinien ohne Angabe von Schemas

Eine Ausnahme von dieser Regel wird die `AddIdentity` Methode. Diese Methode fügt die Cookies für Sie und legt die Standard-authentifizieren und Abfrage von Schemas, die das Cookie für die Anwendung `IdentityConstants.ApplicationScheme`. Darüber hinaus wird die Standard-anmelden-Schema auf das externe Cookie `IdentityConstants.ExternalScheme`.

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a>Verwenden Sie "HttpContext" authentifizierungserweiterungen

Die `IAuthenticationManager` Schnittstelle ist der Haupteinstiegspunkt, in dem 1.x-Authentifizierungssystem her. Es wurde durch einen neuen Satz von ersetzt `HttpContext` Erweiterungsmethoden in den `Microsoft.AspNetCore.Authentication` Namespace.

Z. B. 1.x-Projekte Verweis ein `Authentication` Eigenschaft:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

Importieren Sie in 2.0-Projekten die `Microsoft.AspNetCore.Authentication` -Namespace, und löschen Sie die `Authentication` Eigenschaftenverweise:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a>Windows Authentication (HTTP.sys / IISIntegration)

Es gibt zwei Varianten der Windows-Authentifizierung:
1. Der Host lässt nur authentifizierte Benutzer
2. Der Host ermöglicht sowohl anonymen und authentifizierte Benutzer

Die erste Variante, die oben beschriebenen ist von den 2.0 Änderungen nicht betroffen.

Die zweite Variante, die oben beschriebenen wird durch die 2.0-Änderungen beeinflusst. Beispielsweise ermöglichen Sie möglicherweise werden es anonyme Benutzer in Ihrer app auf IIS oder [HTTP.sys](xref:fundamentals/servers/httpsys) layer jedoch autorisierende von Benutzern auf der Controllerebene. In diesem Szenario legen Sie das Standardschema auf `IISDefaults.AuthenticationScheme` in die `Startup.ConfigureServices` Methode:

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

Fehler beim Festlegen der Standard-Schema wird entsprechend verhindert, dass der autorisierungsanforderung sollen nicht funktionieren.

<a name="identity-cookie-options"></a>

## <a name="identitycookieoptions-instances"></a>IdentityCookieOptions-Instanzen

Ein Nebeneffekt der 2.0 Änderungen ist die Umstellung auf die Verwendung benannter Optionen anstelle von Instanzen der Cookie-Optionen. Die Möglichkeit, die die Identity-Cookie-Schemanamen anpassen wird entfernt.

Z. B. 1.x Projekte [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection) übergeben eine `IdentityCookieOptions` Parameter in *AccountController.cs*. Das Authentifizierungsschema für den externen Cookie wird von der bereitgestellten Instanz zugegriffen werden:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

Die oben genannten Konstruktorinjektion in 2.0-Projekten nicht erforderlich ist und die `_externalCookieScheme` Feld gelöscht werden kann:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

Die `IdentityConstants.ExternalScheme` Konstante kann direkt verwendet werden:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="navigation-properties"></a>

## <a name="add-identityuser-poco-navigation-properties"></a>Hinzufügen von "identityuser" POCO Navigationseigenschaften

Die Entity Framework (EF) Core Navigationseigenschaften des basistexts `IdentityUser` POCO (Plain Old CLR Object) wurde entfernt. Wenn Ihre 1.x-Projekt auf diese Eigenschaften verwendet, müssen Sie fügen sie manuell an die 2.0-Projekt hinzu:

```csharp
/// <summary>
/// Navigation property for the roles this user belongs to.
/// </summary>
public virtual ICollection<IdentityUserRole<int>> Roles { get; } = new List<IdentityUserRole<int>>();

/// <summary>
/// Navigation property for the claims this user possesses.
/// </summary>
public virtual ICollection<IdentityUserClaim<int>> Claims { get; } = new List<IdentityUserClaim<int>>();

/// <summary>
/// Navigation property for this users login accounts.
/// </summary>
public virtual ICollection<IdentityUserLogin<int>> Logins { get; } = new List<IdentityUserLogin<int>>();
```

Um doppelte Fremdschlüssel zu verhindern, wenn EF Core-Migrationen ausführen, fügen Sie den folgenden Ihre `IdentityDbContext` Klasse `OnModelCreating` -Methode (nach der `base.OnModelCreating();` aufrufen):

```csharp
protected override void OnModelCreating(ModelBuilder builder)
{
    base.OnModelCreating(builder);
    // Customize the ASP.NET Core Identity model and override the defaults if needed.
    // For example, you can rename the ASP.NET Core Identity table names and more.
    // Add your customizations after calling base.OnModelCreating(builder);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Claims)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Logins)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Roles)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);
}
```

<a name="synchronous-method-removal"></a>

## <a name="replace-getexternalauthenticationschemes"></a>Ersetzen Sie GetExternalAuthenticationSchemes

Die synchrone Methode `GetExternalAuthenticationSchemes` wurde durch eine asynchrone Version entfernt. 1.x-Projekten müssen Sie den folgenden Code in *ManageController.cs*:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

Diese Methode ist in *Login.cshtml* zu:

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?range=62,75-84)]

Verwenden Sie in 2.0-Projekten die `GetExternalAuthenticationSchemesAsync` Methode:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

In *Login.cshtml*, `AuthenticationScheme` Eigenschaft zugegriffen wird, der `foreach` Schleife wird nun `Name`:

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?range=62,75-84)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a>Ändern der ManageLoginsViewModel-Eigenschaft

Ein `ManageLoginsViewModel` Objekt wird in verwendet die `ManageLogins` Aktion *ManageController.cs*. In 1.x-Projekten, das Objekt des `OtherLogins` Eigenschaft Rückgabetyp ist `IList<AuthenticationDescription>`. Dieser Rückgabetyp ist erforderlich, einen Import `Microsoft.AspNetCore.Http.Authentication`:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

In 2.0-Projekten wird der Rückgabetyp nun `IList<AuthenticationScheme>`. Diese neuen Rückgabetyp ist erforderlich, und Ersetzen Sie dabei die `Microsoft.AspNetCore.Http.Authentication` importieren Sie mit einem `Microsoft.AspNetCore.Authentication` importieren.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Weitere Informationen und Diskussionen, finden Sie unter der [Diskussion Auth 2.0](https://github.com/aspnet/Security/issues/1338) Problem auf GitHub.

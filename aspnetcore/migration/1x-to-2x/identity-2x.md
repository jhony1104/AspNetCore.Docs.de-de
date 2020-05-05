---
title: Migrieren der Identity Authentifizierung und zu ASP.net Core 2,0
author: scottaddie
description: In diesem Artikel werden die häufigsten Schritte zum Migrieren ASP.net Core 1. x- Identity Authentifizierung und zu ASP.net Core 2,0 beschrieben.
ms.author: scaddie
ms.date: 06/21/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: e828446716d88d92aeb587874421a5751dcb6de0
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82769500"
---
# <a name="migrate-authentication-and-identity-to-aspnet-core-20"></a>Migrieren der Identity Authentifizierung und zu ASP.net Core 2,0

Von [Scott Adder](https://github.com/scottaddie) und [Hao Kung](https://github.com/HaoK)

ASP.net Core 2,0 verfügt über ein neues Modell für die [Identity](xref:security/authentication/identity) Authentifizierung und vereinfacht die Konfiguration mithilfe von Diensten. ASP.net Core 1. x-Anwendungen, die die Identity -Authentifizierung verwenden, oder können aktualisiert werden, um das neue Modell wie unten beschrieben zu verwenden.

## <a name="update-namespaces"></a>Namespaces aktualisieren

In 1. x wurden Klassen wie `IdentityRole` und `IdentityUser` im- `Microsoft.AspNetCore.Identity.EntityFrameworkCore` Namespace gefunden.

In 2,0 wurde der <xref:Microsoft.AspNetCore.Identity> Namespace zur neuen Startseite für einige dieser Klassen. Mit dem Standard Identity Code enthalten `ApplicationUser` die betroffenen Klassen und `Startup`. Passen Sie `using` die Anweisungen so an, dass die betroffenen Verweise aufgelöst werden.

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a>Authentifizierungs Middleware und-Dienste

In 1. x-Projekten wird die Authentifizierung über Middleware konfiguriert. Für jedes Authentifizierungsschema, das Sie unterstützen möchten, wird eine Middleware-Methode aufgerufen.

Im folgenden 1. x-Beispiel wird die Facebook- Identity Authentifizierung mit in *Startup.cs*konfiguriert:

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

In 2,0-Projekten wird die Authentifizierung über Dienste konfiguriert. Jedes Authentifizierungsschema ist in der `ConfigureServices` -Methode von *Startup.cs*registriert. Die `UseIdentity` -Methode wird durch `UseAuthentication`ersetzt.

Im 2,0 folgenden Beispiel wird die Facebook-Authentifizierung Identity mit in *Startup.cs*konfiguriert:

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

Die `UseAuthentication` -Methode fügt eine einzelne Authentifizierungs Middleware-Komponente hinzu, die für die automatische Authentifizierung und die Behandlung von Remote Authentifizierungsanforderungen zuständig ist. Es ersetzt alle einzelnen middlewarekomponenten durch eine einzelne, gängige Middlewarekomponente.

Im folgenden finden Sie 2,0 Migrations Anweisungen für jedes wichtige Authentifizierungsschema.

### <a name="cookie-based-authentication"></a>Cookiebasierte Authentifizierung

Wählen Sie eine der beiden folgenden Optionen aus, und nehmen Sie die erforderlichen Änderungen in *Startup.cs*vor:

1. Verwenden von Cookies mitIdentity
    - Ersetzen `UseIdentity` Sie `UseAuthentication` durch in `Configure` der-Methode:

        ```csharp
        app.UseAuthentication();
        ```

    - Rufen Sie `AddIdentity` die-Methode `ConfigureServices` in der-Methode auf, um die Cookie-Authentifizierungsdienste hinzuzufügen
    - Rufen Sie optional die `ConfigureApplicationCookie` - `ConfigureExternalCookie` oder-Methode `ConfigureServices` in der-Methode auf Identity , um die Cookie-Einstellungen zu optimieren.

        ```csharp
        services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();

        services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
        ```

2. Verwenden von Cookies ohneIdentity
    - Ersetzen Sie `UseCookieAuthentication` den Methodenaufrufe in `Configure` der- `UseAuthentication`Methode durch:

        ```csharp
        app.UseAuthentication();
        ```

    - Rufen Sie `AddAuthentication` die `AddCookie` Methoden und in `ConfigureServices` der-Methode auf:

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

### <a name="jwt-bearer-authentication"></a>JWT-Träger Authentifizierung

Nehmen Sie die folgenden Änderungen in *Startup.cs*vor:
- Ersetzen Sie `UseJwtBearerAuthentication` den Methodenaufrufe in `Configure` der- `UseAuthentication`Methode durch:

    ```csharp
    app.UseAuthentication();
    ```

- Rufen Sie `AddJwtBearer` die-Methode `ConfigureServices` in der-Methode auf:

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    Dieser Code Ausschnitt verwendet Identitynicht. Daher sollte das Standardschema durch Übergabe `JwtBearerDefaults.AuthenticationScheme` an die `AddAuthentication` -Methode festgelegt werden.

### <a name="openid-connect-oidc-authentication"></a>OpenID Connect-Authentifizierung (oidc)

Nehmen Sie die folgenden Änderungen in *Startup.cs*vor:

- Ersetzen Sie `UseOpenIdConnectAuthentication` den Methodenaufrufe in `Configure` der- `UseAuthentication`Methode durch:

    ```csharp
    app.UseAuthentication();
    ```

- Rufen Sie `AddOpenIdConnect` die-Methode `ConfigureServices` in der-Methode auf:

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

- Ersetzen Sie `PostLogoutRedirectUri` die-Eigenschaft `OpenIdConnectOptions` in der `SignedOutRedirectUri`Aktion durch:

    ```csharp
    .AddOpenIdConnect(options =>
    {
        options.SignedOutRedirectUri = "https://contoso.com";
    });
    ```
    
### <a name="facebook-authentication"></a>Facebook-Authentifizierung

Nehmen Sie die folgenden Änderungen in *Startup.cs*vor:
- Ersetzen Sie `UseFacebookAuthentication` den Methodenaufrufe in `Configure` der- `UseAuthentication`Methode durch:

    ```csharp
    app.UseAuthentication();
    ```

- Rufen Sie `AddFacebook` die-Methode `ConfigureServices` in der-Methode auf:

    ```csharp
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a>Google-Authentifizierung

Nehmen Sie die folgenden Änderungen in *Startup.cs*vor:
- Ersetzen Sie `UseGoogleAuthentication` den Methodenaufrufe in `Configure` der- `UseAuthentication`Methode durch:

    ```csharp
    app.UseAuthentication();
    ```

- Rufen Sie `AddGoogle` die-Methode `ConfigureServices` in der-Methode auf:

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options =>
            {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });
    ```

### <a name="microsoft-account-authentication"></a>Authentifizierung über Microsoft-Konto

Weitere Informationen zur Microsoft-Konto-Authentifizierung finden Sie in [diesem GitHub-Problem](https://github.com/dotnet/AspNetCore.Docs/issues/14455).

Nehmen Sie die folgenden Änderungen in *Startup.cs*vor:
- Ersetzen Sie `UseMicrosoftAccountAuthentication` den Methodenaufrufe in `Configure` der- `UseAuthentication`Methode durch:

    ```csharp
    app.UseAuthentication();
    ```

- Rufen Sie `AddMicrosoftAccount` die-Methode `ConfigureServices` in der-Methode auf:

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options =>
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ```

### <a name="twitter-authentication"></a>Twitter-Authentifizierung

Nehmen Sie die folgenden Änderungen in *Startup.cs*vor:
- Ersetzen Sie `UseTwitterAuthentication` den Methodenaufrufe in `Configure` der- `UseAuthentication`Methode durch:

    ```csharp
    app.UseAuthentication();
    ```

- Rufen Sie `AddTwitter` die-Methode `ConfigureServices` in der-Methode auf:

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options =>
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a>Festlegen von Standard Authentifizierungs Schemas

In 1. x wollten die `AutomaticAuthenticate` - `AutomaticChallenge` Eigenschaft und die-Eigenschaft der [authenticationoptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) -Basisklasse für ein einzelnes Authentifizierungsschema festgelegt werden. Es gab keinen guten Weg, dies zu erzwingen.

In 2,0 wurden diese beiden Eigenschaften als Eigenschaften für die jeweilige `AuthenticationOptions` Instanz entfernt. Sie können im `AddAuthentication` Methoden Befehl innerhalb der `ConfigureServices` -Methode von *Startup.cs*konfiguriert werden:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme);
```

Im vorherigen Code Ausschnitt ist das Standardschema auf `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies") festgelegt.

Verwenden Sie alternativ eine überladene Version `AddAuthentication` der-Methode, um mehr als eine Eigenschaft festzulegen. Im folgenden Beispiel für eine überladene Methode wird das Standardschema `CookieAuthenticationDefaults.AuthenticationScheme`auf festgelegt. Das Authentifizierungsschema kann alternativ in ihren einzelnen `[Authorize]` Attributen oder Autorisierungs Richtlinien angegeben werden.

```csharp
services.AddAuthentication(options =>
{
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

Definieren Sie ein Standardschema in 2,0, wenn eine der folgenden Bedingungen zutrifft:
- Sie möchten, dass der Benutzer automatisch angemeldet wird.
- Sie verwenden die `[Authorize]` Attribute oder Autorisierungs Richtlinien ohne Angabe von Schemas.

Eine Ausnahme von dieser Regel ist die `AddIdentity` -Methode. Mit dieser Methode werden Cookies für Sie hinzugefügt, und die standardmäßigen Authentifizierungs-und Abfrage Schemas werden auf das Anwendungs Cookie `IdentityConstants.ApplicationScheme`festgelegt. Außerdem wird das Standard Anmelde Schema auf das externe Cookie `IdentityConstants.ExternalScheme`festgelegt.

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a>Verwenden von HttpContext-Authentifizierungs Erweiterungen

Die `IAuthenticationManager` -Schnittstelle ist der Haupteinstiegspunkt in das 1. x-Authentifizierungssystem. Sie wurde durch einen neuen Satz von `HttpContext` Erweiterungs Methoden im- `Microsoft.AspNetCore.Authentication` Namespace ersetzt.

Beispielsweise verweisen 1. x-Projekte auf `Authentication` eine Eigenschaft:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

Importieren Sie in 2,0-Projekten `Microsoft.AspNetCore.Authentication` den-Namespace, und `Authentication` löschen Sie die Eigenschafts Verweise:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a>Windows-Authentifizierung (http. sys/IISIntegration)

Es gibt zwei Variationen der Windows-Authentifizierung:

* Der Host erlaubt nur authentifizierte Benutzer. Diese Variation wirkt sich nicht auf die 2,0-Änderungen aus.
* Der Host ermöglicht sowohl anonyme als auch authentifizierte Benutzer. Diese Variation ist von den 2,0-Änderungen betroffen. Beispielsweise sollte die APP anonyme Benutzer auf der [IIS](xref:host-and-deploy/iis/index) -oder [http. sys](xref:fundamentals/servers/httpsys) -Ebene zulassen, aber Benutzer auf Controller Ebene autorisieren. Legen Sie in diesem Szenario das Standardschema in der `Startup.ConfigureServices` -Methode fest.

  Legen Sie für [Microsoft. aspnetcore. Server. IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/)das Standardschema auf `IISDefaults.AuthenticationScheme`Folgendes fest:

  ```csharp
  using Microsoft.AspNetCore.Server.IISIntegration;

  services.AddAuthentication(IISDefaults.AuthenticationScheme);
  ```

  Legen Sie für [Microsoft. aspnetcore. Server. httpsys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)das Standardschema auf `HttpSysDefaults.AuthenticationScheme`Folgendes fest:

  ```csharp
  using Microsoft.AspNetCore.Server.HttpSys;

  services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
  ```

  Wenn Sie das Standardschema nicht festlegen, wird verhindert, dass die Autorisierungs Anforderung (Challenge) mit der folgenden Ausnahme funktioniert:

  > `System.InvalidOperationException`: Es wurde kein "authenticationScheme" angegeben, und es wurde kein defaultherausfordergescheme gefunden.

Weitere Informationen finden Sie unter <xref:security/authentication/windowsauth>.

<a name="identity-cookie-options"></a>

## <a name="identitycookieoptions-instances"></a>Identitycookieoptions-Instanzen

Ein Nebeneffekt der 2,0-Änderungen ist der Wechsel zur Verwendung von benannten Optionen anstelle von Cookie-Options Instanzen. Die Möglichkeit zum Anpassen der Identity Namen des Cookie-Schemas wird entfernt.

Beispielsweise verwenden 1. x-Projekte die [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection) , um `IdentityCookieOptions` einen Parameter an *AccountController.cs* und *ManageController.cs*zu übergeben. Der Zugriff auf das externe Cookie-Authentifizierungsschema erfolgt über die bereitgestellte-Instanz:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

Die oben genannte Konstruktorinjektion ist in 2,0-Projekten unnötig, `_externalCookieScheme` und das Feld kann gelöscht werden:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

1. x-Projekte haben `_externalCookieScheme` das Feld wie folgt verwendet:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

Ersetzen Sie in 2,0-Projekten den vorangehenden Code durch den folgenden Code. Die `IdentityConstants.ExternalScheme` Konstante kann direkt verwendet werden.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

Beheben Sie den neu `SignOutAsync` hinzugefügten-Befehl, indem Sie den folgenden Namespace importieren:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationImport)]

<a name="navigation-properties"></a>

## <a name="add-identityuser-poco-navigation-properties"></a>Identityuser poco-Navigations Eigenschaften hinzufügen

Die Entity Framework (EF)-Kern Navigations Eigenschaften des poco `IdentityUser` -Basis Objekts (Plain Old CLR Object) wurden entfernt. Wenn Ihr 1. x-Projekt diese Eigenschaften verwendet hat, fügen Sie Sie dem Projekt 2,0 manuell wieder hinzu:

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

Um beim Ausführen EF Core Migrationen doppelte Fremdschlüssel zu verhindern, fügen Sie der `IdentityDbContext` - `OnModelCreating` Methode (nach dem `base.OnModelCreating();` -Befehl) Folgendes hinzu:

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

## <a name="replace-getexternalauthenticationschemes"></a>Ersetzen von getexternalauthenticationschemas

Die synchrone `GetExternalAuthenticationSchemes` Methode wurde entfernt, um eine asynchrone Version zu bevorzugen. 1. x-Projekte verfügen über den folgenden Code in *Controllers/managecontroller. cs*:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

Diese Methode wird auch in *views/Account/Login. cshtml* angezeigt:

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemes&highlight=2)]

Verwenden Sie in 2,0-Projekten <xref:Microsoft.AspNetCore.Identity.SignInManager`1.GetExternalAuthenticationSchemesAsync*> die-Methode. Die Änderung in *ManageController.cs* ähnelt dem folgenden Code:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

In *Login. cshtml*wird die `AuthenticationScheme` Eigenschaft, auf `Name`die `foreach` in der Schleife zugegriffen wird, in geändert:

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemesAsync&highlight=2,19)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a>Änderung der manageloginsviewmodel-Eigenschaft

Ein `ManageLoginsViewModel` -Objekt wird in der `ManageLogins` *ManageController.cs*-Aktion verwendet. In 1. x-Projekten lautet `OtherLogins` `IList<AuthenticationDescription>`der Rückgabetyp des-Objekts. Dieser Rückgabetyp erfordert den `Microsoft.AspNetCore.Http.Authentication`Import von:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

In 2,0-Projekten wird der Rückgabetyp in `IList<AuthenticationScheme>`geändert. Dieser neue Rückgabetyp erfordert `Microsoft.AspNetCore.Http.Authentication` , dass der `Microsoft.AspNetCore.Authentication` Import durch einen Import ersetzt wird.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Weitere Informationen finden Sie in der [Erörterung des Themas auth 2,0](https://github.com/aspnet/Security/issues/1338) auf GitHub.

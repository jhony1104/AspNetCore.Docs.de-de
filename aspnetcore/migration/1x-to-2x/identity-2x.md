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
# <a name="migrate-authentication-and-identity-to-aspnet-core-20"></a><span data-ttu-id="041ce-103">Migrieren der Identity Authentifizierung und zu ASP.net Core 2,0</span><span class="sxs-lookup"><span data-stu-id="041ce-103">Migrate authentication and Identity to ASP.NET Core 2.0</span></span>

<span data-ttu-id="041ce-104">Von [Scott Adder](https://github.com/scottaddie) und [Hao Kung](https://github.com/HaoK)</span><span class="sxs-lookup"><span data-stu-id="041ce-104">By [Scott Addie](https://github.com/scottaddie) and [Hao Kung](https://github.com/HaoK)</span></span>

<span data-ttu-id="041ce-105">ASP.net Core 2,0 verfügt über ein neues Modell für die [Identity](xref:security/authentication/identity) Authentifizierung und vereinfacht die Konfiguration mithilfe von Diensten.</span><span class="sxs-lookup"><span data-stu-id="041ce-105">ASP.NET Core 2.0 has a new model for authentication and [Identity](xref:security/authentication/identity) that simplifies configuration by using services.</span></span> <span data-ttu-id="041ce-106">ASP.net Core 1. x-Anwendungen, die die Identity -Authentifizierung verwenden, oder können aktualisiert werden, um das neue Modell wie unten beschrieben zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="041ce-106">ASP.NET Core 1.x applications that use authentication or Identity can be updated to use the new model as outlined below.</span></span>

## <a name="update-namespaces"></a><span data-ttu-id="041ce-107">Namespaces aktualisieren</span><span class="sxs-lookup"><span data-stu-id="041ce-107">Update namespaces</span></span>

<span data-ttu-id="041ce-108">In 1. x wurden Klassen wie `IdentityRole` und `IdentityUser` im- `Microsoft.AspNetCore.Identity.EntityFrameworkCore` Namespace gefunden.</span><span class="sxs-lookup"><span data-stu-id="041ce-108">In 1.x, classes such `IdentityRole` and `IdentityUser` were found in the `Microsoft.AspNetCore.Identity.EntityFrameworkCore` namespace.</span></span>

<span data-ttu-id="041ce-109">In 2,0 wurde der <xref:Microsoft.AspNetCore.Identity> Namespace zur neuen Startseite für einige dieser Klassen.</span><span class="sxs-lookup"><span data-stu-id="041ce-109">In 2.0, the <xref:Microsoft.AspNetCore.Identity> namespace became the new home for several of such classes.</span></span> <span data-ttu-id="041ce-110">Mit dem Standard Identity Code enthalten `ApplicationUser` die betroffenen Klassen und `Startup`.</span><span class="sxs-lookup"><span data-stu-id="041ce-110">With the default Identity code, affected classes include `ApplicationUser` and `Startup`.</span></span> <span data-ttu-id="041ce-111">Passen Sie `using` die Anweisungen so an, dass die betroffenen Verweise aufgelöst werden.</span><span class="sxs-lookup"><span data-stu-id="041ce-111">Adjust your `using` statements to resolve the affected references.</span></span>

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a><span data-ttu-id="041ce-112">Authentifizierungs Middleware und-Dienste</span><span class="sxs-lookup"><span data-stu-id="041ce-112">Authentication Middleware and services</span></span>

<span data-ttu-id="041ce-113">In 1. x-Projekten wird die Authentifizierung über Middleware konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="041ce-113">In 1.x projects, authentication is configured via middleware.</span></span> <span data-ttu-id="041ce-114">Für jedes Authentifizierungsschema, das Sie unterstützen möchten, wird eine Middleware-Methode aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="041ce-114">A middleware method is invoked for each authentication scheme you want to support.</span></span>

<span data-ttu-id="041ce-115">Im folgenden 1. x-Beispiel wird die Facebook- Identity Authentifizierung mit in *Startup.cs*konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="041ce-115">The following 1.x example configures Facebook authentication with Identity in *Startup.cs*:</span></span>

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

<span data-ttu-id="041ce-116">In 2,0-Projekten wird die Authentifizierung über Dienste konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="041ce-116">In 2.0 projects, authentication is configured via services.</span></span> <span data-ttu-id="041ce-117">Jedes Authentifizierungsschema ist in der `ConfigureServices` -Methode von *Startup.cs*registriert.</span><span class="sxs-lookup"><span data-stu-id="041ce-117">Each authentication scheme is registered in the `ConfigureServices` method of *Startup.cs*.</span></span> <span data-ttu-id="041ce-118">Die `UseIdentity` -Methode wird durch `UseAuthentication`ersetzt.</span><span class="sxs-lookup"><span data-stu-id="041ce-118">The `UseIdentity` method is replaced with `UseAuthentication`.</span></span>

<span data-ttu-id="041ce-119">Im 2,0 folgenden Beispiel wird die Facebook-Authentifizierung Identity mit in *Startup.cs*konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="041ce-119">The following 2.0 example configures Facebook authentication with Identity in *Startup.cs*:</span></span>

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

<span data-ttu-id="041ce-120">Die `UseAuthentication` -Methode fügt eine einzelne Authentifizierungs Middleware-Komponente hinzu, die für die automatische Authentifizierung und die Behandlung von Remote Authentifizierungsanforderungen zuständig ist.</span><span class="sxs-lookup"><span data-stu-id="041ce-120">The `UseAuthentication` method adds a single authentication middleware component, which is responsible for automatic authentication and the handling of remote authentication requests.</span></span> <span data-ttu-id="041ce-121">Es ersetzt alle einzelnen middlewarekomponenten durch eine einzelne, gängige Middlewarekomponente.</span><span class="sxs-lookup"><span data-stu-id="041ce-121">It replaces all of the individual middleware components with a single, common middleware component.</span></span>

<span data-ttu-id="041ce-122">Im folgenden finden Sie 2,0 Migrations Anweisungen für jedes wichtige Authentifizierungsschema.</span><span class="sxs-lookup"><span data-stu-id="041ce-122">Below are 2.0 migration instructions for each major authentication scheme.</span></span>

### <a name="cookie-based-authentication"></a><span data-ttu-id="041ce-123">Cookiebasierte Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="041ce-123">Cookie-based authentication</span></span>

<span data-ttu-id="041ce-124">Wählen Sie eine der beiden folgenden Optionen aus, und nehmen Sie die erforderlichen Änderungen in *Startup.cs*vor:</span><span class="sxs-lookup"><span data-stu-id="041ce-124">Select one of the two options below, and make the necessary changes in *Startup.cs*:</span></span>

1. <span data-ttu-id="041ce-125">Verwenden von Cookies mitIdentity</span><span class="sxs-lookup"><span data-stu-id="041ce-125">Use cookies with Identity</span></span>
    - <span data-ttu-id="041ce-126">Ersetzen `UseIdentity` Sie `UseAuthentication` durch in `Configure` der-Methode:</span><span class="sxs-lookup"><span data-stu-id="041ce-126">Replace `UseIdentity` with `UseAuthentication` in the `Configure` method:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="041ce-127">Rufen Sie `AddIdentity` die-Methode `ConfigureServices` in der-Methode auf, um die Cookie-Authentifizierungsdienste hinzuzufügen</span><span class="sxs-lookup"><span data-stu-id="041ce-127">Invoke the `AddIdentity` method in the `ConfigureServices` method to add the cookie authentication services.</span></span>
    - <span data-ttu-id="041ce-128">Rufen Sie optional die `ConfigureApplicationCookie` - `ConfigureExternalCookie` oder-Methode `ConfigureServices` in der-Methode auf Identity , um die Cookie-Einstellungen zu optimieren.</span><span class="sxs-lookup"><span data-stu-id="041ce-128">Optionally, invoke the `ConfigureApplicationCookie` or `ConfigureExternalCookie` method in the `ConfigureServices` method to tweak the Identity cookie settings.</span></span>

        ```csharp
        services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();

        services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
        ```

2. <span data-ttu-id="041ce-129">Verwenden von Cookies ohneIdentity</span><span class="sxs-lookup"><span data-stu-id="041ce-129">Use cookies without Identity</span></span>
    - <span data-ttu-id="041ce-130">Ersetzen Sie `UseCookieAuthentication` den Methodenaufrufe in `Configure` der- `UseAuthentication`Methode durch:</span><span class="sxs-lookup"><span data-stu-id="041ce-130">Replace the `UseCookieAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="041ce-131">Rufen Sie `AddAuthentication` die `AddCookie` Methoden und in `ConfigureServices` der-Methode auf:</span><span class="sxs-lookup"><span data-stu-id="041ce-131">Invoke the `AddAuthentication` and `AddCookie` methods in the `ConfigureServices` method:</span></span>

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

### <a name="jwt-bearer-authentication"></a><span data-ttu-id="041ce-132">JWT-Träger Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="041ce-132">JWT Bearer Authentication</span></span>

<span data-ttu-id="041ce-133">Nehmen Sie die folgenden Änderungen in *Startup.cs*vor:</span><span class="sxs-lookup"><span data-stu-id="041ce-133">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="041ce-134">Ersetzen Sie `UseJwtBearerAuthentication` den Methodenaufrufe in `Configure` der- `UseAuthentication`Methode durch:</span><span class="sxs-lookup"><span data-stu-id="041ce-134">Replace the `UseJwtBearerAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="041ce-135">Rufen Sie `AddJwtBearer` die-Methode `ConfigureServices` in der-Methode auf:</span><span class="sxs-lookup"><span data-stu-id="041ce-135">Invoke the `AddJwtBearer` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    <span data-ttu-id="041ce-136">Dieser Code Ausschnitt verwendet Identitynicht. Daher sollte das Standardschema durch Übergabe `JwtBearerDefaults.AuthenticationScheme` an die `AddAuthentication` -Methode festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="041ce-136">This code snippet doesn't use Identity, so the default scheme should be set by passing `JwtBearerDefaults.AuthenticationScheme` to the `AddAuthentication` method.</span></span>

### <a name="openid-connect-oidc-authentication"></a><span data-ttu-id="041ce-137">OpenID Connect-Authentifizierung (oidc)</span><span class="sxs-lookup"><span data-stu-id="041ce-137">OpenID Connect (OIDC) authentication</span></span>

<span data-ttu-id="041ce-138">Nehmen Sie die folgenden Änderungen in *Startup.cs*vor:</span><span class="sxs-lookup"><span data-stu-id="041ce-138">Make the following changes in *Startup.cs*:</span></span>

- <span data-ttu-id="041ce-139">Ersetzen Sie `UseOpenIdConnectAuthentication` den Methodenaufrufe in `Configure` der- `UseAuthentication`Methode durch:</span><span class="sxs-lookup"><span data-stu-id="041ce-139">Replace the `UseOpenIdConnectAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="041ce-140">Rufen Sie `AddOpenIdConnect` die-Methode `ConfigureServices` in der-Methode auf:</span><span class="sxs-lookup"><span data-stu-id="041ce-140">Invoke the `AddOpenIdConnect` method in the `ConfigureServices` method:</span></span>

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

- <span data-ttu-id="041ce-141">Ersetzen Sie `PostLogoutRedirectUri` die-Eigenschaft `OpenIdConnectOptions` in der `SignedOutRedirectUri`Aktion durch:</span><span class="sxs-lookup"><span data-stu-id="041ce-141">Replace the `PostLogoutRedirectUri` property in the `OpenIdConnectOptions` action with `SignedOutRedirectUri`:</span></span>

    ```csharp
    .AddOpenIdConnect(options =>
    {
        options.SignedOutRedirectUri = "https://contoso.com";
    });
    ```
    
### <a name="facebook-authentication"></a><span data-ttu-id="041ce-142">Facebook-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="041ce-142">Facebook authentication</span></span>

<span data-ttu-id="041ce-143">Nehmen Sie die folgenden Änderungen in *Startup.cs*vor:</span><span class="sxs-lookup"><span data-stu-id="041ce-143">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="041ce-144">Ersetzen Sie `UseFacebookAuthentication` den Methodenaufrufe in `Configure` der- `UseAuthentication`Methode durch:</span><span class="sxs-lookup"><span data-stu-id="041ce-144">Replace the `UseFacebookAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="041ce-145">Rufen Sie `AddFacebook` die-Methode `ConfigureServices` in der-Methode auf:</span><span class="sxs-lookup"><span data-stu-id="041ce-145">Invoke the `AddFacebook` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a><span data-ttu-id="041ce-146">Google-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="041ce-146">Google authentication</span></span>

<span data-ttu-id="041ce-147">Nehmen Sie die folgenden Änderungen in *Startup.cs*vor:</span><span class="sxs-lookup"><span data-stu-id="041ce-147">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="041ce-148">Ersetzen Sie `UseGoogleAuthentication` den Methodenaufrufe in `Configure` der- `UseAuthentication`Methode durch:</span><span class="sxs-lookup"><span data-stu-id="041ce-148">Replace the `UseGoogleAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="041ce-149">Rufen Sie `AddGoogle` die-Methode `ConfigureServices` in der-Methode auf:</span><span class="sxs-lookup"><span data-stu-id="041ce-149">Invoke the `AddGoogle` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options =>
            {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });
    ```

### <a name="microsoft-account-authentication"></a><span data-ttu-id="041ce-150">Authentifizierung über Microsoft-Konto</span><span class="sxs-lookup"><span data-stu-id="041ce-150">Microsoft Account authentication</span></span>

<span data-ttu-id="041ce-151">Weitere Informationen zur Microsoft-Konto-Authentifizierung finden Sie in [diesem GitHub-Problem](https://github.com/dotnet/AspNetCore.Docs/issues/14455).</span><span class="sxs-lookup"><span data-stu-id="041ce-151">For more information on Microsoft account authentication, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/14455).</span></span>

<span data-ttu-id="041ce-152">Nehmen Sie die folgenden Änderungen in *Startup.cs*vor:</span><span class="sxs-lookup"><span data-stu-id="041ce-152">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="041ce-153">Ersetzen Sie `UseMicrosoftAccountAuthentication` den Methodenaufrufe in `Configure` der- `UseAuthentication`Methode durch:</span><span class="sxs-lookup"><span data-stu-id="041ce-153">Replace the `UseMicrosoftAccountAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="041ce-154">Rufen Sie `AddMicrosoftAccount` die-Methode `ConfigureServices` in der-Methode auf:</span><span class="sxs-lookup"><span data-stu-id="041ce-154">Invoke the `AddMicrosoftAccount` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options =>
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ```

### <a name="twitter-authentication"></a><span data-ttu-id="041ce-155">Twitter-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="041ce-155">Twitter authentication</span></span>

<span data-ttu-id="041ce-156">Nehmen Sie die folgenden Änderungen in *Startup.cs*vor:</span><span class="sxs-lookup"><span data-stu-id="041ce-156">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="041ce-157">Ersetzen Sie `UseTwitterAuthentication` den Methodenaufrufe in `Configure` der- `UseAuthentication`Methode durch:</span><span class="sxs-lookup"><span data-stu-id="041ce-157">Replace the `UseTwitterAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="041ce-158">Rufen Sie `AddTwitter` die-Methode `ConfigureServices` in der-Methode auf:</span><span class="sxs-lookup"><span data-stu-id="041ce-158">Invoke the `AddTwitter` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options =>
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a><span data-ttu-id="041ce-159">Festlegen von Standard Authentifizierungs Schemas</span><span class="sxs-lookup"><span data-stu-id="041ce-159">Setting default authentication schemes</span></span>

<span data-ttu-id="041ce-160">In 1. x wollten die `AutomaticAuthenticate` - `AutomaticChallenge` Eigenschaft und die-Eigenschaft der [authenticationoptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) -Basisklasse für ein einzelnes Authentifizierungsschema festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="041ce-160">In 1.x, the `AutomaticAuthenticate` and `AutomaticChallenge` properties of the [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) base class were intended to be set on a single authentication scheme.</span></span> <span data-ttu-id="041ce-161">Es gab keinen guten Weg, dies zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="041ce-161">There was no good way to enforce this.</span></span>

<span data-ttu-id="041ce-162">In 2,0 wurden diese beiden Eigenschaften als Eigenschaften für die jeweilige `AuthenticationOptions` Instanz entfernt.</span><span class="sxs-lookup"><span data-stu-id="041ce-162">In 2.0, these two properties have been removed as properties on the individual `AuthenticationOptions` instance.</span></span> <span data-ttu-id="041ce-163">Sie können im `AddAuthentication` Methoden Befehl innerhalb der `ConfigureServices` -Methode von *Startup.cs*konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="041ce-163">They can be configured in the `AddAuthentication` method call within the `ConfigureServices` method of *Startup.cs*:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme);
```

<span data-ttu-id="041ce-164">Im vorherigen Code Ausschnitt ist das Standardschema auf `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies") festgelegt.</span><span class="sxs-lookup"><span data-stu-id="041ce-164">In the preceding code snippet, the default scheme is set to `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="041ce-165">Verwenden Sie alternativ eine überladene Version `AddAuthentication` der-Methode, um mehr als eine Eigenschaft festzulegen.</span><span class="sxs-lookup"><span data-stu-id="041ce-165">Alternatively, use an overloaded version of the `AddAuthentication` method to set more than one property.</span></span> <span data-ttu-id="041ce-166">Im folgenden Beispiel für eine überladene Methode wird das Standardschema `CookieAuthenticationDefaults.AuthenticationScheme`auf festgelegt.</span><span class="sxs-lookup"><span data-stu-id="041ce-166">In the following overloaded method example, the default scheme is set to `CookieAuthenticationDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="041ce-167">Das Authentifizierungsschema kann alternativ in ihren einzelnen `[Authorize]` Attributen oder Autorisierungs Richtlinien angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="041ce-167">The authentication scheme may alternatively be specified within your individual `[Authorize]` attributes or authorization policies.</span></span>

```csharp
services.AddAuthentication(options =>
{
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

<span data-ttu-id="041ce-168">Definieren Sie ein Standardschema in 2,0, wenn eine der folgenden Bedingungen zutrifft:</span><span class="sxs-lookup"><span data-stu-id="041ce-168">Define a default scheme in 2.0 if one of the following conditions is true:</span></span>
- <span data-ttu-id="041ce-169">Sie möchten, dass der Benutzer automatisch angemeldet wird.</span><span class="sxs-lookup"><span data-stu-id="041ce-169">You want the user to be automatically signed in</span></span>
- <span data-ttu-id="041ce-170">Sie verwenden die `[Authorize]` Attribute oder Autorisierungs Richtlinien ohne Angabe von Schemas.</span><span class="sxs-lookup"><span data-stu-id="041ce-170">You use the `[Authorize]` attribute or authorization policies without specifying schemes</span></span>

<span data-ttu-id="041ce-171">Eine Ausnahme von dieser Regel ist die `AddIdentity` -Methode.</span><span class="sxs-lookup"><span data-stu-id="041ce-171">An exception to this rule is the `AddIdentity` method.</span></span> <span data-ttu-id="041ce-172">Mit dieser Methode werden Cookies für Sie hinzugefügt, und die standardmäßigen Authentifizierungs-und Abfrage Schemas werden auf das Anwendungs Cookie `IdentityConstants.ApplicationScheme`festgelegt.</span><span class="sxs-lookup"><span data-stu-id="041ce-172">This method adds cookies for you and sets the default authenticate and challenge schemes to the application cookie `IdentityConstants.ApplicationScheme`.</span></span> <span data-ttu-id="041ce-173">Außerdem wird das Standard Anmelde Schema auf das externe Cookie `IdentityConstants.ExternalScheme`festgelegt.</span><span class="sxs-lookup"><span data-stu-id="041ce-173">Additionally, it sets the default sign-in scheme to the external cookie `IdentityConstants.ExternalScheme`.</span></span>

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a><span data-ttu-id="041ce-174">Verwenden von HttpContext-Authentifizierungs Erweiterungen</span><span class="sxs-lookup"><span data-stu-id="041ce-174">Use HttpContext authentication extensions</span></span>

<span data-ttu-id="041ce-175">Die `IAuthenticationManager` -Schnittstelle ist der Haupteinstiegspunkt in das 1. x-Authentifizierungssystem.</span><span class="sxs-lookup"><span data-stu-id="041ce-175">The `IAuthenticationManager` interface is the main entry point into the 1.x authentication system.</span></span> <span data-ttu-id="041ce-176">Sie wurde durch einen neuen Satz von `HttpContext` Erweiterungs Methoden im- `Microsoft.AspNetCore.Authentication` Namespace ersetzt.</span><span class="sxs-lookup"><span data-stu-id="041ce-176">It has been replaced with a new set of `HttpContext` extension methods in the `Microsoft.AspNetCore.Authentication` namespace.</span></span>

<span data-ttu-id="041ce-177">Beispielsweise verweisen 1. x-Projekte auf `Authentication` eine Eigenschaft:</span><span class="sxs-lookup"><span data-stu-id="041ce-177">For example, 1.x projects reference an `Authentication` property:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="041ce-178">Importieren Sie in 2,0-Projekten `Microsoft.AspNetCore.Authentication` den-Namespace, und `Authentication` löschen Sie die Eigenschafts Verweise:</span><span class="sxs-lookup"><span data-stu-id="041ce-178">In 2.0 projects, import the `Microsoft.AspNetCore.Authentication` namespace, and delete the `Authentication` property references:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a><span data-ttu-id="041ce-179">Windows-Authentifizierung (http. sys/IISIntegration)</span><span class="sxs-lookup"><span data-stu-id="041ce-179">Windows Authentication (HTTP.sys / IISIntegration)</span></span>

<span data-ttu-id="041ce-180">Es gibt zwei Variationen der Windows-Authentifizierung:</span><span class="sxs-lookup"><span data-stu-id="041ce-180">There are two variations of Windows authentication:</span></span>

* <span data-ttu-id="041ce-181">Der Host erlaubt nur authentifizierte Benutzer.</span><span class="sxs-lookup"><span data-stu-id="041ce-181">The host only allows authenticated users.</span></span> <span data-ttu-id="041ce-182">Diese Variation wirkt sich nicht auf die 2,0-Änderungen aus.</span><span class="sxs-lookup"><span data-stu-id="041ce-182">This variation isn't affected by the 2.0 changes.</span></span>
* <span data-ttu-id="041ce-183">Der Host ermöglicht sowohl anonyme als auch authentifizierte Benutzer.</span><span class="sxs-lookup"><span data-stu-id="041ce-183">The host allows both anonymous and authenticated users.</span></span> <span data-ttu-id="041ce-184">Diese Variation ist von den 2,0-Änderungen betroffen.</span><span class="sxs-lookup"><span data-stu-id="041ce-184">This variation is affected by the 2.0 changes.</span></span> <span data-ttu-id="041ce-185">Beispielsweise sollte die APP anonyme Benutzer auf der [IIS](xref:host-and-deploy/iis/index) -oder [http. sys](xref:fundamentals/servers/httpsys) -Ebene zulassen, aber Benutzer auf Controller Ebene autorisieren.</span><span class="sxs-lookup"><span data-stu-id="041ce-185">For example, the app should allow anonymous users at the [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys) layer but authorize users at the controller level.</span></span> <span data-ttu-id="041ce-186">Legen Sie in diesem Szenario das Standardschema in der `Startup.ConfigureServices` -Methode fest.</span><span class="sxs-lookup"><span data-stu-id="041ce-186">In this scenario, set the default scheme in the `Startup.ConfigureServices` method.</span></span>

  <span data-ttu-id="041ce-187">Legen Sie für [Microsoft. aspnetcore. Server. IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/)das Standardschema auf `IISDefaults.AuthenticationScheme`Folgendes fest:</span><span class="sxs-lookup"><span data-stu-id="041ce-187">For [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/), set the default scheme to `IISDefaults.AuthenticationScheme`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Server.IISIntegration;

  services.AddAuthentication(IISDefaults.AuthenticationScheme);
  ```

  <span data-ttu-id="041ce-188">Legen Sie für [Microsoft. aspnetcore. Server. httpsys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)das Standardschema auf `HttpSysDefaults.AuthenticationScheme`Folgendes fest:</span><span class="sxs-lookup"><span data-stu-id="041ce-188">For [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/), set the default scheme to `HttpSysDefaults.AuthenticationScheme`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Server.HttpSys;

  services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
  ```

  <span data-ttu-id="041ce-189">Wenn Sie das Standardschema nicht festlegen, wird verhindert, dass die Autorisierungs Anforderung (Challenge) mit der folgenden Ausnahme funktioniert:</span><span class="sxs-lookup"><span data-stu-id="041ce-189">Failure to set the default scheme prevents the authorize (challenge) request from working with the following exception:</span></span>

  > <span data-ttu-id="041ce-190">`System.InvalidOperationException`: Es wurde kein "authenticationScheme" angegeben, und es wurde kein defaultherausfordergescheme gefunden.</span><span class="sxs-lookup"><span data-stu-id="041ce-190">`System.InvalidOperationException`: No authenticationScheme was specified, and there was no DefaultChallengeScheme found.</span></span>

<span data-ttu-id="041ce-191">Weitere Informationen finden Sie unter <xref:security/authentication/windowsauth>.</span><span class="sxs-lookup"><span data-stu-id="041ce-191">For more information, see <xref:security/authentication/windowsauth>.</span></span>

<a name="identity-cookie-options"></a>

## <a name="identitycookieoptions-instances"></a><span data-ttu-id="041ce-192">Identitycookieoptions-Instanzen</span><span class="sxs-lookup"><span data-stu-id="041ce-192">IdentityCookieOptions instances</span></span>

<span data-ttu-id="041ce-193">Ein Nebeneffekt der 2,0-Änderungen ist der Wechsel zur Verwendung von benannten Optionen anstelle von Cookie-Options Instanzen.</span><span class="sxs-lookup"><span data-stu-id="041ce-193">A side effect of the 2.0 changes is the switch to using named options instead of cookie options instances.</span></span> <span data-ttu-id="041ce-194">Die Möglichkeit zum Anpassen der Identity Namen des Cookie-Schemas wird entfernt.</span><span class="sxs-lookup"><span data-stu-id="041ce-194">The ability to customize the Identity cookie scheme names is removed.</span></span>

<span data-ttu-id="041ce-195">Beispielsweise verwenden 1. x-Projekte die [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection) , um `IdentityCookieOptions` einen Parameter an *AccountController.cs* und *ManageController.cs*zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="041ce-195">For example, 1.x projects use [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) to pass an `IdentityCookieOptions` parameter into *AccountController.cs* and *ManageController.cs*.</span></span> <span data-ttu-id="041ce-196">Der Zugriff auf das externe Cookie-Authentifizierungsschema erfolgt über die bereitgestellte-Instanz:</span><span class="sxs-lookup"><span data-stu-id="041ce-196">The external cookie authentication scheme is accessed from the provided instance:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

<span data-ttu-id="041ce-197">Die oben genannte Konstruktorinjektion ist in 2,0-Projekten unnötig, `_externalCookieScheme` und das Feld kann gelöscht werden:</span><span class="sxs-lookup"><span data-stu-id="041ce-197">The aforementioned constructor injection becomes unnecessary in 2.0 projects, and the `_externalCookieScheme` field can be deleted:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

<span data-ttu-id="041ce-198">1. x-Projekte haben `_externalCookieScheme` das Feld wie folgt verwendet:</span><span class="sxs-lookup"><span data-stu-id="041ce-198">1.x projects used the `_externalCookieScheme` field as follows:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="041ce-199">Ersetzen Sie in 2,0-Projekten den vorangehenden Code durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="041ce-199">In 2.0 projects, replace the preceding code with the following.</span></span> <span data-ttu-id="041ce-200">Die `IdentityConstants.ExternalScheme` Konstante kann direkt verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="041ce-200">The `IdentityConstants.ExternalScheme` constant can be used directly.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="041ce-201">Beheben Sie den neu `SignOutAsync` hinzugefügten-Befehl, indem Sie den folgenden Namespace importieren:</span><span class="sxs-lookup"><span data-stu-id="041ce-201">Resolve the newly added `SignOutAsync` call by importing the following namespace:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationImport)]

<a name="navigation-properties"></a>

## <a name="add-identityuser-poco-navigation-properties"></a><span data-ttu-id="041ce-202">Identityuser poco-Navigations Eigenschaften hinzufügen</span><span class="sxs-lookup"><span data-stu-id="041ce-202">Add IdentityUser POCO navigation properties</span></span>

<span data-ttu-id="041ce-203">Die Entity Framework (EF)-Kern Navigations Eigenschaften des poco `IdentityUser` -Basis Objekts (Plain Old CLR Object) wurden entfernt.</span><span class="sxs-lookup"><span data-stu-id="041ce-203">The Entity Framework (EF) Core navigation properties of the base `IdentityUser` POCO (Plain Old CLR Object) have been removed.</span></span> <span data-ttu-id="041ce-204">Wenn Ihr 1. x-Projekt diese Eigenschaften verwendet hat, fügen Sie Sie dem Projekt 2,0 manuell wieder hinzu:</span><span class="sxs-lookup"><span data-stu-id="041ce-204">If your 1.x project used these properties, manually add them back to the 2.0 project:</span></span>

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

<span data-ttu-id="041ce-205">Um beim Ausführen EF Core Migrationen doppelte Fremdschlüssel zu verhindern, fügen Sie der `IdentityDbContext` - `OnModelCreating` Methode (nach dem `base.OnModelCreating();` -Befehl) Folgendes hinzu:</span><span class="sxs-lookup"><span data-stu-id="041ce-205">To prevent duplicate foreign keys when running EF Core Migrations, add the following to your `IdentityDbContext` class' `OnModelCreating` method (after the `base.OnModelCreating();` call):</span></span>

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

## <a name="replace-getexternalauthenticationschemes"></a><span data-ttu-id="041ce-206">Ersetzen von getexternalauthenticationschemas</span><span class="sxs-lookup"><span data-stu-id="041ce-206">Replace GetExternalAuthenticationSchemes</span></span>

<span data-ttu-id="041ce-207">Die synchrone `GetExternalAuthenticationSchemes` Methode wurde entfernt, um eine asynchrone Version zu bevorzugen.</span><span class="sxs-lookup"><span data-stu-id="041ce-207">The synchronous method `GetExternalAuthenticationSchemes` was removed in favor of an asynchronous version.</span></span> <span data-ttu-id="041ce-208">1. x-Projekte verfügen über den folgenden Code in *Controllers/managecontroller. cs*:</span><span class="sxs-lookup"><span data-stu-id="041ce-208">1.x projects have the following code in *Controllers/ManageController.cs*:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

<span data-ttu-id="041ce-209">Diese Methode wird auch in *views/Account/Login. cshtml* angezeigt:</span><span class="sxs-lookup"><span data-stu-id="041ce-209">This method appears in *Views/Account/Login.cshtml* too:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemes&highlight=2)]

<span data-ttu-id="041ce-210">Verwenden Sie in 2,0-Projekten <xref:Microsoft.AspNetCore.Identity.SignInManager`1.GetExternalAuthenticationSchemesAsync*> die-Methode.</span><span class="sxs-lookup"><span data-stu-id="041ce-210">In 2.0 projects, use the <xref:Microsoft.AspNetCore.Identity.SignInManager`1.GetExternalAuthenticationSchemesAsync*> method.</span></span> <span data-ttu-id="041ce-211">Die Änderung in *ManageController.cs* ähnelt dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="041ce-211">The change in *ManageController.cs* resembles the following code:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

<span data-ttu-id="041ce-212">In *Login. cshtml*wird die `AuthenticationScheme` Eigenschaft, auf `Name`die `foreach` in der Schleife zugegriffen wird, in geändert:</span><span class="sxs-lookup"><span data-stu-id="041ce-212">In *Login.cshtml*, the `AuthenticationScheme` property accessed in the `foreach` loop changes to `Name`:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemesAsync&highlight=2,19)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a><span data-ttu-id="041ce-213">Änderung der manageloginsviewmodel-Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="041ce-213">ManageLoginsViewModel property change</span></span>

<span data-ttu-id="041ce-214">Ein `ManageLoginsViewModel` -Objekt wird in der `ManageLogins` *ManageController.cs*-Aktion verwendet.</span><span class="sxs-lookup"><span data-stu-id="041ce-214">A `ManageLoginsViewModel` object is used in the `ManageLogins` action of *ManageController.cs*.</span></span> <span data-ttu-id="041ce-215">In 1. x-Projekten lautet `OtherLogins` `IList<AuthenticationDescription>`der Rückgabetyp des-Objekts.</span><span class="sxs-lookup"><span data-stu-id="041ce-215">In 1.x projects, the object's `OtherLogins` property return type is `IList<AuthenticationDescription>`.</span></span> <span data-ttu-id="041ce-216">Dieser Rückgabetyp erfordert den `Microsoft.AspNetCore.Http.Authentication`Import von:</span><span class="sxs-lookup"><span data-stu-id="041ce-216">This return type requires an import of `Microsoft.AspNetCore.Http.Authentication`:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<span data-ttu-id="041ce-217">In 2,0-Projekten wird der Rückgabetyp in `IList<AuthenticationScheme>`geändert.</span><span class="sxs-lookup"><span data-stu-id="041ce-217">In 2.0 projects, the return type changes to `IList<AuthenticationScheme>`.</span></span> <span data-ttu-id="041ce-218">Dieser neue Rückgabetyp erfordert `Microsoft.AspNetCore.Http.Authentication` , dass der `Microsoft.AspNetCore.Authentication` Import durch einen Import ersetzt wird.</span><span class="sxs-lookup"><span data-stu-id="041ce-218">This new return type requires replacing the `Microsoft.AspNetCore.Http.Authentication` import with a `Microsoft.AspNetCore.Authentication` import.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a><span data-ttu-id="041ce-219">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="041ce-219">Additional resources</span></span>

<span data-ttu-id="041ce-220">Weitere Informationen finden Sie in der [Erörterung des Themas auth 2,0](https://github.com/aspnet/Security/issues/1338) auf GitHub.</span><span class="sxs-lookup"><span data-stu-id="041ce-220">For more information, see the [Discussion for Auth 2.0](https://github.com/aspnet/Security/issues/1338) issue on GitHub.</span></span>

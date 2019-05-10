---
title: Migrieren von Authentifizierungs- und Identitätseinstellungen nach ASP.NET Core 2.0
author: scottaddie
description: In diesem Artikel wird beschrieben, die am häufigsten verwendeten Schritte zum Migrieren von ASP.NET Core 1.x-Authentifizierung und Identifizierung zu ASP.NET Core 2.0.
ms.author: scaddie
ms.date: 12/18/2018
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: d11d41c82236436096660a24df81a3df4da0fb8e
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892207"
---
# <a name="migrate-authentication-and-identity-to-aspnet-core-20"></a><span data-ttu-id="43a16-103">Migrieren von Authentifizierungs- und Identitätseinstellungen nach ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="43a16-103">Migrate authentication and Identity to ASP.NET Core 2.0</span></span>

<span data-ttu-id="43a16-104">Durch [Scott Addie](https://github.com/scottaddie) und [Hao Kung](https://github.com/HaoK)</span><span class="sxs-lookup"><span data-stu-id="43a16-104">By [Scott Addie](https://github.com/scottaddie) and [Hao Kung](https://github.com/HaoK)</span></span>

<span data-ttu-id="43a16-105">ASP.NET Core 2.0 wurde ein neues Modell für die Authentifizierung und [Identität](xref:security/authentication/identity) das vereinfacht die Konfiguration mithilfe von Diensten.</span><span class="sxs-lookup"><span data-stu-id="43a16-105">ASP.NET Core 2.0 has a new model for authentication and [Identity](xref:security/authentication/identity) which simplifies configuration by using services.</span></span> <span data-ttu-id="43a16-106">ASP.NET Core 1.x-Anwendungen, die Authentifizierungs- oder Identitätsfunktionen verwenden können aktualisiert werden, um das neue Modell zu verwenden, wie unten beschrieben.</span><span class="sxs-lookup"><span data-stu-id="43a16-106">ASP.NET Core 1.x applications that use authentication or Identity can be updated to use the new model as outlined below.</span></span>

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a><span data-ttu-id="43a16-107">Authentifizierungsmiddleware und Dienste</span><span class="sxs-lookup"><span data-stu-id="43a16-107">Authentication Middleware and services</span></span>

<span data-ttu-id="43a16-108">In 1.x-Projekten ist die Authentifizierung über Middleware konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="43a16-108">In 1.x projects, authentication is configured via middleware.</span></span> <span data-ttu-id="43a16-109">Eine Middleware-Methode wird für jede Authentifizierungsschema aufgerufen, die Sie unterstützen möchten.</span><span class="sxs-lookup"><span data-stu-id="43a16-109">A middleware method is invoked for each authentication scheme you want to support.</span></span>

<span data-ttu-id="43a16-110">Im folgenden 1.x-Beispiel konfiguriert die Facebook-Authentifizierung mit Identität in *"Startup.cs"*:</span><span class="sxs-lookup"><span data-stu-id="43a16-110">The following 1.x example configures Facebook authentication with Identity in *Startup.cs*:</span></span>

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

<span data-ttu-id="43a16-111">In 2.0-Projekten ist die Authentifizierung über Dienste konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="43a16-111">In 2.0 projects, authentication is configured via services.</span></span> <span data-ttu-id="43a16-112">Jede Authentifizierungsschema registriert ist, der `ConfigureServices` -Methode der *"Startup.cs"*.</span><span class="sxs-lookup"><span data-stu-id="43a16-112">Each authentication scheme is registered in the `ConfigureServices` method of *Startup.cs*.</span></span> <span data-ttu-id="43a16-113">Die `UseIdentity` Methode wird durch ersetzt `UseAuthentication`.</span><span class="sxs-lookup"><span data-stu-id="43a16-113">The `UseIdentity` method is replaced with `UseAuthentication`.</span></span>

<span data-ttu-id="43a16-114">Im folgenden Beispiel für die 2.0 konfiguriert Facebook-Authentifizierung mit Identitäten in *"Startup.cs"*:</span><span class="sxs-lookup"><span data-stu-id="43a16-114">The following 2.0 example configures Facebook authentication with Identity in *Startup.cs*:</span></span>

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

<span data-ttu-id="43a16-115">Die `UseAuthentication` Methode fügt eine einzelne Komponente, die für die automatische Authentifizierung und die Behandlung von Anforderungen der Remoteauthentifizierung zuständig ist.</span><span class="sxs-lookup"><span data-stu-id="43a16-115">The `UseAuthentication` method adds a single authentication middleware component which is responsible for automatic authentication and the handling of remote authentication requests.</span></span> <span data-ttu-id="43a16-116">Er ersetzt alle einzelnen middlewarekomponenten mit einer einzelnen, gemeinsamen middlewarekomponente.</span><span class="sxs-lookup"><span data-stu-id="43a16-116">It replaces all of the individual middleware components with a single, common middleware component.</span></span>

<span data-ttu-id="43a16-117">Im folgenden finden Sie Anweisungen für jede wichtige Authentifizierungsschema 2.0.</span><span class="sxs-lookup"><span data-stu-id="43a16-117">Below are 2.0 migration instructions for each major authentication scheme.</span></span>

### <a name="cookie-based-authentication"></a><span data-ttu-id="43a16-118">Cookie-basierte Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="43a16-118">Cookie-based authentication</span></span>

<span data-ttu-id="43a16-119">Wählen Sie eine der folgenden beiden Optionen aus, und stellen Sie die erforderlichen Änderungen im *"Startup.cs"*:</span><span class="sxs-lookup"><span data-stu-id="43a16-119">Select one of the two options below, and make the necessary changes in *Startup.cs*:</span></span>

1. <span data-ttu-id="43a16-120">Cookies verwenden, mit der Identität</span><span class="sxs-lookup"><span data-stu-id="43a16-120">Use cookies with Identity</span></span>
    - <span data-ttu-id="43a16-121">Ersetzen Sie dies `UseIdentity` mit `UseAuthentication` in die `Configure` Methode:</span><span class="sxs-lookup"><span data-stu-id="43a16-121">Replace `UseIdentity` with `UseAuthentication` in the `Configure` method:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="43a16-122">Rufen Sie die `AddIdentity` -Methode in der die `ConfigureServices` Methode, um die Cookie-Authentifizierungsdienste hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="43a16-122">Invoke the `AddIdentity` method in the `ConfigureServices` method to add the cookie authentication services.</span></span>
    - <span data-ttu-id="43a16-123">Rufen Sie optional die `ConfigureApplicationCookie` oder `ConfigureExternalCookie` -Methode in der die `ConfigureServices` Methode, um die Identity-Cookie-Einstellungen zu optimieren.</span><span class="sxs-lookup"><span data-stu-id="43a16-123">Optionally, invoke the `ConfigureApplicationCookie` or `ConfigureExternalCookie` method in the `ConfigureServices` method to tweak the Identity cookie settings.</span></span>

        ```csharp
        services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();

        services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
        ```

2. <span data-ttu-id="43a16-124">Cookies verwenden, ohne Identity</span><span class="sxs-lookup"><span data-stu-id="43a16-124">Use cookies without Identity</span></span>
    - <span data-ttu-id="43a16-125">Ersetzen Sie die `UseCookieAuthentication` -Methodenaufruf in der `Configure` -Methode mit `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="43a16-125">Replace the `UseCookieAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="43a16-126">Rufen Sie die `AddAuthentication` und `AddCookie` Methoden in der `ConfigureServices` Methode:</span><span class="sxs-lookup"><span data-stu-id="43a16-126">Invoke the `AddAuthentication` and `AddCookie` methods in the `ConfigureServices` method:</span></span>

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

### <a name="jwt-bearer-authentication"></a><span data-ttu-id="43a16-127">JWT-Bearer-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="43a16-127">JWT Bearer Authentication</span></span>

<span data-ttu-id="43a16-128">Nehmen Sie folgende Änderungen in *"Startup.cs"*:</span><span class="sxs-lookup"><span data-stu-id="43a16-128">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="43a16-129">Ersetzen Sie die `UseJwtBearerAuthentication` -Methodenaufruf in der `Configure` -Methode mit `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="43a16-129">Replace the `UseJwtBearerAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="43a16-130">Rufen Sie die `AddJwtBearer` -Methode in der die `ConfigureServices` Methode:</span><span class="sxs-lookup"><span data-stu-id="43a16-130">Invoke the `AddJwtBearer` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    <span data-ttu-id="43a16-131">Dieser Codeausschnitt nicht Identität verwenden, damit das Standardschema soll, übergeben festgelegt werden `JwtBearerDefaults.AuthenticationScheme` auf die `AddAuthentication` Methode.</span><span class="sxs-lookup"><span data-stu-id="43a16-131">This code snippet doesn't use Identity, so the default scheme should be set by passing `JwtBearerDefaults.AuthenticationScheme` to the `AddAuthentication` method.</span></span>

### <a name="openid-connect-oidc-authentication"></a><span data-ttu-id="43a16-132">OpenID Connect (OIDC) Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="43a16-132">OpenID Connect (OIDC) authentication</span></span>

<span data-ttu-id="43a16-133">Nehmen Sie folgende Änderungen in *"Startup.cs"*:</span><span class="sxs-lookup"><span data-stu-id="43a16-133">Make the following changes in *Startup.cs*:</span></span>

- <span data-ttu-id="43a16-134">Ersetzen Sie die `UseOpenIdConnectAuthentication` -Methodenaufruf in der `Configure` -Methode mit `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="43a16-134">Replace the `UseOpenIdConnectAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="43a16-135">Rufen Sie die `AddOpenIdConnect` -Methode in der die `ConfigureServices` Methode:</span><span class="sxs-lookup"><span data-stu-id="43a16-135">Invoke the `AddOpenIdConnect` method in the `ConfigureServices` method:</span></span>

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

### <a name="facebook-authentication"></a><span data-ttu-id="43a16-136">Facebook-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="43a16-136">Facebook authentication</span></span>

<span data-ttu-id="43a16-137">Nehmen Sie folgende Änderungen in *"Startup.cs"*:</span><span class="sxs-lookup"><span data-stu-id="43a16-137">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="43a16-138">Ersetzen Sie die `UseFacebookAuthentication` -Methodenaufruf in der `Configure` -Methode mit `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="43a16-138">Replace the `UseFacebookAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="43a16-139">Rufen Sie die `AddFacebook` -Methode in der die `ConfigureServices` Methode:</span><span class="sxs-lookup"><span data-stu-id="43a16-139">Invoke the `AddFacebook` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a><span data-ttu-id="43a16-140">Google-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="43a16-140">Google authentication</span></span>

<span data-ttu-id="43a16-141">Nehmen Sie folgende Änderungen in *"Startup.cs"*:</span><span class="sxs-lookup"><span data-stu-id="43a16-141">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="43a16-142">Ersetzen Sie die `UseGoogleAuthentication` -Methodenaufruf in der `Configure` -Methode mit `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="43a16-142">Replace the `UseGoogleAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="43a16-143">Rufen Sie die `AddGoogle` -Methode in der die `ConfigureServices` Methode:</span><span class="sxs-lookup"><span data-stu-id="43a16-143">Invoke the `AddGoogle` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options =>
            {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });
    ```

### <a name="microsoft-account-authentication"></a><span data-ttu-id="43a16-144">Microsoft-Account-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="43a16-144">Microsoft Account authentication</span></span>

<span data-ttu-id="43a16-145">Nehmen Sie folgende Änderungen in *"Startup.cs"*:</span><span class="sxs-lookup"><span data-stu-id="43a16-145">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="43a16-146">Ersetzen Sie die `UseMicrosoftAccountAuthentication` -Methodenaufruf in der `Configure` -Methode mit `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="43a16-146">Replace the `UseMicrosoftAccountAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="43a16-147">Rufen Sie die `AddMicrosoftAccount` -Methode in der die `ConfigureServices` Methode:</span><span class="sxs-lookup"><span data-stu-id="43a16-147">Invoke the `AddMicrosoftAccount` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options =>
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ```

### <a name="twitter-authentication"></a><span data-ttu-id="43a16-148">Twitter-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="43a16-148">Twitter authentication</span></span>

<span data-ttu-id="43a16-149">Nehmen Sie folgende Änderungen in *"Startup.cs"*:</span><span class="sxs-lookup"><span data-stu-id="43a16-149">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="43a16-150">Ersetzen Sie die `UseTwitterAuthentication` -Methodenaufruf in der `Configure` -Methode mit `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="43a16-150">Replace the `UseTwitterAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="43a16-151">Rufen Sie die `AddTwitter` -Methode in der die `ConfigureServices` Methode:</span><span class="sxs-lookup"><span data-stu-id="43a16-151">Invoke the `AddTwitter` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options =>
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a><span data-ttu-id="43a16-152">Festlegen von Standardschemas für die Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="43a16-152">Setting default authentication schemes</span></span>

<span data-ttu-id="43a16-153">In 1.x die `AutomaticAuthenticate` und `AutomaticChallenge` Eigenschaften der [authenticationoptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) Basisklasse wurden auf einem einzelnen Authentifizierungsschema festgelegt werden soll.</span><span class="sxs-lookup"><span data-stu-id="43a16-153">In 1.x, the `AutomaticAuthenticate` and `AutomaticChallenge` properties of the [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) base class were intended to be set on a single authentication scheme.</span></span> <span data-ttu-id="43a16-154">Gab es keine gute Möglichkeit, dies durchzusetzen.</span><span class="sxs-lookup"><span data-stu-id="43a16-154">There was no good way to enforce this.</span></span>

<span data-ttu-id="43a16-155">In 2.0 wurden diese beiden Eigenschaften als Eigenschaften für die einzelnen entfernt `AuthenticationOptions` Instanz.</span><span class="sxs-lookup"><span data-stu-id="43a16-155">In 2.0, these two properties have been removed as properties on the individual `AuthenticationOptions` instance.</span></span> <span data-ttu-id="43a16-156">Sie können konfiguriert werden, der `AddAuthentication` Methodenaufruf innerhalb der `ConfigureServices` -Methode der *"Startup.cs"*:</span><span class="sxs-lookup"><span data-stu-id="43a16-156">They can be configured in the `AddAuthentication` method call within the `ConfigureServices` method of *Startup.cs*:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme);
```

<span data-ttu-id="43a16-157">Im obigen Codeausschnitt ist das Standardschema festgelegt ist, um `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span><span class="sxs-lookup"><span data-stu-id="43a16-157">In the preceding code snippet, the default scheme is set to `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="43a16-158">Verwenden Sie alternativ eine überladene Version der dem `AddAuthentication` Methode, um mehr als eine Eigenschaft festzulegen.</span><span class="sxs-lookup"><span data-stu-id="43a16-158">Alternatively, use an overloaded version of the `AddAuthentication` method to set more than one property.</span></span> <span data-ttu-id="43a16-159">Im folgenden Beispiel überladene Methode das Standardschema festgelegt ist, um `CookieAuthenticationDefaults.AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="43a16-159">In the following overloaded method example, the default scheme is set to `CookieAuthenticationDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="43a16-160">Das Authentifizierungsschema kann auch angegeben werden, innerhalb des einzelnen `[Authorize]` Attribute oder Autorisierungsrichtlinien.</span><span class="sxs-lookup"><span data-stu-id="43a16-160">The authentication scheme may alternatively be specified within your individual `[Authorize]` attributes or authorization policies.</span></span>

```csharp
services.AddAuthentication(options =>
{
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

<span data-ttu-id="43a16-161">Definieren Sie eine Standard-Schema in 2.0, wenn eine der folgenden Bedingungen zutrifft:</span><span class="sxs-lookup"><span data-stu-id="43a16-161">Define a default scheme in 2.0 if one of the following conditions is true:</span></span>
- <span data-ttu-id="43a16-162">Sie möchten die Benutzer automatisch angemeldet werden</span><span class="sxs-lookup"><span data-stu-id="43a16-162">You want the user to be automatically signed in</span></span>
- <span data-ttu-id="43a16-163">Sie verwenden die `[Authorize]` Attribut- oder Autorisierung Richtlinien ohne Angabe von Schemas</span><span class="sxs-lookup"><span data-stu-id="43a16-163">You use the `[Authorize]` attribute or authorization policies without specifying schemes</span></span>

<span data-ttu-id="43a16-164">Eine Ausnahme von dieser Regel wird die `AddIdentity` Methode.</span><span class="sxs-lookup"><span data-stu-id="43a16-164">An exception to this rule is the `AddIdentity` method.</span></span> <span data-ttu-id="43a16-165">Diese Methode fügt die Cookies für Sie und legt die Standard-authentifizieren und Abfrage von Schemas, die das Cookie für die Anwendung `IdentityConstants.ApplicationScheme`.</span><span class="sxs-lookup"><span data-stu-id="43a16-165">This method adds cookies for you and sets the default authenticate and challenge schemes to the application cookie `IdentityConstants.ApplicationScheme`.</span></span> <span data-ttu-id="43a16-166">Darüber hinaus wird die Standard-anmelden-Schema auf das externe Cookie `IdentityConstants.ExternalScheme`.</span><span class="sxs-lookup"><span data-stu-id="43a16-166">Additionally, it sets the default sign-in scheme to the external cookie `IdentityConstants.ExternalScheme`.</span></span>

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a><span data-ttu-id="43a16-167">Verwenden Sie "HttpContext" authentifizierungserweiterungen</span><span class="sxs-lookup"><span data-stu-id="43a16-167">Use HttpContext authentication extensions</span></span>

<span data-ttu-id="43a16-168">Die `IAuthenticationManager` Schnittstelle ist der Haupteinstiegspunkt, in dem 1.x-Authentifizierungssystem her.</span><span class="sxs-lookup"><span data-stu-id="43a16-168">The `IAuthenticationManager` interface is the main entry point into the 1.x authentication system.</span></span> <span data-ttu-id="43a16-169">Es wurde durch einen neuen Satz von ersetzt `HttpContext` Erweiterungsmethoden in den `Microsoft.AspNetCore.Authentication` Namespace.</span><span class="sxs-lookup"><span data-stu-id="43a16-169">It has been replaced with a new set of `HttpContext` extension methods in the `Microsoft.AspNetCore.Authentication` namespace.</span></span>

<span data-ttu-id="43a16-170">Z. B. 1.x-Projekte Verweis ein `Authentication` Eigenschaft:</span><span class="sxs-lookup"><span data-stu-id="43a16-170">For example, 1.x projects reference an `Authentication` property:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="43a16-171">Importieren Sie in 2.0-Projekten die `Microsoft.AspNetCore.Authentication` -Namespace, und löschen Sie die `Authentication` Eigenschaftenverweise:</span><span class="sxs-lookup"><span data-stu-id="43a16-171">In 2.0 projects, import the `Microsoft.AspNetCore.Authentication` namespace, and delete the `Authentication` property references:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a><span data-ttu-id="43a16-172">Windows Authentication (HTTP.sys / IISIntegration)</span><span class="sxs-lookup"><span data-stu-id="43a16-172">Windows Authentication (HTTP.sys / IISIntegration)</span></span>

<span data-ttu-id="43a16-173">Es gibt zwei Varianten der Windows-Authentifizierung:</span><span class="sxs-lookup"><span data-stu-id="43a16-173">There are two variations of Windows authentication:</span></span>
1. <span data-ttu-id="43a16-174">Der Host lässt nur authentifizierte Benutzer</span><span class="sxs-lookup"><span data-stu-id="43a16-174">The host only allows authenticated users</span></span>
2. <span data-ttu-id="43a16-175">Der Host ermöglicht sowohl anonymen und authentifizierte Benutzer</span><span class="sxs-lookup"><span data-stu-id="43a16-175">The host allows both anonymous and authenticated users</span></span>

<span data-ttu-id="43a16-176">Die erste Variante, die oben beschriebenen ist von den 2.0 Änderungen nicht betroffen.</span><span class="sxs-lookup"><span data-stu-id="43a16-176">The first variation described above is unaffected by the 2.0 changes.</span></span>

<span data-ttu-id="43a16-177">Die zweite Variante, die oben beschriebenen wird durch die 2.0-Änderungen beeinflusst.</span><span class="sxs-lookup"><span data-stu-id="43a16-177">The second variation described above is affected by the 2.0 changes.</span></span> <span data-ttu-id="43a16-178">Beispielsweise ermöglichen Sie möglicherweise werden es anonyme Benutzer in Ihrer app auf IIS oder [HTTP.sys](xref:fundamentals/servers/httpsys) layer jedoch autorisierende von Benutzern auf der Controllerebene.</span><span class="sxs-lookup"><span data-stu-id="43a16-178">As an example, you may be allowing anonymous users into your app at the IIS or [HTTP.sys](xref:fundamentals/servers/httpsys) layer but authorizing users at the Controller level.</span></span> <span data-ttu-id="43a16-179">In diesem Szenario legen Sie das Standardschema auf `IISDefaults.AuthenticationScheme` in die `Startup.ConfigureServices` Methode:</span><span class="sxs-lookup"><span data-stu-id="43a16-179">In this scenario, set the default scheme to `IISDefaults.AuthenticationScheme` in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

<span data-ttu-id="43a16-180">Fehler beim Festlegen der Standard-Schema wird entsprechend verhindert, dass der autorisierungsanforderung sollen nicht funktionieren.</span><span class="sxs-lookup"><span data-stu-id="43a16-180">Failure to set the default scheme accordingly prevents the authorize request to challenge from working.</span></span>

<a name="identity-cookie-options"></a>

## <a name="identitycookieoptions-instances"></a><span data-ttu-id="43a16-181">IdentityCookieOptions-Instanzen</span><span class="sxs-lookup"><span data-stu-id="43a16-181">IdentityCookieOptions instances</span></span>

<span data-ttu-id="43a16-182">Ein Nebeneffekt der 2.0 Änderungen ist die Umstellung auf die Verwendung benannter Optionen anstelle von Instanzen der Cookie-Optionen.</span><span class="sxs-lookup"><span data-stu-id="43a16-182">A side effect of the 2.0 changes is the switch to using named options instead of cookie options instances.</span></span> <span data-ttu-id="43a16-183">Die Möglichkeit, die die Identity-Cookie-Schemanamen anpassen wird entfernt.</span><span class="sxs-lookup"><span data-stu-id="43a16-183">The ability to customize the Identity cookie scheme names is removed.</span></span>

<span data-ttu-id="43a16-184">Z. B. 1.x Projekte [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection) übergeben eine `IdentityCookieOptions` Parameter in *AccountController.cs*.</span><span class="sxs-lookup"><span data-stu-id="43a16-184">For example, 1.x projects use [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) to pass an `IdentityCookieOptions` parameter into *AccountController.cs*.</span></span> <span data-ttu-id="43a16-185">Das Authentifizierungsschema für den externen Cookie wird von der bereitgestellten Instanz zugegriffen werden:</span><span class="sxs-lookup"><span data-stu-id="43a16-185">The external cookie authentication scheme is accessed from the provided instance:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

<span data-ttu-id="43a16-186">Die oben genannten Konstruktorinjektion in 2.0-Projekten nicht erforderlich ist und die `_externalCookieScheme` Feld gelöscht werden kann:</span><span class="sxs-lookup"><span data-stu-id="43a16-186">The aforementioned constructor injection becomes unnecessary in 2.0 projects, and the `_externalCookieScheme` field can be deleted:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

<span data-ttu-id="43a16-187">Die `IdentityConstants.ExternalScheme` Konstante kann direkt verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="43a16-187">The `IdentityConstants.ExternalScheme` constant can be used directly:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="navigation-properties"></a>

## <a name="add-identityuser-poco-navigation-properties"></a><span data-ttu-id="43a16-188">Hinzufügen von "identityuser" POCO Navigationseigenschaften</span><span class="sxs-lookup"><span data-stu-id="43a16-188">Add IdentityUser POCO navigation properties</span></span>

<span data-ttu-id="43a16-189">Die Entity Framework (EF) Core Navigationseigenschaften des basistexts `IdentityUser` POCO (Plain Old CLR Object) wurde entfernt.</span><span class="sxs-lookup"><span data-stu-id="43a16-189">The Entity Framework (EF) Core navigation properties of the base `IdentityUser` POCO (Plain Old CLR Object) have been removed.</span></span> <span data-ttu-id="43a16-190">Wenn Ihre 1.x-Projekt auf diese Eigenschaften verwendet, müssen Sie fügen sie manuell an die 2.0-Projekt hinzu:</span><span class="sxs-lookup"><span data-stu-id="43a16-190">If your 1.x project used these properties, manually add them back to the 2.0 project:</span></span>

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

<span data-ttu-id="43a16-191">Um doppelte Fremdschlüssel zu verhindern, wenn EF Core-Migrationen ausführen, fügen Sie den folgenden Ihre `IdentityDbContext` Klasse `OnModelCreating` -Methode (nach der `base.OnModelCreating();` aufrufen):</span><span class="sxs-lookup"><span data-stu-id="43a16-191">To prevent duplicate foreign keys when running EF Core Migrations, add the following to your `IdentityDbContext` class' `OnModelCreating` method (after the `base.OnModelCreating();` call):</span></span>

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

## <a name="replace-getexternalauthenticationschemes"></a><span data-ttu-id="43a16-192">Ersetzen Sie GetExternalAuthenticationSchemes</span><span class="sxs-lookup"><span data-stu-id="43a16-192">Replace GetExternalAuthenticationSchemes</span></span>

<span data-ttu-id="43a16-193">Die synchrone Methode `GetExternalAuthenticationSchemes` wurde durch eine asynchrone Version entfernt.</span><span class="sxs-lookup"><span data-stu-id="43a16-193">The synchronous method `GetExternalAuthenticationSchemes` was removed in favor of an asynchronous version.</span></span> <span data-ttu-id="43a16-194">1.x-Projekten müssen Sie den folgenden Code in *ManageController.cs*:</span><span class="sxs-lookup"><span data-stu-id="43a16-194">1.x projects have the following code in *ManageController.cs*:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

<span data-ttu-id="43a16-195">Diese Methode ist in *Login.cshtml* zu:</span><span class="sxs-lookup"><span data-stu-id="43a16-195">This method appears in *Login.cshtml* too:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?range=62,75-84)]

<span data-ttu-id="43a16-196">Verwenden Sie in 2.0-Projekten die `GetExternalAuthenticationSchemesAsync` Methode:</span><span class="sxs-lookup"><span data-stu-id="43a16-196">In 2.0 projects, use the `GetExternalAuthenticationSchemesAsync` method:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

<span data-ttu-id="43a16-197">In *Login.cshtml*, `AuthenticationScheme` Eigenschaft zugegriffen wird, der `foreach` Schleife wird nun `Name`:</span><span class="sxs-lookup"><span data-stu-id="43a16-197">In *Login.cshtml*, the `AuthenticationScheme` property accessed in the `foreach` loop changes to `Name`:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?range=62,75-84)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a><span data-ttu-id="43a16-198">Ändern der ManageLoginsViewModel-Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="43a16-198">ManageLoginsViewModel property change</span></span>

<span data-ttu-id="43a16-199">Ein `ManageLoginsViewModel` Objekt wird in verwendet die `ManageLogins` Aktion *ManageController.cs*.</span><span class="sxs-lookup"><span data-stu-id="43a16-199">A `ManageLoginsViewModel` object is used in the `ManageLogins` action of *ManageController.cs*.</span></span> <span data-ttu-id="43a16-200">In 1.x-Projekten, das Objekt des `OtherLogins` Eigenschaft Rückgabetyp ist `IList<AuthenticationDescription>`.</span><span class="sxs-lookup"><span data-stu-id="43a16-200">In 1.x projects, the object's `OtherLogins` property return type is `IList<AuthenticationDescription>`.</span></span> <span data-ttu-id="43a16-201">Dieser Rückgabetyp ist erforderlich, einen Import `Microsoft.AspNetCore.Http.Authentication`:</span><span class="sxs-lookup"><span data-stu-id="43a16-201">This return type requires an import of `Microsoft.AspNetCore.Http.Authentication`:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<span data-ttu-id="43a16-202">In 2.0-Projekten wird der Rückgabetyp nun `IList<AuthenticationScheme>`.</span><span class="sxs-lookup"><span data-stu-id="43a16-202">In 2.0 projects, the return type changes to `IList<AuthenticationScheme>`.</span></span> <span data-ttu-id="43a16-203">Diese neuen Rückgabetyp ist erforderlich, und Ersetzen Sie dabei die `Microsoft.AspNetCore.Http.Authentication` importieren Sie mit einem `Microsoft.AspNetCore.Authentication` importieren.</span><span class="sxs-lookup"><span data-stu-id="43a16-203">This new return type requires replacing the `Microsoft.AspNetCore.Http.Authentication` import with a `Microsoft.AspNetCore.Authentication` import.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a><span data-ttu-id="43a16-204">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="43a16-204">Additional resources</span></span>

<span data-ttu-id="43a16-205">Weitere Informationen und Diskussionen, finden Sie unter der [Diskussion Auth 2.0](https://github.com/aspnet/Security/issues/1338) Problem auf GitHub.</span><span class="sxs-lookup"><span data-stu-id="43a16-205">For additional details and discussion, see the [Discussion for Auth 2.0](https://github.com/aspnet/Security/issues/1338) issue on GitHub.</span></span>

---
title: Migrieren von Authentifizierungs- und Identitätseinstellungen nach ASP.NET Core 2.0
author: scottaddie
description: In diesem Artikel wird beschrieben, die am häufigsten verwendeten Schritte zum Migrieren von ASP.NET Core 1.x-Authentifizierung und Identifizierung zu ASP.NET Core 2.0.
ms.author: scaddie
ms.date: 06/13/2019
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: 3e8bc75b87a85159c9668b52eea32bb7d700be6c
ms.sourcegitcommit: 516f166c5f7cec54edf3d9c71e6e2ba53fb3b0e5
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67196384"
---
# <a name="migrate-authentication-and-identity-to-aspnet-core-20"></a><span data-ttu-id="f7767-103">Migrieren von Authentifizierungs- und Identitätseinstellungen nach ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="f7767-103">Migrate authentication and Identity to ASP.NET Core 2.0</span></span>

<span data-ttu-id="f7767-104">Durch [Scott Addie](https://github.com/scottaddie) und [Hao Kung](https://github.com/HaoK)</span><span class="sxs-lookup"><span data-stu-id="f7767-104">By [Scott Addie](https://github.com/scottaddie) and [Hao Kung](https://github.com/HaoK)</span></span>

<span data-ttu-id="f7767-105">ASP.NET Core 2.0 wurde ein neues Modell für die Authentifizierung und [Identität](xref:security/authentication/identity) vereinfacht die Konfiguration, die mithilfe von Diensten.</span><span class="sxs-lookup"><span data-stu-id="f7767-105">ASP.NET Core 2.0 has a new model for authentication and [Identity](xref:security/authentication/identity) that simplifies configuration by using services.</span></span> <span data-ttu-id="f7767-106">ASP.NET Core 1.x-Anwendungen, die Authentifizierungs- oder Identitätsfunktionen verwenden können aktualisiert werden, um das neue Modell zu verwenden, wie unten beschrieben.</span><span class="sxs-lookup"><span data-stu-id="f7767-106">ASP.NET Core 1.x applications that use authentication or Identity can be updated to use the new model as outlined below.</span></span>

## <a name="update-namespaces"></a><span data-ttu-id="f7767-107">Aktualisieren von namespaces</span><span class="sxs-lookup"><span data-stu-id="f7767-107">Update namespaces</span></span>

<span data-ttu-id="f7767-108">In 1.x, Klassen, z. B. `IdentityRole` und `IdentityUser` wurden gefunden, der `Microsoft.AspNetCore.Identity.EntityFrameworkCore` Namespace.</span><span class="sxs-lookup"><span data-stu-id="f7767-108">In 1.x, classes such `IdentityRole` and `IdentityUser` were found in the `Microsoft.AspNetCore.Identity.EntityFrameworkCore` namespace.</span></span>

<span data-ttu-id="f7767-109">In 2.0 die <xref:Microsoft.AspNetCore.Identity> Namespace wurden neue Anlaufstelle für mehrere solcher Klassen.</span><span class="sxs-lookup"><span data-stu-id="f7767-109">In 2.0, the <xref:Microsoft.AspNetCore.Identity> namespace became the new home for several of such classes.</span></span> <span data-ttu-id="f7767-110">Durch den Code für den Standard-Identität, betroffenen Klassen umfassen `ApplicationUser` und `Startup`.</span><span class="sxs-lookup"><span data-stu-id="f7767-110">With the default Identity code, affected classes include `ApplicationUser` and `Startup`.</span></span> <span data-ttu-id="f7767-111">Passen Sie Ihre `using` Anweisungen, um die betroffenen Verweise aufzulösen.</span><span class="sxs-lookup"><span data-stu-id="f7767-111">Adjust your `using` statements to resolve the affected references.</span></span>

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a><span data-ttu-id="f7767-112">Authentifizierungsmiddleware und Dienste</span><span class="sxs-lookup"><span data-stu-id="f7767-112">Authentication Middleware and services</span></span>

<span data-ttu-id="f7767-113">In 1.x-Projekten ist die Authentifizierung über Middleware konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="f7767-113">In 1.x projects, authentication is configured via middleware.</span></span> <span data-ttu-id="f7767-114">Eine Middleware-Methode wird für jede Authentifizierungsschema aufgerufen, die Sie unterstützen möchten.</span><span class="sxs-lookup"><span data-stu-id="f7767-114">A middleware method is invoked for each authentication scheme you want to support.</span></span>

<span data-ttu-id="f7767-115">Im folgenden 1.x-Beispiel konfiguriert die Facebook-Authentifizierung mit Identität in *"Startup.cs"* :</span><span class="sxs-lookup"><span data-stu-id="f7767-115">The following 1.x example configures Facebook authentication with Identity in *Startup.cs*:</span></span>

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

<span data-ttu-id="f7767-116">In 2.0-Projekten ist die Authentifizierung über Dienste konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="f7767-116">In 2.0 projects, authentication is configured via services.</span></span> <span data-ttu-id="f7767-117">Jede Authentifizierungsschema registriert ist, der `ConfigureServices` -Methode der *"Startup.cs"* .</span><span class="sxs-lookup"><span data-stu-id="f7767-117">Each authentication scheme is registered in the `ConfigureServices` method of *Startup.cs*.</span></span> <span data-ttu-id="f7767-118">Die `UseIdentity` Methode wird durch ersetzt `UseAuthentication`.</span><span class="sxs-lookup"><span data-stu-id="f7767-118">The `UseIdentity` method is replaced with `UseAuthentication`.</span></span>

<span data-ttu-id="f7767-119">Im folgenden Beispiel für die 2.0 konfiguriert Facebook-Authentifizierung mit Identitäten in *"Startup.cs"* :</span><span class="sxs-lookup"><span data-stu-id="f7767-119">The following 2.0 example configures Facebook authentication with Identity in *Startup.cs*:</span></span>

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

<span data-ttu-id="f7767-120">Die `UseAuthentication` Methode fügt eine einzelne Authentifizierung-Middleware-Komponente, die für die automatische Authentifizierung und die Behandlung von Anforderungen der Remoteauthentifizierung zuständig ist.</span><span class="sxs-lookup"><span data-stu-id="f7767-120">The `UseAuthentication` method adds a single authentication middleware component, which is responsible for automatic authentication and the handling of remote authentication requests.</span></span> <span data-ttu-id="f7767-121">Er ersetzt alle einzelnen middlewarekomponenten mit einer einzelnen, gemeinsamen middlewarekomponente.</span><span class="sxs-lookup"><span data-stu-id="f7767-121">It replaces all of the individual middleware components with a single, common middleware component.</span></span>

<span data-ttu-id="f7767-122">Im folgenden finden Sie Anweisungen für jede wichtige Authentifizierungsschema 2.0.</span><span class="sxs-lookup"><span data-stu-id="f7767-122">Below are 2.0 migration instructions for each major authentication scheme.</span></span>

### <a name="cookie-based-authentication"></a><span data-ttu-id="f7767-123">Cookie-basierte Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="f7767-123">Cookie-based authentication</span></span>

<span data-ttu-id="f7767-124">Wählen Sie eine der folgenden beiden Optionen aus, und stellen Sie die erforderlichen Änderungen im *"Startup.cs"* :</span><span class="sxs-lookup"><span data-stu-id="f7767-124">Select one of the two options below, and make the necessary changes in *Startup.cs*:</span></span>

1. <span data-ttu-id="f7767-125">Cookies verwenden, mit der Identität</span><span class="sxs-lookup"><span data-stu-id="f7767-125">Use cookies with Identity</span></span>
    - <span data-ttu-id="f7767-126">Ersetzen Sie dies `UseIdentity` mit `UseAuthentication` in die `Configure` Methode:</span><span class="sxs-lookup"><span data-stu-id="f7767-126">Replace `UseIdentity` with `UseAuthentication` in the `Configure` method:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="f7767-127">Rufen Sie die `AddIdentity` -Methode in der die `ConfigureServices` Methode, um die Cookie-Authentifizierungsdienste hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="f7767-127">Invoke the `AddIdentity` method in the `ConfigureServices` method to add the cookie authentication services.</span></span>
    - <span data-ttu-id="f7767-128">Rufen Sie optional die `ConfigureApplicationCookie` oder `ConfigureExternalCookie` -Methode in der die `ConfigureServices` Methode, um die Identity-Cookie-Einstellungen zu optimieren.</span><span class="sxs-lookup"><span data-stu-id="f7767-128">Optionally, invoke the `ConfigureApplicationCookie` or `ConfigureExternalCookie` method in the `ConfigureServices` method to tweak the Identity cookie settings.</span></span>

        ```csharp
        services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();

        services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
        ```

2. <span data-ttu-id="f7767-129">Cookies verwenden, ohne Identity</span><span class="sxs-lookup"><span data-stu-id="f7767-129">Use cookies without Identity</span></span>
    - <span data-ttu-id="f7767-130">Ersetzen Sie die `UseCookieAuthentication` -Methodenaufruf in der `Configure` -Methode mit `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="f7767-130">Replace the `UseCookieAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="f7767-131">Rufen Sie die `AddAuthentication` und `AddCookie` Methoden in der `ConfigureServices` Methode:</span><span class="sxs-lookup"><span data-stu-id="f7767-131">Invoke the `AddAuthentication` and `AddCookie` methods in the `ConfigureServices` method:</span></span>

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

### <a name="jwt-bearer-authentication"></a><span data-ttu-id="f7767-132">JWT-Bearer-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="f7767-132">JWT Bearer Authentication</span></span>

<span data-ttu-id="f7767-133">Nehmen Sie folgende Änderungen in *"Startup.cs"* :</span><span class="sxs-lookup"><span data-stu-id="f7767-133">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="f7767-134">Ersetzen Sie die `UseJwtBearerAuthentication` -Methodenaufruf in der `Configure` -Methode mit `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="f7767-134">Replace the `UseJwtBearerAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="f7767-135">Rufen Sie die `AddJwtBearer` -Methode in der die `ConfigureServices` Methode:</span><span class="sxs-lookup"><span data-stu-id="f7767-135">Invoke the `AddJwtBearer` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    <span data-ttu-id="f7767-136">Dieser Codeausschnitt nicht Identität verwenden, damit das Standardschema soll, übergeben festgelegt werden `JwtBearerDefaults.AuthenticationScheme` auf die `AddAuthentication` Methode.</span><span class="sxs-lookup"><span data-stu-id="f7767-136">This code snippet doesn't use Identity, so the default scheme should be set by passing `JwtBearerDefaults.AuthenticationScheme` to the `AddAuthentication` method.</span></span>

### <a name="openid-connect-oidc-authentication"></a><span data-ttu-id="f7767-137">OpenID Connect (OIDC) Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="f7767-137">OpenID Connect (OIDC) authentication</span></span>

<span data-ttu-id="f7767-138">Nehmen Sie folgende Änderungen in *"Startup.cs"* :</span><span class="sxs-lookup"><span data-stu-id="f7767-138">Make the following changes in *Startup.cs*:</span></span>

- <span data-ttu-id="f7767-139">Ersetzen Sie die `UseOpenIdConnectAuthentication` -Methodenaufruf in der `Configure` -Methode mit `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="f7767-139">Replace the `UseOpenIdConnectAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="f7767-140">Rufen Sie die `AddOpenIdConnect` -Methode in der die `ConfigureServices` Methode:</span><span class="sxs-lookup"><span data-stu-id="f7767-140">Invoke the `AddOpenIdConnect` method in the `ConfigureServices` method:</span></span>

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

- <span data-ttu-id="f7767-141">Ersetzen Sie die `PostLogoutRedirectUri` -Eigenschaft in der `OpenIdConnectOptions` Aktion mit `SignedOutRedirectUri`:</span><span class="sxs-lookup"><span data-stu-id="f7767-141">Replace the `PostLogoutRedirectUri` property in the `OpenIdConnectOptions` action with `SignedOutRedirectUri`:</span></span>

    ```csharp
    .AddOpenIdConnect(options =>
    {
        options.SignedOutRedirectUri = "https://contoso.com";
    });
    ```
    
### <a name="facebook-authentication"></a><span data-ttu-id="f7767-142">Facebook-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="f7767-142">Facebook authentication</span></span>

<span data-ttu-id="f7767-143">Nehmen Sie folgende Änderungen in *"Startup.cs"* :</span><span class="sxs-lookup"><span data-stu-id="f7767-143">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="f7767-144">Ersetzen Sie die `UseFacebookAuthentication` -Methodenaufruf in der `Configure` -Methode mit `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="f7767-144">Replace the `UseFacebookAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="f7767-145">Rufen Sie die `AddFacebook` -Methode in der die `ConfigureServices` Methode:</span><span class="sxs-lookup"><span data-stu-id="f7767-145">Invoke the `AddFacebook` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a><span data-ttu-id="f7767-146">Google-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="f7767-146">Google authentication</span></span>

<span data-ttu-id="f7767-147">Nehmen Sie folgende Änderungen in *"Startup.cs"* :</span><span class="sxs-lookup"><span data-stu-id="f7767-147">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="f7767-148">Ersetzen Sie die `UseGoogleAuthentication` -Methodenaufruf in der `Configure` -Methode mit `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="f7767-148">Replace the `UseGoogleAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="f7767-149">Rufen Sie die `AddGoogle` -Methode in der die `ConfigureServices` Methode:</span><span class="sxs-lookup"><span data-stu-id="f7767-149">Invoke the `AddGoogle` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options =>
            {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });
    ```

### <a name="microsoft-account-authentication"></a><span data-ttu-id="f7767-150">Microsoft-Account-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="f7767-150">Microsoft Account authentication</span></span>

<span data-ttu-id="f7767-151">Nehmen Sie folgende Änderungen in *"Startup.cs"* :</span><span class="sxs-lookup"><span data-stu-id="f7767-151">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="f7767-152">Ersetzen Sie die `UseMicrosoftAccountAuthentication` -Methodenaufruf in der `Configure` -Methode mit `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="f7767-152">Replace the `UseMicrosoftAccountAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="f7767-153">Rufen Sie die `AddMicrosoftAccount` -Methode in der die `ConfigureServices` Methode:</span><span class="sxs-lookup"><span data-stu-id="f7767-153">Invoke the `AddMicrosoftAccount` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options =>
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ```

### <a name="twitter-authentication"></a><span data-ttu-id="f7767-154">Twitter-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="f7767-154">Twitter authentication</span></span>

<span data-ttu-id="f7767-155">Nehmen Sie folgende Änderungen in *"Startup.cs"* :</span><span class="sxs-lookup"><span data-stu-id="f7767-155">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="f7767-156">Ersetzen Sie die `UseTwitterAuthentication` -Methodenaufruf in der `Configure` -Methode mit `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="f7767-156">Replace the `UseTwitterAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="f7767-157">Rufen Sie die `AddTwitter` -Methode in der die `ConfigureServices` Methode:</span><span class="sxs-lookup"><span data-stu-id="f7767-157">Invoke the `AddTwitter` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options =>
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a><span data-ttu-id="f7767-158">Festlegen von Standardschemas für die Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="f7767-158">Setting default authentication schemes</span></span>

<span data-ttu-id="f7767-159">In 1.x die `AutomaticAuthenticate` und `AutomaticChallenge` Eigenschaften der [authenticationoptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) Basisklasse wurden auf einem einzelnen Authentifizierungsschema festgelegt werden soll.</span><span class="sxs-lookup"><span data-stu-id="f7767-159">In 1.x, the `AutomaticAuthenticate` and `AutomaticChallenge` properties of the [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) base class were intended to be set on a single authentication scheme.</span></span> <span data-ttu-id="f7767-160">Gab es keine gute Möglichkeit, dies durchzusetzen.</span><span class="sxs-lookup"><span data-stu-id="f7767-160">There was no good way to enforce this.</span></span>

<span data-ttu-id="f7767-161">In 2.0 wurden diese beiden Eigenschaften als Eigenschaften für die einzelnen entfernt `AuthenticationOptions` Instanz.</span><span class="sxs-lookup"><span data-stu-id="f7767-161">In 2.0, these two properties have been removed as properties on the individual `AuthenticationOptions` instance.</span></span> <span data-ttu-id="f7767-162">Sie können konfiguriert werden, der `AddAuthentication` Methodenaufruf innerhalb der `ConfigureServices` -Methode der *"Startup.cs"* :</span><span class="sxs-lookup"><span data-stu-id="f7767-162">They can be configured in the `AddAuthentication` method call within the `ConfigureServices` method of *Startup.cs*:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme);
```

<span data-ttu-id="f7767-163">Im obigen Codeausschnitt ist das Standardschema festgelegt ist, um `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span><span class="sxs-lookup"><span data-stu-id="f7767-163">In the preceding code snippet, the default scheme is set to `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="f7767-164">Verwenden Sie alternativ eine überladene Version der dem `AddAuthentication` Methode, um mehr als eine Eigenschaft festzulegen.</span><span class="sxs-lookup"><span data-stu-id="f7767-164">Alternatively, use an overloaded version of the `AddAuthentication` method to set more than one property.</span></span> <span data-ttu-id="f7767-165">Im folgenden Beispiel überladene Methode das Standardschema festgelegt ist, um `CookieAuthenticationDefaults.AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="f7767-165">In the following overloaded method example, the default scheme is set to `CookieAuthenticationDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="f7767-166">Das Authentifizierungsschema kann auch angegeben werden, innerhalb des einzelnen `[Authorize]` Attribute oder Autorisierungsrichtlinien.</span><span class="sxs-lookup"><span data-stu-id="f7767-166">The authentication scheme may alternatively be specified within your individual `[Authorize]` attributes or authorization policies.</span></span>

```csharp
services.AddAuthentication(options =>
{
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

<span data-ttu-id="f7767-167">Definieren Sie eine Standard-Schema in 2.0, wenn eine der folgenden Bedingungen zutrifft:</span><span class="sxs-lookup"><span data-stu-id="f7767-167">Define a default scheme in 2.0 if one of the following conditions is true:</span></span>
- <span data-ttu-id="f7767-168">Sie möchten die Benutzer automatisch angemeldet werden</span><span class="sxs-lookup"><span data-stu-id="f7767-168">You want the user to be automatically signed in</span></span>
- <span data-ttu-id="f7767-169">Sie verwenden die `[Authorize]` Attribut- oder Autorisierung Richtlinien ohne Angabe von Schemas</span><span class="sxs-lookup"><span data-stu-id="f7767-169">You use the `[Authorize]` attribute or authorization policies without specifying schemes</span></span>

<span data-ttu-id="f7767-170">Eine Ausnahme von dieser Regel wird die `AddIdentity` Methode.</span><span class="sxs-lookup"><span data-stu-id="f7767-170">An exception to this rule is the `AddIdentity` method.</span></span> <span data-ttu-id="f7767-171">Diese Methode fügt die Cookies für Sie und legt die Standard-authentifizieren und Abfrage von Schemas, die das Cookie für die Anwendung `IdentityConstants.ApplicationScheme`.</span><span class="sxs-lookup"><span data-stu-id="f7767-171">This method adds cookies for you and sets the default authenticate and challenge schemes to the application cookie `IdentityConstants.ApplicationScheme`.</span></span> <span data-ttu-id="f7767-172">Darüber hinaus wird die Standard-anmelden-Schema auf das externe Cookie `IdentityConstants.ExternalScheme`.</span><span class="sxs-lookup"><span data-stu-id="f7767-172">Additionally, it sets the default sign-in scheme to the external cookie `IdentityConstants.ExternalScheme`.</span></span>

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a><span data-ttu-id="f7767-173">Verwenden Sie "HttpContext" authentifizierungserweiterungen</span><span class="sxs-lookup"><span data-stu-id="f7767-173">Use HttpContext authentication extensions</span></span>

<span data-ttu-id="f7767-174">Die `IAuthenticationManager` Schnittstelle ist der Haupteinstiegspunkt, in dem 1.x-Authentifizierungssystem her.</span><span class="sxs-lookup"><span data-stu-id="f7767-174">The `IAuthenticationManager` interface is the main entry point into the 1.x authentication system.</span></span> <span data-ttu-id="f7767-175">Es wurde durch einen neuen Satz von ersetzt `HttpContext` Erweiterungsmethoden in den `Microsoft.AspNetCore.Authentication` Namespace.</span><span class="sxs-lookup"><span data-stu-id="f7767-175">It has been replaced with a new set of `HttpContext` extension methods in the `Microsoft.AspNetCore.Authentication` namespace.</span></span>

<span data-ttu-id="f7767-176">Z. B. 1.x-Projekte Verweis ein `Authentication` Eigenschaft:</span><span class="sxs-lookup"><span data-stu-id="f7767-176">For example, 1.x projects reference an `Authentication` property:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="f7767-177">Importieren Sie in 2.0-Projekten die `Microsoft.AspNetCore.Authentication` -Namespace, und löschen Sie die `Authentication` Eigenschaftenverweise:</span><span class="sxs-lookup"><span data-stu-id="f7767-177">In 2.0 projects, import the `Microsoft.AspNetCore.Authentication` namespace, and delete the `Authentication` property references:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a><span data-ttu-id="f7767-178">Windows Authentication (HTTP.sys / IISIntegration)</span><span class="sxs-lookup"><span data-stu-id="f7767-178">Windows Authentication (HTTP.sys / IISIntegration)</span></span>

<span data-ttu-id="f7767-179">Es gibt zwei Varianten der Windows-Authentifizierung:</span><span class="sxs-lookup"><span data-stu-id="f7767-179">There are two variations of Windows authentication:</span></span>
1. <span data-ttu-id="f7767-180">Der Host lässt nur authentifizierte Benutzer</span><span class="sxs-lookup"><span data-stu-id="f7767-180">The host only allows authenticated users</span></span>
2. <span data-ttu-id="f7767-181">Der Host ermöglicht sowohl anonymen und authentifizierte Benutzer</span><span class="sxs-lookup"><span data-stu-id="f7767-181">The host allows both anonymous and authenticated users</span></span>

<span data-ttu-id="f7767-182">Die erste Variante, die oben beschriebenen ist von den 2.0 Änderungen nicht betroffen.</span><span class="sxs-lookup"><span data-stu-id="f7767-182">The first variation described above is unaffected by the 2.0 changes.</span></span>

<span data-ttu-id="f7767-183">Die zweite Variante, die oben beschriebenen wird durch die 2.0-Änderungen beeinflusst.</span><span class="sxs-lookup"><span data-stu-id="f7767-183">The second variation described above is affected by the 2.0 changes.</span></span> <span data-ttu-id="f7767-184">Zum Beispiel Sie möglicherweise sein können Sie anonyme Benutzer in Ihrer app auf IIS oder [HTTP.sys](xref:fundamentals/servers/httpsys) layer jedoch autorisierende von Benutzern auf der Controllerebene.</span><span class="sxs-lookup"><span data-stu-id="f7767-184">For example, you may be allowing anonymous users into your app at the IIS or [HTTP.sys](xref:fundamentals/servers/httpsys) layer but authorizing users at the Controller level.</span></span> <span data-ttu-id="f7767-185">In diesem Szenario legen Sie das Standardschema auf `IISDefaults.AuthenticationScheme` in die `Startup.ConfigureServices` Methode:</span><span class="sxs-lookup"><span data-stu-id="f7767-185">In this scenario, set the default scheme to `IISDefaults.AuthenticationScheme` in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

<span data-ttu-id="f7767-186">Fehler beim Festlegen der Standard-Schema wird verhindert, dass der autorisierungsanforderung sollen nicht funktionieren.</span><span class="sxs-lookup"><span data-stu-id="f7767-186">Failure to set the default scheme prevents the authorize request to challenge from working.</span></span>

<a name="identity-cookie-options"></a>

## <a name="identitycookieoptions-instances"></a><span data-ttu-id="f7767-187">IdentityCookieOptions-Instanzen</span><span class="sxs-lookup"><span data-stu-id="f7767-187">IdentityCookieOptions instances</span></span>

<span data-ttu-id="f7767-188">Ein Nebeneffekt der 2.0 Änderungen ist die Umstellung auf die Verwendung benannter Optionen anstelle von Instanzen der Cookie-Optionen.</span><span class="sxs-lookup"><span data-stu-id="f7767-188">A side effect of the 2.0 changes is the switch to using named options instead of cookie options instances.</span></span> <span data-ttu-id="f7767-189">Die Möglichkeit, die die Identity-Cookie-Schemanamen anpassen wird entfernt.</span><span class="sxs-lookup"><span data-stu-id="f7767-189">The ability to customize the Identity cookie scheme names is removed.</span></span>

<span data-ttu-id="f7767-190">Z. B. 1.x Projekte [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection) übergeben eine `IdentityCookieOptions` Parameter in *AccountController.cs* und *ManageController.cs*.</span><span class="sxs-lookup"><span data-stu-id="f7767-190">For example, 1.x projects use [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) to pass an `IdentityCookieOptions` parameter into *AccountController.cs* and *ManageController.cs*.</span></span> <span data-ttu-id="f7767-191">Das Authentifizierungsschema für den externen Cookie wird von der bereitgestellten Instanz zugegriffen werden:</span><span class="sxs-lookup"><span data-stu-id="f7767-191">The external cookie authentication scheme is accessed from the provided instance:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

<span data-ttu-id="f7767-192">Die oben genannten Konstruktorinjektion in 2.0-Projekten nicht erforderlich ist und die `_externalCookieScheme` Feld gelöscht werden kann:</span><span class="sxs-lookup"><span data-stu-id="f7767-192">The aforementioned constructor injection becomes unnecessary in 2.0 projects, and the `_externalCookieScheme` field can be deleted:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

<span data-ttu-id="f7767-193">1.x-Projekten verwendet die `_externalCookieScheme` Feld wie folgt:</span><span class="sxs-lookup"><span data-stu-id="f7767-193">1.x projects used the `_externalCookieScheme` field as follows:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="f7767-194">Ersetzen Sie in 2.0-Projekten des vorangehenden Codes durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="f7767-194">In 2.0 projects, replace the preceding code with the following.</span></span> <span data-ttu-id="f7767-195">Die `IdentityConstants.ExternalScheme` Konstante kann direkt verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="f7767-195">The `IdentityConstants.ExternalScheme` constant can be used directly.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="f7767-196">Beheben Sie die neu hinzugefügte `SignOutAsync` aufrufen, indem Sie den folgenden Namespace importieren:</span><span class="sxs-lookup"><span data-stu-id="f7767-196">Resolve the newly added `SignOutAsync` call by importing the following namespace:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationImport)]

<a name="navigation-properties"></a>

## <a name="add-identityuser-poco-navigation-properties"></a><span data-ttu-id="f7767-197">Hinzufügen von "identityuser" POCO Navigationseigenschaften</span><span class="sxs-lookup"><span data-stu-id="f7767-197">Add IdentityUser POCO navigation properties</span></span>

<span data-ttu-id="f7767-198">Die Entity Framework (EF) Core Navigationseigenschaften des basistexts `IdentityUser` POCO (Plain Old CLR Object) wurde entfernt.</span><span class="sxs-lookup"><span data-stu-id="f7767-198">The Entity Framework (EF) Core navigation properties of the base `IdentityUser` POCO (Plain Old CLR Object) have been removed.</span></span> <span data-ttu-id="f7767-199">Wenn Ihre 1.x-Projekt auf diese Eigenschaften verwendet, müssen Sie fügen sie manuell an die 2.0-Projekt hinzu:</span><span class="sxs-lookup"><span data-stu-id="f7767-199">If your 1.x project used these properties, manually add them back to the 2.0 project:</span></span>

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

<span data-ttu-id="f7767-200">Um doppelte Fremdschlüssel zu verhindern, wenn EF Core-Migrationen ausführen, fügen Sie den folgenden Ihre `IdentityDbContext` Klasse `OnModelCreating` -Methode (nach der `base.OnModelCreating();` aufrufen):</span><span class="sxs-lookup"><span data-stu-id="f7767-200">To prevent duplicate foreign keys when running EF Core Migrations, add the following to your `IdentityDbContext` class' `OnModelCreating` method (after the `base.OnModelCreating();` call):</span></span>

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

## <a name="replace-getexternalauthenticationschemes"></a><span data-ttu-id="f7767-201">Ersetzen Sie GetExternalAuthenticationSchemes</span><span class="sxs-lookup"><span data-stu-id="f7767-201">Replace GetExternalAuthenticationSchemes</span></span>

<span data-ttu-id="f7767-202">Die synchrone Methode `GetExternalAuthenticationSchemes` wurde durch eine asynchrone Version entfernt.</span><span class="sxs-lookup"><span data-stu-id="f7767-202">The synchronous method `GetExternalAuthenticationSchemes` was removed in favor of an asynchronous version.</span></span> <span data-ttu-id="f7767-203">1.x-Projekten müssen Sie den folgenden Code in *Controllers/ManageController.cs*:</span><span class="sxs-lookup"><span data-stu-id="f7767-203">1.x projects have the following code in *Controllers/ManageController.cs*:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

<span data-ttu-id="f7767-204">Diese Methode ist in *Views/Account/Login.cshtml* zu:</span><span class="sxs-lookup"><span data-stu-id="f7767-204">This method appears in *Views/Account/Login.cshtml* too:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemes&highlight=2)]

<span data-ttu-id="f7767-205">Verwenden Sie in 2.0-Projekten die <xref:Microsoft.AspNetCore.Identity.SignInManager`1.GetExternalAuthenticationSchemesAsync*> Methode.</span><span class="sxs-lookup"><span data-stu-id="f7767-205">In 2.0 projects, use the <xref:Microsoft.AspNetCore.Identity.SignInManager`1.GetExternalAuthenticationSchemesAsync*> method.</span></span> <span data-ttu-id="f7767-206">Die Änderung der *ManageController.cs* ähnelt dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="f7767-206">The change in *ManageController.cs* resembles the following code:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

<span data-ttu-id="f7767-207">In *Login.cshtml*, `AuthenticationScheme` Eigenschaft zugegriffen wird, der `foreach` Schleife wird nun `Name`:</span><span class="sxs-lookup"><span data-stu-id="f7767-207">In *Login.cshtml*, the `AuthenticationScheme` property accessed in the `foreach` loop changes to `Name`:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemesAsync&highlight=2,19)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a><span data-ttu-id="f7767-208">Ändern der ManageLoginsViewModel-Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="f7767-208">ManageLoginsViewModel property change</span></span>

<span data-ttu-id="f7767-209">Ein `ManageLoginsViewModel` Objekt wird in verwendet die `ManageLogins` Aktion *ManageController.cs*.</span><span class="sxs-lookup"><span data-stu-id="f7767-209">A `ManageLoginsViewModel` object is used in the `ManageLogins` action of *ManageController.cs*.</span></span> <span data-ttu-id="f7767-210">In 1.x-Projekten, das Objekt des `OtherLogins` Eigenschaft Rückgabetyp ist `IList<AuthenticationDescription>`.</span><span class="sxs-lookup"><span data-stu-id="f7767-210">In 1.x projects, the object's `OtherLogins` property return type is `IList<AuthenticationDescription>`.</span></span> <span data-ttu-id="f7767-211">Dieser Rückgabetyp ist erforderlich, einen Import `Microsoft.AspNetCore.Http.Authentication`:</span><span class="sxs-lookup"><span data-stu-id="f7767-211">This return type requires an import of `Microsoft.AspNetCore.Http.Authentication`:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<span data-ttu-id="f7767-212">In 2.0-Projekten wird der Rückgabetyp nun `IList<AuthenticationScheme>`.</span><span class="sxs-lookup"><span data-stu-id="f7767-212">In 2.0 projects, the return type changes to `IList<AuthenticationScheme>`.</span></span> <span data-ttu-id="f7767-213">Diese neuen Rückgabetyp ist erforderlich, und Ersetzen Sie dabei die `Microsoft.AspNetCore.Http.Authentication` importieren Sie mit einem `Microsoft.AspNetCore.Authentication` importieren.</span><span class="sxs-lookup"><span data-stu-id="f7767-213">This new return type requires replacing the `Microsoft.AspNetCore.Http.Authentication` import with a `Microsoft.AspNetCore.Authentication` import.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a><span data-ttu-id="f7767-214">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="f7767-214">Additional resources</span></span>

<span data-ttu-id="f7767-215">Weitere Informationen finden Sie unter den [Diskussion Auth 2.0](https://github.com/aspnet/Security/issues/1338) Problem auf GitHub.</span><span class="sxs-lookup"><span data-stu-id="f7767-215">For more information, see the [Discussion for Auth 2.0](https://github.com/aspnet/Security/issues/1338) issue on GitHub.</span></span>

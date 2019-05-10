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
# <a name="authorize-with-a-specific-scheme-in-aspnet-core"></a><span data-ttu-id="15377-103">Autorisieren Sie mit einem bestimmten Schema in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="15377-103">Authorize with a specific scheme in ASP.NET Core</span></span>

<span data-ttu-id="15377-104">In einigen Szenarien, z. B. Single Page Applications (SPAs) ist es üblich, mehrere Authentifizierungsmethoden zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="15377-104">In some scenarios, such as Single Page Applications (SPAs), it's common to use multiple authentication methods.</span></span> <span data-ttu-id="15377-105">Beispielsweise kann die app cookiebasierte Authentifizierung für die Anmeldung und JWT-Bearer-Authentifizierung für JavaScript-Anforderungen verwenden.</span><span class="sxs-lookup"><span data-stu-id="15377-105">For example, the app may use cookie-based authentication to log in and JWT bearer authentication for JavaScript requests.</span></span> <span data-ttu-id="15377-106">In einigen Fällen möglicherweise die app mehrere Instanzen eines Handlers für die Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="15377-106">In some cases, the app may have multiple instances of an authentication handler.</span></span> <span data-ttu-id="15377-107">Z. B. wird zwei Cookie-Handler, von denen einer eine grundlegende Identität enthält, und eine erstellt, wenn ein Multi-Factor Authentication (MFA) ausgelöst wurde.</span><span class="sxs-lookup"><span data-stu-id="15377-107">For example, two cookie handlers where one contains a basic identity and one is created when a multi-factor authentication (MFA) has been triggered.</span></span> <span data-ttu-id="15377-108">MFA kann ausgelöst werden, da der Benutzer einen Vorgang angefordert, der zusätzliche Sicherheit erfordert.</span><span class="sxs-lookup"><span data-stu-id="15377-108">MFA may be triggered because the user requested an operation that requires extra security.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="15377-109">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="15377-109">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="15377-110">Ein betroffenes Authentifizierungsschema heißt, wenn der Authentifizierungsdienst während der Authentifizierung konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="15377-110">An authentication scheme is named when the authentication service is configured during authentication.</span></span> <span data-ttu-id="15377-111">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="15377-111">For example:</span></span>

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

<span data-ttu-id="15377-112">Im vorangehenden Code wurden zwei authentifizierungshandler hinzugefügt: eine für Cookies und eine für trägertoken.</span><span class="sxs-lookup"><span data-stu-id="15377-112">In the preceding code, two authentication handlers have been added: one for cookies and one for bearer.</span></span>

>[!NOTE]
><span data-ttu-id="15377-113">Die Angabe der Standard-Schema führt zu den `HttpContext.User` -Eigenschaft, die auf dieser Identität festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="15377-113">Specifying the default scheme results in the `HttpContext.User` property being set to that identity.</span></span> <span data-ttu-id="15377-114">Wenn Sie dieses Verhalten nicht möchten, deaktivieren Sie es durch Aufrufen der parameterlose Form `AddAuthentication`.</span><span class="sxs-lookup"><span data-stu-id="15377-114">If that behavior isn't desired, disable it by invoking the parameterless form of `AddAuthentication`.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="15377-115">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="15377-115">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="15377-116">Authentifizierungsschemas heißen, wenn Authentifizierung-Middleware, die während der Authentifizierung konfiguriert sind.</span><span class="sxs-lookup"><span data-stu-id="15377-116">Authentication schemes are named when authentication middlewares are configured during authentication.</span></span> <span data-ttu-id="15377-117">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="15377-117">For example:</span></span>

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

<span data-ttu-id="15377-118">Im vorangehenden Code wurden zwei Authentifizierung-Middleware hinzugefügt: eine für Cookies und eine für trägertoken.</span><span class="sxs-lookup"><span data-stu-id="15377-118">In the preceding code, two authentication middlewares have been added: one for cookies and one for bearer.</span></span>

>[!NOTE]
><span data-ttu-id="15377-119">Die Angabe der Standard-Schema führt zu den `HttpContext.User` -Eigenschaft, die auf dieser Identität festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="15377-119">Specifying the default scheme results in the `HttpContext.User` property being set to that identity.</span></span> <span data-ttu-id="15377-120">Wenn Sie dieses Verhalten nicht möchten, deaktivieren Sie es durch Festlegen der `AuthenticationOptions.AutomaticAuthenticate` Eigenschaft `false`.</span><span class="sxs-lookup"><span data-stu-id="15377-120">If that behavior isn't desired, disable it by setting the `AuthenticationOptions.AutomaticAuthenticate` property to `false`.</span></span>

---

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a><span data-ttu-id="15377-121">Wählen das Schema mit dem Authorize-Attribut</span><span class="sxs-lookup"><span data-stu-id="15377-121">Selecting the scheme with the Authorize attribute</span></span>

<span data-ttu-id="15377-122">Die app zum Zeitpunkt der Autorisierung gibt an, dass der Handler, der verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="15377-122">At the point of authorization, the app indicates the handler to be used.</span></span> <span data-ttu-id="15377-123">Wählen Sie den Handler für die app wird mit dem durch Übergeben einer durch Trennzeichen getrennte Liste der Authentifizierungsschemas autorisiert `[Authorize]`.</span><span class="sxs-lookup"><span data-stu-id="15377-123">Select the handler with which the app will authorize by passing a comma-delimited list of authentication schemes to `[Authorize]`.</span></span> <span data-ttu-id="15377-124">Die `[Authorize]` Attribut gibt an, das Authentifizierungsschema oder Schemas verwenden, unabhängig davon, ob ein Standardwert konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="15377-124">The `[Authorize]` attribute specifies the authentication scheme or schemes to use regardless of whether a default is configured.</span></span> <span data-ttu-id="15377-125">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="15377-125">For example:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="15377-126">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="15377-126">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

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

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="15377-127">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="15377-127">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

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

<span data-ttu-id="15377-128">Im vorherigen Beispiel sowohl die Cookie-als auch das Bearer-Handler ausgeführt und haben die Möglichkeit, zu erstellen, und fügen eine Identität für den aktuellen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="15377-128">In the preceding example, both the cookie and bearer handlers run and have a chance to create and append an identity for the current user.</span></span> <span data-ttu-id="15377-129">Führt durch Angabe eines Schemas nur an, der entsprechende Handler.</span><span class="sxs-lookup"><span data-stu-id="15377-129">By specifying a single scheme only, the corresponding handler runs.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="15377-130">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="15377-130">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
[Authorize(AuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="15377-131">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="15377-131">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
[Authorize(ActiveAuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

---

<span data-ttu-id="15377-132">Im vorangehenden Code ausgeführt wird nur der Handler mit dem Schema "Bearer" aus.</span><span class="sxs-lookup"><span data-stu-id="15377-132">In the preceding code, only the handler with the "Bearer" scheme runs.</span></span> <span data-ttu-id="15377-133">Cookiebasierte Identitäten werden ignoriert.</span><span class="sxs-lookup"><span data-stu-id="15377-133">Any cookie-based identities are ignored.</span></span>

## <a name="selecting-the-scheme-with-policies"></a><span data-ttu-id="15377-134">Wählen das Schema mit Richtlinien</span><span class="sxs-lookup"><span data-stu-id="15377-134">Selecting the scheme with policies</span></span>

<span data-ttu-id="15377-135">Wenn, geben Sie die gewünschten Schemas in möchten [Richtlinie](xref:security/authorization/policies), Sie können festlegen, die `AuthenticationSchemes` Auflistung bei Ihrer Richtlinie hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="15377-135">If you prefer to specify the desired schemes in [policy](xref:security/authorization/policies), you can set the `AuthenticationSchemes` collection when adding your policy:</span></span>

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

<span data-ttu-id="15377-136">Im vorherigen Beispiel wird die Richtlinie "Over18" nur ausgeführt, mit der Identität, die vom Handler "Bearer" erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="15377-136">In the preceding example, the "Over18" policy only runs against the identity created by the "Bearer" handler.</span></span> <span data-ttu-id="15377-137">Verwenden Sie die Richtlinie durch Festlegen der `[Authorize]` des Attributs `Policy` Eigenschaft:</span><span class="sxs-lookup"><span data-stu-id="15377-137">Use the policy by setting the `[Authorize]` attribute's `Policy` property:</span></span>

```csharp
[Authorize(Policy = "Over18")]
public class RegistrationController : Controller
```

::: moniker range=">= aspnetcore-2.0"

## <a name="use-multiple-authentication-schemes"></a><span data-ttu-id="15377-138">Verwenden Sie mehrere Authentifizierungsschemas</span><span class="sxs-lookup"><span data-stu-id="15377-138">Use multiple authentication schemes</span></span>

<span data-ttu-id="15377-139">Einige apps müssen möglicherweise mehrere Arten von Authentifizierung zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="15377-139">Some apps may need to support multiple types of authentication.</span></span> <span data-ttu-id="15377-140">Ihre app kann z. B. Benutzer aus Azure Active Directory und aus einer Benutzerdatenbank authentifizieren.</span><span class="sxs-lookup"><span data-stu-id="15377-140">For example, your app might authenticate users from Azure Active Directory and from a users database.</span></span> <span data-ttu-id="15377-141">Ein weiteres Beispiel ist eine app, die Benutzer aus Active Directory Federation Services und Azure Active Directory B2C authentifiziert.</span><span class="sxs-lookup"><span data-stu-id="15377-141">Another example is an app that authenticates users from both Active Directory Federation Services and Azure Active Directory B2C.</span></span> <span data-ttu-id="15377-142">In diesem Fall sollte die app ein JWT-Bearer-Token von mehreren Ausstellern akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="15377-142">In this case, the app should accept a JWT bearer token from several issuers.</span></span>

<span data-ttu-id="15377-143">Fügen Sie alle Authentifizierungsschemas, die Sie übernehmen möchten.</span><span class="sxs-lookup"><span data-stu-id="15377-143">Add all authentication schemes you'd like to accept.</span></span> <span data-ttu-id="15377-144">Im folgenden code wird beispielsweise `Startup.ConfigureServices` fügt zwei JWT-Bearer-Authentifizierungsschemas mit unterschiedliche Aussteller:</span><span class="sxs-lookup"><span data-stu-id="15377-144">For example, the following code in `Startup.ConfigureServices` adds two JWT bearer authentication schemes with different issuers:</span></span>

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
> <span data-ttu-id="15377-145">Nur ein JWT-Bearer-Authentifizierung registriert ist, mit dem Standard-Authentifizierungsschema `JwtBearerDefaults.AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="15377-145">Only one JWT bearer authentication is registered with the default authentication scheme `JwtBearerDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="15377-146">Zusätzlicher Authentifizierung muss mit einem eindeutigen Authentifizierungsschema registriert werden.</span><span class="sxs-lookup"><span data-stu-id="15377-146">Additional authentication has to be registered with a unique authentication scheme.</span></span>

<span data-ttu-id="15377-147">Der nächste Schritt ist die Standardrichtlinie für die Autorisierung zum Akzeptieren von beide Authentifizierungsschemas zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="15377-147">The next step is to update the default authorization policy to accept both authentication schemes.</span></span> <span data-ttu-id="15377-148">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="15377-148">For example:</span></span>

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

<span data-ttu-id="15377-149">Wie die Standardrichtlinie für die Autorisierung überschrieben wird, ist es möglich, verwenden die `[Authorize]` Attribut in Controllern.</span><span class="sxs-lookup"><span data-stu-id="15377-149">As the default authorization policy is overridden, it's possible to use the `[Authorize]` attribute in controllers.</span></span> <span data-ttu-id="15377-150">Der Controller akzeptiert Anforderungen klicken Sie dann mit der von der ersten oder zweiten Aussteller ausgestellte JWT.</span><span class="sxs-lookup"><span data-stu-id="15377-150">The controller then accepts requests with JWT issued by the first or second issuer.</span></span>

::: moniker-end

---
title: Autorisieren mit einem bestimmten Schema in ASP.net Core
author: rick-anderson
description: In diesem Artikel wird erläutert, wie Sie die Identität auf ein bestimmtes Schema beschränken, wenn Sie mit mehreren Authentifizierungsmethoden arbeiten.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
uid: security/authorization/limitingidentitybyscheme
ms.openlocfilehash: 38da80519b9d5d097c24d38b5a37503174629fc4
ms.sourcegitcommit: 4818385c3cfe0805e15138a2c1785b62deeaab90
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2019
ms.locfileid: "73896968"
---
# <a name="authorize-with-a-specific-scheme-in-aspnet-core"></a><span data-ttu-id="4aa2e-103">Autorisieren mit einem bestimmten Schema in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="4aa2e-103">Authorize with a specific scheme in ASP.NET Core</span></span>

<span data-ttu-id="4aa2e-104">In einigen Szenarien, z. b. Single-Page-Anwendungen (Spas), ist es üblich, mehrere Authentifizierungsmethoden zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="4aa2e-104">In some scenarios, such as Single Page Applications (SPAs), it's common to use multiple authentication methods.</span></span> <span data-ttu-id="4aa2e-105">Die APP kann z. b. die cookiebasierte Authentifizierung für die Anmeldung und die JWT-Träger Authentifizierung für JavaScript-Anforderungen verwenden.</span><span class="sxs-lookup"><span data-stu-id="4aa2e-105">For example, the app may use cookie-based authentication to log in and JWT bearer authentication for JavaScript requests.</span></span> <span data-ttu-id="4aa2e-106">In einigen Fällen verfügt die APP möglicherweise über mehrere Instanzen eines Authentifizierungs Handlers.</span><span class="sxs-lookup"><span data-stu-id="4aa2e-106">In some cases, the app may have multiple instances of an authentication handler.</span></span> <span data-ttu-id="4aa2e-107">Beispielsweise zwei Cookie-Handler, bei denen eine eine grundlegende Identität enthält und eine erstellt wird, wenn eine mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) ausgelöst wurde.</span><span class="sxs-lookup"><span data-stu-id="4aa2e-107">For example, two cookie handlers where one contains a basic identity and one is created when a multi-factor authentication (MFA) has been triggered.</span></span> <span data-ttu-id="4aa2e-108">MFA kann ausgelöst werden, da der Benutzer einen Vorgang angefordert hat, der zusätzliche Sicherheit erfordert.</span><span class="sxs-lookup"><span data-stu-id="4aa2e-108">MFA may be triggered because the user requested an operation that requires extra security.</span></span>

<span data-ttu-id="4aa2e-109">Ein Authentifizierungsschema wird benannt, wenn der Authentifizierungsdienst während der Authentifizierung konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="4aa2e-109">An authentication scheme is named when the authentication service is configured during authentication.</span></span> <span data-ttu-id="4aa2e-110">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="4aa2e-110">For example:</span></span>

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

<span data-ttu-id="4aa2e-111">Im vorangehenden Code wurden zwei Authentifizierungs Handler hinzugefügt: eine für Cookies und eine für Bearer.</span><span class="sxs-lookup"><span data-stu-id="4aa2e-111">In the preceding code, two authentication handlers have been added: one for cookies and one for bearer.</span></span>

>[!NOTE]
><span data-ttu-id="4aa2e-112">Wenn Sie das Standardschema angeben, wird die `HttpContext.User`-Eigenschaft auf diese Identität festgelegt.</span><span class="sxs-lookup"><span data-stu-id="4aa2e-112">Specifying the default scheme results in the `HttpContext.User` property being set to that identity.</span></span> <span data-ttu-id="4aa2e-113">Wenn dieses Verhalten nicht erwünscht ist, deaktivieren Sie es, indem Sie die Parameter lose Form `AddAuthentication`aufrufen.</span><span class="sxs-lookup"><span data-stu-id="4aa2e-113">If that behavior isn't desired, disable it by invoking the parameterless form of `AddAuthentication`.</span></span>

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a><span data-ttu-id="4aa2e-114">Auswählen des Schemas mit dem Attribut "autorisieren"</span><span class="sxs-lookup"><span data-stu-id="4aa2e-114">Selecting the scheme with the Authorize attribute</span></span>

<span data-ttu-id="4aa2e-115">Zum Zeitpunkt der Autorisierung gibt die APP den zu verwendenden Handler an.</span><span class="sxs-lookup"><span data-stu-id="4aa2e-115">At the point of authorization, the app indicates the handler to be used.</span></span> <span data-ttu-id="4aa2e-116">Wählen Sie den Handler aus, mit dem die APP autorisiert werden soll, indem Sie eine durch Trennzeichen getrennte Liste von Authentifizierungs Schemas an `[Authorize]`übergeben.</span><span class="sxs-lookup"><span data-stu-id="4aa2e-116">Select the handler with which the app will authorize by passing a comma-delimited list of authentication schemes to `[Authorize]`.</span></span> <span data-ttu-id="4aa2e-117">Das `[Authorize]`-Attribut gibt das Authentifizierungsschema bzw. die zu verwendenden Schemas an, unabhängig davon, ob ein Standard konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="4aa2e-117">The `[Authorize]` attribute specifies the authentication scheme or schemes to use regardless of whether a default is configured.</span></span> <span data-ttu-id="4aa2e-118">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="4aa2e-118">For example:</span></span>

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

<span data-ttu-id="4aa2e-119">Im vorherigen Beispiel werden sowohl der Cookie-als auch der bearerhandler ausgeführt, und Sie haben die Möglichkeit, eine Identität für den aktuellen Benutzer zu erstellen und anzufügen.</span><span class="sxs-lookup"><span data-stu-id="4aa2e-119">In the preceding example, both the cookie and bearer handlers run and have a chance to create and append an identity for the current user.</span></span> <span data-ttu-id="4aa2e-120">Wenn Sie nur ein einzelnes Schema angeben, wird der entsprechende Handler ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="4aa2e-120">By specifying a single scheme only, the corresponding handler runs.</span></span>

```csharp
[Authorize(AuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

<span data-ttu-id="4aa2e-121">Im vorangehenden Code wird nur der Handler mit dem Schema "Träger" ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="4aa2e-121">In the preceding code, only the handler with the "Bearer" scheme runs.</span></span> <span data-ttu-id="4aa2e-122">Cookiebasierte Identitäten werden ignoriert.</span><span class="sxs-lookup"><span data-stu-id="4aa2e-122">Any cookie-based identities are ignored.</span></span>

## <a name="selecting-the-scheme-with-policies"></a><span data-ttu-id="4aa2e-123">Auswählen des Schemas mit Richtlinien</span><span class="sxs-lookup"><span data-stu-id="4aa2e-123">Selecting the scheme with policies</span></span>

<span data-ttu-id="4aa2e-124">Wenn Sie die gewünschten Schemas in der [Richtlinie](xref:security/authorization/policies)angeben möchten, können Sie beim Hinzufügen Ihrer Richtlinie die `AuthenticationSchemes` Sammlung festlegen:</span><span class="sxs-lookup"><span data-stu-id="4aa2e-124">If you prefer to specify the desired schemes in [policy](xref:security/authorization/policies), you can set the `AuthenticationSchemes` collection when adding your policy:</span></span>

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

<span data-ttu-id="4aa2e-125">Im vorherigen Beispiel wird die Richtlinie "Over18" nur für die Identität ausgeführt, die vom Handler "Träger" erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="4aa2e-125">In the preceding example, the "Over18" policy only runs against the identity created by the "Bearer" handler.</span></span> <span data-ttu-id="4aa2e-126">Verwenden Sie die Richtlinie, indem Sie die `Policy`-Eigenschaft des `[Authorize]` Attributs festlegen:</span><span class="sxs-lookup"><span data-stu-id="4aa2e-126">Use the policy by setting the `[Authorize]` attribute's `Policy` property:</span></span>

```csharp
[Authorize(Policy = "Over18")]
public class RegistrationController : Controller
```

::: moniker range=">= aspnetcore-2.0"

## <a name="use-multiple-authentication-schemes"></a><span data-ttu-id="4aa2e-127">Verwenden mehrerer Authentifizierungs Schemas</span><span class="sxs-lookup"><span data-stu-id="4aa2e-127">Use multiple authentication schemes</span></span>

<span data-ttu-id="4aa2e-128">Einige apps müssen möglicherweise mehrere Arten der Authentifizierung unterstützen.</span><span class="sxs-lookup"><span data-stu-id="4aa2e-128">Some apps may need to support multiple types of authentication.</span></span> <span data-ttu-id="4aa2e-129">Beispielsweise kann Ihre App Benutzer von Azure Active Directory und einer Benutzerdatenbank authentifizieren.</span><span class="sxs-lookup"><span data-stu-id="4aa2e-129">For example, your app might authenticate users from Azure Active Directory and from a users database.</span></span> <span data-ttu-id="4aa2e-130">Ein weiteres Beispiel ist eine APP, die Benutzer sowohl aus Active Directory-Verbunddienste (AD FS) als auch aus Azure Active Directory B2C authentifiziert.</span><span class="sxs-lookup"><span data-stu-id="4aa2e-130">Another example is an app that authenticates users from both Active Directory Federation Services and Azure Active Directory B2C.</span></span> <span data-ttu-id="4aa2e-131">In diesem Fall sollte die APP ein JWT-bearertoken von mehreren Ausstellern akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="4aa2e-131">In this case, the app should accept a JWT bearer token from several issuers.</span></span>

<span data-ttu-id="4aa2e-132">Fügen Sie alle Authentifizierungs Schemas hinzu, die angenommen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="4aa2e-132">Add all authentication schemes you'd like to accept.</span></span> <span data-ttu-id="4aa2e-133">Beispielsweise werden mit dem folgenden Code in `Startup.ConfigureServices` zwei JWT-Träger Authentifizierungs Schemas für verschiedene Aussteller hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="4aa2e-133">For example, the following code in `Startup.ConfigureServices` adds two JWT bearer authentication schemes with different issuers:</span></span>

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
> <span data-ttu-id="4aa2e-134">Nur eine JWT-Träger Authentifizierung wird mit dem standardmäßigen Authentifizierungsschema `JwtBearerDefaults.AuthenticationScheme`registriert.</span><span class="sxs-lookup"><span data-stu-id="4aa2e-134">Only one JWT bearer authentication is registered with the default authentication scheme `JwtBearerDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="4aa2e-135">Die zusätzliche Authentifizierung muss mit einem eindeutigen Authentifizierungsschema registriert werden.</span><span class="sxs-lookup"><span data-stu-id="4aa2e-135">Additional authentication has to be registered with a unique authentication scheme.</span></span>

<span data-ttu-id="4aa2e-136">Der nächste Schritt besteht darin, die Standard Autorisierungs Richtlinie so zu aktualisieren, dass beide Authentifizierungs Schemas akzeptiert werden.</span><span class="sxs-lookup"><span data-stu-id="4aa2e-136">The next step is to update the default authorization policy to accept both authentication schemes.</span></span> <span data-ttu-id="4aa2e-137">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="4aa2e-137">For example:</span></span>

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

<span data-ttu-id="4aa2e-138">Wenn die Standard Autorisierungs Richtlinie überschrieben wird, ist es möglich, das `[Authorize]`-Attribut in Controllern zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="4aa2e-138">As the default authorization policy is overridden, it's possible to use the `[Authorize]` attribute in controllers.</span></span> <span data-ttu-id="4aa2e-139">Der Controller akzeptiert dann Anforderungen mit JWT, die vom ersten oder zweiten Aussteller ausgegeben wurden.</span><span class="sxs-lookup"><span data-stu-id="4aa2e-139">The controller then accepts requests with JWT issued by the first or second issuer.</span></span>

::: moniker-end

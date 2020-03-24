---
title: Sichern eines ASP.net Core Blazor gehosteten Webassembly-App mit Identity Server
author: guardrex
description: So erstellen Sie eine neue Blazor gehostete App mit Authentifizierung aus Visual Studio, die ein [identityserver](https://identityserver.io/) -Backend verwendet
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: 6c7942a827d88a620e6f295af3f523c23f4b3890
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/24/2020
ms.locfileid: "80219050"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="303ca-103">Sichern eines ASP.net Core Blazor gehosteten Webassembly-App mit Identity Server</span><span class="sxs-lookup"><span data-stu-id="303ca-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="303ca-104">Von [Javier calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="303ca-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="303ca-105">So erstellen Sie eine neue Blazor gehostete app in Visual Studio, die [identityserver](https://identityserver.io/) verwendet, um Benutzer und API-Aufrufe zu authentifizieren:</span><span class="sxs-lookup"><span data-stu-id="303ca-105">To create a new Blazor hosted app in Visual Studio that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls:</span></span>

1. <span data-ttu-id="303ca-106">Verwenden Sie Visual Studio, um eine neue **Blazor Webassembly** -APP zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="303ca-106">Use Visual Studio to create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="303ca-107">Weitere Informationen finden Sie unter <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="303ca-107">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="303ca-108">Wählen Sie im Dialogfeld **neue Blazor-app erstellen** im Abschnitt **Authentifizierung** die Option **ändern** aus.</span><span class="sxs-lookup"><span data-stu-id="303ca-108">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="303ca-109">Wählen Sie **einzelne Benutzerkonten** und dann **OK**aus.</span><span class="sxs-lookup"><span data-stu-id="303ca-109">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="303ca-110">Aktivieren Sie das Kontrollkästchen **ASP.net Core gehostet** im Abschnitt **erweitert** .</span><span class="sxs-lookup"><span data-stu-id="303ca-110">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="303ca-111">Wählen Sie die Schaltfläche **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="303ca-111">Select the **Create** button.</span></span>

<span data-ttu-id="303ca-112">Führen Sie den folgenden Befehl aus, um die app in einer Befehlsshell zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="303ca-112">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="303ca-113">Um den Ausgabe Speicherort anzugeben, der einen Projektordner erstellt, wenn er nicht vorhanden ist, fügen Sie die Output-Option im Befehl mit einem Pfad (z. b. `-o BlazorSample`) ein.</span><span class="sxs-lookup"><span data-stu-id="303ca-113">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="303ca-114">Der Name des Ordners wird auch Teil des Projekt namens.</span><span class="sxs-lookup"><span data-stu-id="303ca-114">The folder name also becomes part of the project's name.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="303ca-115">Server-App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="303ca-115">Server app configuration</span></span>

<span data-ttu-id="303ca-116">In den folgenden Abschnitten werden Ergänzungen zum-Projekt beschrieben, wenn die Authentifizierungs Unterstützung enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="303ca-116">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="303ca-117">Startklasse</span><span class="sxs-lookup"><span data-stu-id="303ca-117">Startup class</span></span>

<span data-ttu-id="303ca-118">Die `Startup`-Klasse verfügt über folgende Ergänzungen:</span><span class="sxs-lookup"><span data-stu-id="303ca-118">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="303ca-119">In `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="303ca-119">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="303ca-120">Identität mit der Standardbenutzer Oberfläche:</span><span class="sxs-lookup"><span data-stu-id="303ca-120">Identity with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="303ca-121">Identityserver mit einer zusätzlichen <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>-Hilfsmethode, mit der einige standardmäßige ASP.net Core Konventionen auf identityserver eingerichtet werden:</span><span class="sxs-lookup"><span data-stu-id="303ca-121">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="303ca-122">Authentifizierung mit einer zusätzlichen <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>-Hilfsmethode, die die APP so konfiguriert, dass die von identityserver erstellten JWT-Token überprüft werden:</span><span class="sxs-lookup"><span data-stu-id="303ca-122">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="303ca-123">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="303ca-123">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="303ca-124">Die Authentifizierungs Middleware, die für die Validierung der Anforderungs Anmelde Informationen und das Festlegen des Benutzers im Anforderungs Kontext zuständig ist:</span><span class="sxs-lookup"><span data-stu-id="303ca-124">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="303ca-125">Die identityserver-Middleware, die die Endpunkte der Open ID Connect (oidc) verfügbar macht:</span><span class="sxs-lookup"><span data-stu-id="303ca-125">The IdentityServer middleware that exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="303ca-126">Addapiauthorization</span><span class="sxs-lookup"><span data-stu-id="303ca-126">AddApiAuthorization</span></span>

<span data-ttu-id="303ca-127">Die <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>-Hilfsmethode konfiguriert [identityserver](https://identityserver.io/) für ASP.net Core Szenarien.</span><span class="sxs-lookup"><span data-stu-id="303ca-127">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="303ca-128">Identityserver ist ein leistungsfähiges und erweiterbares Framework für die Behandlung von App-Sicherheitsbedenken.</span><span class="sxs-lookup"><span data-stu-id="303ca-128">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="303ca-129">Identityserver macht unnötige Komplexität für die häufigsten Szenarien verfügbar.</span><span class="sxs-lookup"><span data-stu-id="303ca-129">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="303ca-130">Folglich wird ein Satz von Konventionen und Konfigurationsoptionen bereitgestellt, die einen guten Ausgangspunkt in Erwägung gezogen.</span><span class="sxs-lookup"><span data-stu-id="303ca-130">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="303ca-131">Nachdem sich Ihre Authentifizierung geändert hat, ist die volle Leistungsfähigkeit von identityserver weiterhin verfügbar, um die Authentifizierung an die Anforderungen einer APP anzupassen.</span><span class="sxs-lookup"><span data-stu-id="303ca-131">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="303ca-132">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="303ca-132">AddIdentityServerJwt</span></span>

<span data-ttu-id="303ca-133">Die <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>-Hilfsmethode konfiguriert ein Richtlinien Schema für die APP als Standard Authentifizierungs Handler.</span><span class="sxs-lookup"><span data-stu-id="303ca-133">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="303ca-134">Die Richtlinie ist so konfiguriert, dass die Identität alle Anforderungen verarbeitet, die an einen Unterpfad im Identitäts-URL-Bereich `/Identity`weitergeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="303ca-134">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="303ca-135">Die <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> behandelt alle anderen Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="303ca-135">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="303ca-136">Außerdem gilt diese Methode:</span><span class="sxs-lookup"><span data-stu-id="303ca-136">Additionally, this method:</span></span>

* <span data-ttu-id="303ca-137">Registriert eine `{APPLICATION NAME}API` API-Ressource bei identityserver mit einem Standardbereich `{APPLICATION NAME}API`.</span><span class="sxs-lookup"><span data-stu-id="303ca-137">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="303ca-138">Konfiguriert die JWT-bearertoken-Middleware zum Überprüfen von Token, die von identityserver für die APP ausgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="303ca-138">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="303ca-139">Weatherforecastcontroller</span><span class="sxs-lookup"><span data-stu-id="303ca-139">WeatherForecastController</span></span>

<span data-ttu-id="303ca-140">Im `WeatherForecastController` (*Controllers/weatherforecastcontroller. cs*) wird das [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) -Attribut auf die-Klasse angewendet.</span><span class="sxs-lookup"><span data-stu-id="303ca-140">In the `WeatherForecastController` (*Controllers/WeatherForecastController.cs*), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="303ca-141">Das-Attribut gibt an, dass der Benutzer auf der Basis der Standard Richtlinie autorisiert werden muss, um auf die Ressource zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="303ca-141">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="303ca-142">Die standardmäßige Autorisierungs Richtlinie ist so konfiguriert, dass das Standard Authentifizierungsschema verwendet wird, das durch <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> auf das zuvor erwähnte Richtlinien Schema festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="303ca-142">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> to the policy scheme that was mentioned earlier.</span></span> <span data-ttu-id="303ca-143">Die-Hilfsmethode konfiguriert <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> als Standard Handler für Anforderungen an die app.</span><span class="sxs-lookup"><span data-stu-id="303ca-143">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="303ca-144">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="303ca-144">ApplicationDbContext</span></span>

<span data-ttu-id="303ca-145">Im `ApplicationDbContext` (*Data/applicationdbcontext. cs*) wird die gleiche <xref:Microsoft.EntityFrameworkCore.DbContext> in der Identität verwendet, mit der Ausnahme, dass Sie <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> erweitert, um das Schema für identityserver einzuschließen.</span><span class="sxs-lookup"><span data-stu-id="303ca-145">In the `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), the same <xref:Microsoft.EntityFrameworkCore.DbContext> is used in Identity with the exception that it extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="303ca-146"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> wird von <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="303ca-146"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="303ca-147">Um die vollständige Kontrolle über das Datenbankschema zu erhalten, erben Sie von einer der verfügbaren Identitäts <xref:Microsoft.EntityFrameworkCore.DbContext> Klassen, und konfigurieren Sie den Kontext so, dass das Identitäts Schema durch Aufrufen von `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in der `OnModelCreating`-Methode eingeschlossen wird</span><span class="sxs-lookup"><span data-stu-id="303ca-147">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="303ca-148">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="303ca-148">OidcConfigurationController</span></span>

<span data-ttu-id="303ca-149">Im `OidcConfigurationController` (*Controllers/oidcconfigurationcontroller. cs*) wird der Client Endpunkt bereitgestellt, um oidc-Parameter bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="303ca-149">In the `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="303ca-150">App-Einstellungsdateien</span><span class="sxs-lookup"><span data-stu-id="303ca-150">App settings files</span></span>

<span data-ttu-id="303ca-151">In der APP-Einstellungsdatei (*appSettings. JSON*) im Projektstamm wird im Abschnitt `IdentityServer` die Liste der konfigurierten Clients beschrieben.</span><span class="sxs-lookup"><span data-stu-id="303ca-151">In the app settings file (*appsettings.json*) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="303ca-152">Im folgenden Beispiel gibt es einen einzelnen Client.</span><span class="sxs-lookup"><span data-stu-id="303ca-152">In the following example, there's a single client.</span></span> <span data-ttu-id="303ca-153">Der Client Name entspricht dem APP-Namen und wird dem OAuth-`ClientId` Parameter gemäß der Konvention zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="303ca-153">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="303ca-154">Das Profil gibt den App-Typ an, der konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="303ca-154">The profile indicates the app type being configured.</span></span> <span data-ttu-id="303ca-155">Das Profil wird intern verwendet, um Konventionen zu steuern, die den Konfigurationsprozess für den Server vereinfachen.</span><span class="sxs-lookup"><span data-stu-id="303ca-155">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "BlazorApplicationWithAuthentication.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="303ca-156">In der APP-Einstellungsdatei der Entwicklungsumgebung (*appSettings). Development. JSON*) im Projektstamm wird im `IdentityServer` Abschnitt der zum Signieren von Token verwendete Schlüssel beschrieben.</span><span class="sxs-lookup"><span data-stu-id="303ca-156">In the Development environment app settings file (*appsettings.Development.json*) at the project root, the `IdentityServer` section describes the key used to sign tokens.</span></span> <!-- When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section. -->

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="303ca-157">Client-App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="303ca-157">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="303ca-158">Authentifizierungs Paket</span><span class="sxs-lookup"><span data-stu-id="303ca-158">Authentication package</span></span>

<span data-ttu-id="303ca-159">Wenn eine APP für die Verwendung einzelner Benutzerkonten (`Individual`) erstellt wird, empfängt die APP automatisch einen Paket Verweis für das `Microsoft.AspNetCore.Components.WebAssembly.Authentication` Paket in der Projektdatei der app.</span><span class="sxs-lookup"><span data-stu-id="303ca-159">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="303ca-160">Das Paket enthält eine Reihe von primitiven, die der APP helfen, Benutzer zu authentifizieren und Token abzurufen, um geschützte APIs aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="303ca-160">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="303ca-161">Wenn Sie eine Authentifizierung zu einer APP hinzufügen, fügen Sie das Paket manuell zur Projektdatei der APP hinzu:</span><span class="sxs-lookup"><span data-stu-id="303ca-161">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="303ca-162">Ersetzen Sie `{VERSION}` im vorangehenden Paket Verweis durch die Version des `Microsoft.AspNetCore.Blazor.Templates` Pakets, das im <xref:blazor/get-started> Artikel angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="303ca-162">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="303ca-163">API-Autorisierungs Unterstützung</span><span class="sxs-lookup"><span data-stu-id="303ca-163">API authorization support</span></span>

<span data-ttu-id="303ca-164">Die Unterstützung für das Authentifizieren von Benutzern wird durch die im `Microsoft.AspNetCore.Components.WebAssembly.Authentication` Paket bereitgestellte Erweiterungsmethode an den Dienst Container angeschlossen.</span><span class="sxs-lookup"><span data-stu-id="303ca-164">The support for authenticating users is plugged into the service container by the extension method provided inside the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="303ca-165">Diese Methode richtet alle Dienste ein, die erforderlich sind, damit die APP mit dem vorhandenen Autorisierungssystem interagiert.</span><span class="sxs-lookup"><span data-stu-id="303ca-165">This method sets up all the services needed for the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="303ca-166">Standardmäßig wird die Konfiguration für die APP gemäß der Konvention aus `_configuration/{client-id}`geladen.</span><span class="sxs-lookup"><span data-stu-id="303ca-166">By default, it loads the configuration for the app by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="303ca-167">Gemäß der Konvention wird die Client-ID auf den Assemblynamen der APP festgelegt.</span><span class="sxs-lookup"><span data-stu-id="303ca-167">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="303ca-168">Diese URL kann geändert werden, um auf einen separaten Endpunkt zu verweisen, indem Sie die-Überladung mit-Optionen aufrufen.</span><span class="sxs-lookup"><span data-stu-id="303ca-168">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="index-page"></a><span data-ttu-id="303ca-169">Indexseite</span><span class="sxs-lookup"><span data-stu-id="303ca-169">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="303ca-170">App-Komponente</span><span class="sxs-lookup"><span data-stu-id="303ca-170">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="303ca-171">Redirecttologin-Komponente</span><span class="sxs-lookup"><span data-stu-id="303ca-171">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="303ca-172">Logindisplay-Komponente</span><span class="sxs-lookup"><span data-stu-id="303ca-172">LoginDisplay component</span></span>

<span data-ttu-id="303ca-173">Die `LoginDisplay` Komponente (*Shared/logindisplay. Razor*) wird in der `MainLayout` Komponente (*Shared/MainLayout. Razor*) gerendert und verwaltet die folgenden Verhaltensweisen:</span><span class="sxs-lookup"><span data-stu-id="303ca-173">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="303ca-174">Für authentifizierte Benutzer:</span><span class="sxs-lookup"><span data-stu-id="303ca-174">For authenticated users:</span></span>
  * <span data-ttu-id="303ca-175">Zeigt den aktuellen Benutzernamen an.</span><span class="sxs-lookup"><span data-stu-id="303ca-175">Displays the current user name.</span></span>
  * <span data-ttu-id="303ca-176">Gibt einen Link zur Benutzerprofil Seite in ASP.net Core Identität an.</span><span class="sxs-lookup"><span data-stu-id="303ca-176">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="303ca-177">Bietet eine Schaltfläche, um sich von der APP abzumelden.</span><span class="sxs-lookup"><span data-stu-id="303ca-177">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="303ca-178">Für anonyme Benutzer:</span><span class="sxs-lookup"><span data-stu-id="303ca-178">For anonymous users:</span></span>
  * <span data-ttu-id="303ca-179">Bietet die Option zum Registrieren von.</span><span class="sxs-lookup"><span data-stu-id="303ca-179">Offers the option to register.</span></span>
  * <span data-ttu-id="303ca-180">Bietet die Option zum Anmelden.</span><span class="sxs-lookup"><span data-stu-id="303ca-180">Offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

### <a name="authentication-component"></a><span data-ttu-id="303ca-181">Authentifizierungs Komponente</span><span class="sxs-lookup"><span data-stu-id="303ca-181">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="303ca-182">Fetchdata-Komponente</span><span class="sxs-lookup"><span data-stu-id="303ca-182">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="303ca-183">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="303ca-183">Run the app</span></span>

<span data-ttu-id="303ca-184">Führen Sie die APP aus dem Server Projekt aus.</span><span class="sxs-lookup"><span data-stu-id="303ca-184">Run the app from the Server project.</span></span> <span data-ttu-id="303ca-185">Wenn Sie Visual Studio verwenden, wählen Sie das Server Projekt in **Projektmappen-Explorer** aus, und klicken Sie auf der Symbolleiste auf die Schaltfläche **Ausführen** , oder starten Sie die APP im Menü **Debuggen**</span><span class="sxs-lookup"><span data-stu-id="303ca-185">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

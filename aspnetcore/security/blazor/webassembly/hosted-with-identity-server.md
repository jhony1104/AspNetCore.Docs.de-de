---
title: Sichern einer ASP.net Core Blazor Webassembly-gehosteten App mit Identity Server
author: guardrex
description: So erstellen Sie eine Blazor neue gehostete App mit Authentifizierung aus Visual Studio, die ein [identityserver](https://identityserver.io/) -Backend verwendet
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: ffdcd30ae9ce5350113569a500e99cf8db82ad65
ms.sourcegitcommit: 4f91da9ce4543b39dba5e8920a9500d3ce959746
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82138604"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="c8946-103">Sichern einer ASP.net Core Blazor Webassembly-gehosteten App mit Identity Server</span><span class="sxs-lookup"><span data-stu-id="c8946-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="c8946-104">Von [Javier calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c8946-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="c8946-105">So erstellen Sie eine Blazor neue gehostete app in Visual Studio, die [identityserver](https://identityserver.io/) verwendet, um Benutzer und API-Aufrufe zu authentifizieren:</span><span class="sxs-lookup"><span data-stu-id="c8946-105">To create a new Blazor hosted app in Visual Studio that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls:</span></span>

1. <span data-ttu-id="c8946-106">Verwenden Sie Visual Studio, um eine neue \*\* Blazor Webassembly\*\* -APP zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="c8946-106">Use Visual Studio to create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="c8946-107">Weitere Informationen finden Sie unter <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="c8946-107">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="c8946-108">Wählen Sie im Dialogfeld **neue Blazor app erstellen** im Abschnitt **Authentifizierung** die Option **ändern** aus.</span><span class="sxs-lookup"><span data-stu-id="c8946-108">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="c8946-109">Wählen Sie **einzelne Benutzerkonten** und dann **OK**aus.</span><span class="sxs-lookup"><span data-stu-id="c8946-109">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="c8946-110">Aktivieren Sie das Kontrollkästchen **ASP.net Core gehostet** im Abschnitt **erweitert** .</span><span class="sxs-lookup"><span data-stu-id="c8946-110">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="c8946-111">Wählen Sie die Schaltfläche **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="c8946-111">Select the **Create** button.</span></span>

<span data-ttu-id="c8946-112">Führen Sie den folgenden Befehl aus, um die app in einer Befehlsshell zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="c8946-112">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="c8946-113">Um den Ausgabe Speicherort anzugeben, der einen Projektordner erstellt, wenn er nicht vorhanden ist, schließen Sie die Output-Option in den Befehl mit einem `-o BlazorSample`Pfad (z. b.) ein.</span><span class="sxs-lookup"><span data-stu-id="c8946-113">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="c8946-114">Der Name des Ordners wird auch Teil des Projekt namens.</span><span class="sxs-lookup"><span data-stu-id="c8946-114">The folder name also becomes part of the project's name.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="c8946-115">Server-App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="c8946-115">Server app configuration</span></span>

<span data-ttu-id="c8946-116">In den folgenden Abschnitten werden Ergänzungen zum-Projekt beschrieben, wenn die Authentifizierungs Unterstützung enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="c8946-116">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="c8946-117">Startklasse</span><span class="sxs-lookup"><span data-stu-id="c8946-117">Startup class</span></span>

<span data-ttu-id="c8946-118">Die `Startup` -Klasse verfügt über die folgenden Ergänzungen:</span><span class="sxs-lookup"><span data-stu-id="c8946-118">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="c8946-119">In `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="c8946-119">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="c8946-120">Identität:</span><span class="sxs-lookup"><span data-stu-id="c8946-120">Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="c8946-121">Identityserver mit einer zusätzlichen <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> Hilfsmethode, mit der einige Standard ASP.net Core Konventionen auf identityserver eingerichtet werden:</span><span class="sxs-lookup"><span data-stu-id="c8946-121">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="c8946-122">Authentifizierung mit einer zusätzlichen <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> Hilfsmethode, die die APP für die Validierung der von identityserver erstellten JWT-Token konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="c8946-122">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="c8946-123">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="c8946-123">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="c8946-124">Die Authentifizierungs Middleware, die für die Validierung der Anforderungs Anmelde Informationen und das Festlegen des Benutzers im Anforderungs Kontext zuständig ist:</span><span class="sxs-lookup"><span data-stu-id="c8946-124">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="c8946-125">Die identityserver-Middleware, die die Endpunkte der Open ID Connect (oidc) verfügbar macht:</span><span class="sxs-lookup"><span data-stu-id="c8946-125">The IdentityServer middleware that exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="c8946-126">Authentifizierungs-und Autorisierungs Middleware:</span><span class="sxs-lookup"><span data-stu-id="c8946-126">Authentication and Authorization Middleware:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="c8946-127">Addapiauthorization</span><span class="sxs-lookup"><span data-stu-id="c8946-127">AddApiAuthorization</span></span>

<span data-ttu-id="c8946-128">Die <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> -Hilfsmethode konfiguriert [identityserver](https://identityserver.io/) für ASP.net Core Szenarien.</span><span class="sxs-lookup"><span data-stu-id="c8946-128">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="c8946-129">Identityserver ist ein leistungsfähiges und erweiterbares Framework für die Behandlung von App-Sicherheitsbedenken.</span><span class="sxs-lookup"><span data-stu-id="c8946-129">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="c8946-130">Identityserver macht unnötige Komplexität für die häufigsten Szenarien verfügbar.</span><span class="sxs-lookup"><span data-stu-id="c8946-130">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="c8946-131">Folglich wird ein Satz von Konventionen und Konfigurationsoptionen bereitgestellt, die einen guten Ausgangspunkt in Erwägung gezogen.</span><span class="sxs-lookup"><span data-stu-id="c8946-131">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="c8946-132">Nachdem sich Ihre Authentifizierung geändert hat, ist die volle Leistungsfähigkeit von identityserver weiterhin verfügbar, um die Authentifizierung an die Anforderungen einer APP anzupassen.</span><span class="sxs-lookup"><span data-stu-id="c8946-132">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="c8946-133">Addidentityserverjwt</span><span class="sxs-lookup"><span data-stu-id="c8946-133">AddIdentityServerJwt</span></span>

<span data-ttu-id="c8946-134">Die <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> -Hilfsmethode konfiguriert ein Richtlinien Schema für die APP als Standard Authentifizierungs Handler.</span><span class="sxs-lookup"><span data-stu-id="c8946-134">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="c8946-135">Die Richtlinie ist so konfiguriert, dass die Identität alle Anforderungen verarbeitet, die an einen untergeordneten Pfad im `/Identity`Identitäts-URL-Bereich weitergeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="c8946-135">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="c8946-136">Der <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> verarbeitet alle anderen Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="c8946-136">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="c8946-137">Außerdem gilt diese Methode:</span><span class="sxs-lookup"><span data-stu-id="c8946-137">Additionally, this method:</span></span>

* <span data-ttu-id="c8946-138">Registriert eine `{APPLICATION NAME}API` API-Ressource bei identityserver mit einem Standardbereich `{APPLICATION NAME}API`von.</span><span class="sxs-lookup"><span data-stu-id="c8946-138">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="c8946-139">Konfiguriert die JWT-bearertoken-Middleware zum Überprüfen von Token, die von identityserver für die APP ausgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="c8946-139">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="c8946-140">Weatherforecastcontroller</span><span class="sxs-lookup"><span data-stu-id="c8946-140">WeatherForecastController</span></span>

<span data-ttu-id="c8946-141">In `WeatherForecastController` (*Controllers/weatherforecastcontroller. cs*) wird das [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) -Attribut auf die-Klasse angewendet.</span><span class="sxs-lookup"><span data-stu-id="c8946-141">In the `WeatherForecastController` (*Controllers/WeatherForecastController.cs*), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="c8946-142">Das-Attribut gibt an, dass der Benutzer auf der Basis der Standard Richtlinie autorisiert werden muss, um auf die Ressource zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="c8946-142">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="c8946-143">Die standardmäßige Autorisierungs Richtlinie ist so konfiguriert, dass das Standard Authentifizierungsschema verwendet wird, <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> das durch das zuvor erwähnte Richtlinien Schema festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="c8946-143">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> to the policy scheme that was mentioned earlier.</span></span> <span data-ttu-id="c8946-144">Die-Hilfsmethode konfiguriert <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> als Standard Handler für Anforderungen an die app.</span><span class="sxs-lookup"><span data-stu-id="c8946-144">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="c8946-145">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="c8946-145">ApplicationDbContext</span></span>

<span data-ttu-id="c8946-146">In der `ApplicationDbContext` (*Data/applicationdbcontext. cs*) wird dasselbe <xref:Microsoft.EntityFrameworkCore.DbContext> in der Identität verwendet, mit der Ausnahme, dass Sie <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> erweitert wird, um das Schema für identityserver einzuschließen.</span><span class="sxs-lookup"><span data-stu-id="c8946-146">In the `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), the same <xref:Microsoft.EntityFrameworkCore.DbContext> is used in Identity with the exception that it extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="c8946-147"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> wird von <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="c8946-147"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="c8946-148">Um die vollständige Kontrolle über das Datenbankschema zu erhalten, erben Sie von <xref:Microsoft.EntityFrameworkCore.DbContext> einer der verfügbaren Identitäts Klassen und konfigurieren den Kontext so, `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` dass das `OnModelCreating` Identitäts Schema durch Aufrufen von in der-Methode eingeschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="c8946-148">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="c8946-149">Oidcconfigurationcontroller</span><span class="sxs-lookup"><span data-stu-id="c8946-149">OidcConfigurationController</span></span>

<span data-ttu-id="c8946-150">In der `OidcConfigurationController` (*Controllers/oidcconfigurationcontroller. cs*) wird der Client Endpunkt bereitgestellt, um oidc-Parameter bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="c8946-150">In the `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="c8946-151">App-Einstellungsdateien</span><span class="sxs-lookup"><span data-stu-id="c8946-151">App settings files</span></span>

<span data-ttu-id="c8946-152">In der APP-Einstellungsdatei (*appSettings. JSON*) im Projektstamm Abschnitt wird `IdentityServer` in diesem Abschnitt die Liste der konfigurierten Clients beschrieben.</span><span class="sxs-lookup"><span data-stu-id="c8946-152">In the app settings file (*appsettings.json*) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="c8946-153">Im folgenden Beispiel gibt es einen einzelnen Client.</span><span class="sxs-lookup"><span data-stu-id="c8946-153">In the following example, there's a single client.</span></span> <span data-ttu-id="c8946-154">Der Client Name entspricht dem APP-Namen und wird dem OAuth `ClientId` -Parameter gemäß der Konvention zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="c8946-154">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="c8946-155">Das Profil gibt den App-Typ an, der konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="c8946-155">The profile indicates the app type being configured.</span></span> <span data-ttu-id="c8946-156">Das Profil wird intern verwendet, um Konventionen zu steuern, die den Konfigurationsprozess für den Server vereinfachen.</span><span class="sxs-lookup"><span data-stu-id="c8946-156">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="c8946-157">Client-App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="c8946-157">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="c8946-158">Authentifizierungs Paket</span><span class="sxs-lookup"><span data-stu-id="c8946-158">Authentication package</span></span>

<span data-ttu-id="c8946-159">Wenn eine APP für die Verwendung einzelner Benutzerkonten (`Individual`) erstellt wird, erhält die APP automatisch einen Paket Verweis für `Microsoft.AspNetCore.Components.WebAssembly.Authentication` das Paket in der Projektdatei der app.</span><span class="sxs-lookup"><span data-stu-id="c8946-159">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="c8946-160">Das Paket enthält eine Reihe von primitiven, die der APP helfen, Benutzer zu authentifizieren und Token abzurufen, um geschützte APIs aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="c8946-160">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="c8946-161">Wenn Sie eine Authentifizierung zu einer APP hinzufügen, fügen Sie das Paket manuell zur Projektdatei der APP hinzu:</span><span class="sxs-lookup"><span data-stu-id="c8946-161">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="c8946-162">Ersetzen `{VERSION}` Sie im vorherigen Paket Verweis durch die Version des `Microsoft.AspNetCore.Blazor.Templates` Pakets, das im <xref:blazor/get-started> Artikel angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="c8946-162">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="c8946-163">API-Autorisierungs Unterstützung</span><span class="sxs-lookup"><span data-stu-id="c8946-163">API authorization support</span></span>

<span data-ttu-id="c8946-164">Die Unterstützung für das Authentifizieren von Benutzern wird durch die im `Microsoft.AspNetCore.Components.WebAssembly.Authentication` Paket bereitgestellte Erweiterungsmethode an den Dienst Container angeschlossen.</span><span class="sxs-lookup"><span data-stu-id="c8946-164">The support for authenticating users is plugged into the service container by the extension method provided inside the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="c8946-165">Diese Methode richtet alle Dienste ein, die erforderlich sind, damit die APP mit dem vorhandenen Autorisierungssystem interagiert.</span><span class="sxs-lookup"><span data-stu-id="c8946-165">This method sets up all the services needed for the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="c8946-166">Standardmäßig wird die Konfiguration für die APP gemäß der Konvention von `_configuration/{client-id}`geladen.</span><span class="sxs-lookup"><span data-stu-id="c8946-166">By default, it loads the configuration for the app by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="c8946-167">Gemäß der Konvention wird die Client-ID auf den Assemblynamen der APP festgelegt.</span><span class="sxs-lookup"><span data-stu-id="c8946-167">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="c8946-168">Diese URL kann geändert werden, um auf einen separaten Endpunkt zu verweisen, indem Sie die-Überladung mit-Optionen aufrufen.</span><span class="sxs-lookup"><span data-stu-id="c8946-168">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="c8946-169">Import Datei</span><span class="sxs-lookup"><span data-stu-id="c8946-169">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="c8946-170">Indexseite</span><span class="sxs-lookup"><span data-stu-id="c8946-170">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="c8946-171">App-Komponente</span><span class="sxs-lookup"><span data-stu-id="c8946-171">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="c8946-172">Redirecttologin-Komponente</span><span class="sxs-lookup"><span data-stu-id="c8946-172">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="c8946-173">Logindisplay-Komponente</span><span class="sxs-lookup"><span data-stu-id="c8946-173">LoginDisplay component</span></span>

<span data-ttu-id="c8946-174">Die `LoginDisplay` Komponente (*Shared/logindisplay. Razor*) wird in der `MainLayout` Komponente (*Shared/MainLayout. Razor*) gerendert und verwaltet die folgenden Verhaltensweisen:</span><span class="sxs-lookup"><span data-stu-id="c8946-174">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="c8946-175">Für authentifizierte Benutzer:</span><span class="sxs-lookup"><span data-stu-id="c8946-175">For authenticated users:</span></span>
  * <span data-ttu-id="c8946-176">Zeigt den aktuellen Benutzernamen an.</span><span class="sxs-lookup"><span data-stu-id="c8946-176">Displays the current user name.</span></span>
  * <span data-ttu-id="c8946-177">Gibt einen Link zur Benutzerprofil Seite in ASP.net Core Identität an.</span><span class="sxs-lookup"><span data-stu-id="c8946-177">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="c8946-178">Bietet eine Schaltfläche, um sich von der APP abzumelden.</span><span class="sxs-lookup"><span data-stu-id="c8946-178">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="c8946-179">Für anonyme Benutzer:</span><span class="sxs-lookup"><span data-stu-id="c8946-179">For anonymous users:</span></span>
  * <span data-ttu-id="c8946-180">Bietet die Option zum Registrieren von.</span><span class="sxs-lookup"><span data-stu-id="c8946-180">Offers the option to register.</span></span>
  * <span data-ttu-id="c8946-181">Bietet die Option zum Anmelden.</span><span class="sxs-lookup"><span data-stu-id="c8946-181">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="c8946-182">Authentifizierungs Komponente</span><span class="sxs-lookup"><span data-stu-id="c8946-182">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="c8946-183">Fetchdata-Komponente</span><span class="sxs-lookup"><span data-stu-id="c8946-183">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="c8946-184">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="c8946-184">Run the app</span></span>

<span data-ttu-id="c8946-185">Führen Sie die APP aus dem Server Projekt aus.</span><span class="sxs-lookup"><span data-stu-id="c8946-185">Run the app from the Server project.</span></span> <span data-ttu-id="c8946-186">Wenn Sie Visual Studio verwenden, wählen Sie das Server Projekt in **Projektmappen-Explorer** aus, und klicken Sie auf der Symbolleiste auf die Schaltfläche **Ausführen** , oder starten Sie die APP im Menü **Debuggen**</span><span class="sxs-lookup"><span data-stu-id="c8946-186">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="c8946-187">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="c8946-187">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>

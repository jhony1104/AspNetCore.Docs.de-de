---
title: Sichern einer ASP.net Core Blazor Webassembly-gehosteten App mit dem Identity Server
author: guardrex
description: So erstellen Sie eine neue Blazor gehostete App mit Authentifizierung aus Visual Studio, die ein [identityserver](https://identityserver.io/) -Backend verwendet
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: c85843c04688beefe7ea87d9e8b281d14ab85bc5
ms.sourcegitcommit: b0062f29cba2e5c21b95cf89eaf435ba830d11a3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/15/2020
ms.locfileid: "84776513"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="ff65b-103">Sichern einer ASP.net Core Blazor Webassembly-gehosteten App mit dem Identity Server</span><span class="sxs-lookup"><span data-stu-id="ff65b-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="ff65b-104">Von [Javier calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ff65b-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="ff65b-105">In diesem Artikel wird erläutert, wie Sie eine neue Blazor gehostete app erstellen, die [identityserver](https://identityserver.io/) verwendet, um Benutzer und API-Aufrufe zu authentifizieren.</span><span class="sxs-lookup"><span data-stu-id="ff65b-105">This article explains how to create a new Blazor hosted app that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ff65b-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff65b-106">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ff65b-107">In Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="ff65b-107">In Visual Studio:</span></span>

1. <span data-ttu-id="ff65b-108">Erstellen Sie eine neue \*\* Blazor Webassembly\*\* -app.</span><span class="sxs-lookup"><span data-stu-id="ff65b-108">Create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="ff65b-109">Weitere Informationen finden Sie unter <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="ff65b-109">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="ff65b-110">Wählen Sie im Dialogfeld **neue Blazor app erstellen** im Abschnitt **Authentifizierung** die Option **ändern** aus.</span><span class="sxs-lookup"><span data-stu-id="ff65b-110">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="ff65b-111">Wählen Sie **einzelne Benutzerkonten** und dann **OK**aus.</span><span class="sxs-lookup"><span data-stu-id="ff65b-111">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="ff65b-112">Aktivieren Sie das Kontrollkästchen **ASP.net Core gehostet** im Abschnitt **erweitert** .</span><span class="sxs-lookup"><span data-stu-id="ff65b-112">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="ff65b-113">Wählen Sie die Schaltfläche **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="ff65b-113">Select the **Create** button.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="ff65b-114">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="ff65b-114">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="ff65b-115">Führen Sie den folgenden Befehl aus, um die app in einer Befehlsshell zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="ff65b-115">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="ff65b-116">Um den Ausgabe Speicherort anzugeben, der einen Projektordner erstellt, wenn er nicht vorhanden ist, schließen Sie die Output-Option in den Befehl mit einem Pfad (z `-o BlazorSample` . b.) ein.</span><span class="sxs-lookup"><span data-stu-id="ff65b-116">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="ff65b-117">Der Name des Ordners wird auch Teil des Projekt namens.</span><span class="sxs-lookup"><span data-stu-id="ff65b-117">The folder name also becomes part of the project's name.</span></span>

---

## <a name="server-app-configuration"></a><span data-ttu-id="ff65b-118">Server-App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="ff65b-118">Server app configuration</span></span>

<span data-ttu-id="ff65b-119">In den folgenden Abschnitten werden Ergänzungen zum-Projekt beschrieben, wenn die Authentifizierungs Unterstützung enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="ff65b-119">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="ff65b-120">Startklasse</span><span class="sxs-lookup"><span data-stu-id="ff65b-120">Startup class</span></span>

<span data-ttu-id="ff65b-121">Die- `Startup` Klasse verfügt über die folgenden Ergänzungen.</span><span class="sxs-lookup"><span data-stu-id="ff65b-121">The `Startup` class has the following additions.</span></span>

* <span data-ttu-id="ff65b-122">In `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ff65b-122">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="ff65b-123">ASP.net Core Identity :</span><span class="sxs-lookup"><span data-stu-id="ff65b-123">ASP.NET Core Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="ff65b-124">Identityserver mit einer zusätzlichen <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> Hilfsmethode, die Standard-ASP.net Core Konventionen zusätzlich zu identityserver festlegt:</span><span class="sxs-lookup"><span data-stu-id="ff65b-124">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="ff65b-125">Authentifizierung mit einer zusätzlichen <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> Hilfsmethode, die die APP für die Validierung der von identityserver erstellten JWT-Token konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="ff65b-125">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="ff65b-126">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="ff65b-126">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="ff65b-127">Die identityserver-Middleware macht die Endpunkte der Open ID Connect (oidc) verfügbar:</span><span class="sxs-lookup"><span data-stu-id="ff65b-127">The IdentityServer middleware exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="ff65b-128">Die Authentifizierungs Middleware ist verantwortlich für das Überprüfen von Anforderungs Anmelde Informationen und das Festlegen des Benutzers für den Anforderungs Kontext:</span><span class="sxs-lookup"><span data-stu-id="ff65b-128">The Authentication middleware is responsible for validating request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="ff65b-129">Autorisierungs Middleware ermöglicht Autorisierungs Funktionen:</span><span class="sxs-lookup"><span data-stu-id="ff65b-129">Authorization Middleware enables authorization capabilities:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="ff65b-130">Addapiauthorization</span><span class="sxs-lookup"><span data-stu-id="ff65b-130">AddApiAuthorization</span></span>

<span data-ttu-id="ff65b-131">Die- <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> Hilfsmethode konfiguriert [identityserver](https://identityserver.io/) für ASP.net Core Szenarien.</span><span class="sxs-lookup"><span data-stu-id="ff65b-131">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="ff65b-132">Identityserver ist ein leistungsfähiges und erweiterbares Framework für die Behandlung von App-Sicherheitsbedenken.</span><span class="sxs-lookup"><span data-stu-id="ff65b-132">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="ff65b-133">Identityserver macht unnötige Komplexität für die häufigsten Szenarien verfügbar.</span><span class="sxs-lookup"><span data-stu-id="ff65b-133">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="ff65b-134">Folglich wird ein Satz von Konventionen und Konfigurationsoptionen bereitgestellt, die einen guten Ausgangspunkt in Erwägung gezogen.</span><span class="sxs-lookup"><span data-stu-id="ff65b-134">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="ff65b-135">Nachdem sich Ihre Authentifizierung geändert hat, steht die volle Leistungsfähigkeit von identityserver zur Verfügung, um die Authentifizierung an die Anforderungen einer APP anzupassen.</span><span class="sxs-lookup"><span data-stu-id="ff65b-135">Once your authentication needs change, the full power of IdentityServer is available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="ff65b-136">Addidentityserverjwt</span><span class="sxs-lookup"><span data-stu-id="ff65b-136">AddIdentityServerJwt</span></span>

<span data-ttu-id="ff65b-137">Die- <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> Hilfsmethode konfiguriert ein Richtlinien Schema für die APP als Standard Authentifizierungs Handler.</span><span class="sxs-lookup"><span data-stu-id="ff65b-137">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="ff65b-138">Die Richtlinie ist so konfiguriert, dass Identity Sie alle Anforderungen verarbeiten kann, die an einen untergeordneten Pfad im URL-Bereich weitergeleitet werden Identity `/Identity` .</span><span class="sxs-lookup"><span data-stu-id="ff65b-138">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="ff65b-139">Der <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> verarbeitet alle anderen Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="ff65b-139">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="ff65b-140">Außerdem gilt diese Methode:</span><span class="sxs-lookup"><span data-stu-id="ff65b-140">Additionally, this method:</span></span>

* <span data-ttu-id="ff65b-141">Registriert eine `{APPLICATION NAME}API` API-Ressource bei identityserver mit einem Standardbereich von `{APPLICATION NAME}API` .</span><span class="sxs-lookup"><span data-stu-id="ff65b-141">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="ff65b-142">Konfiguriert die JWT-bearertoken-Middleware zum Überprüfen von Token, die von identityserver für die APP ausgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="ff65b-142">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="ff65b-143">Weatherforecastcontroller</span><span class="sxs-lookup"><span data-stu-id="ff65b-143">WeatherForecastController</span></span>

<span data-ttu-id="ff65b-144">In `WeatherForecastController` (*Controllers/weatherforecastcontroller. cs*) wird das- [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) Attribut auf die-Klasse angewendet.</span><span class="sxs-lookup"><span data-stu-id="ff65b-144">In the `WeatherForecastController` (*Controllers/WeatherForecastController.cs*), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="ff65b-145">Das-Attribut gibt an, dass der Benutzer auf der Basis der Standard Richtlinie autorisiert werden muss, um auf die Ressource zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="ff65b-145">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="ff65b-146">Die standardmäßige Autorisierungs Richtlinie ist so konfiguriert, dass das Standard Authentifizierungsschema verwendet wird, das von festgelegt wird <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> .</span><span class="sxs-lookup"><span data-stu-id="ff65b-146">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>.</span></span> <span data-ttu-id="ff65b-147">Die-Hilfsmethode konfiguriert <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> als Standard Handler für Anforderungen an die app.</span><span class="sxs-lookup"><span data-stu-id="ff65b-147">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="ff65b-148">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="ff65b-148">ApplicationDbContext</span></span>

<span data-ttu-id="ff65b-149">Erweitert in `ApplicationDbContext` (*Data/applicationdbcontext. cs*), <xref:Microsoft.EntityFrameworkCore.DbContext> <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> um das Schema für identityserver einzuschließen.</span><span class="sxs-lookup"><span data-stu-id="ff65b-149">In the `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), <xref:Microsoft.EntityFrameworkCore.DbContext> extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="ff65b-150"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> wird von <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="ff65b-150"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="ff65b-151">Um die vollständige Kontrolle über das Datenbankschema zu erhalten, erben Sie von einer der verfügbaren Identity <xref:Microsoft.EntityFrameworkCore.DbContext> Klassen und konfigurieren den Kontext so, dass das Identity Schema durch Aufrufen von `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in der- <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> Methode eingeschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="ff65b-151">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="ff65b-152">Oidcconfigurationcontroller</span><span class="sxs-lookup"><span data-stu-id="ff65b-152">OidcConfigurationController</span></span>

<span data-ttu-id="ff65b-153">In der `OidcConfigurationController` (*Controllers/oidcconfigurationcontroller. cs*) wird der Client Endpunkt bereitgestellt, um oidc-Parameter bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="ff65b-153">In the `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="ff65b-154">App-Einstellungsdateien</span><span class="sxs-lookup"><span data-stu-id="ff65b-154">App settings files</span></span>

<span data-ttu-id="ff65b-155">In der APP-Einstellungsdatei (*appsettings.jsauf*) im Projektstamm Abschnitt wird in diesem `IdentityServer` Abschnitt die Liste der konfigurierten Clients beschrieben.</span><span class="sxs-lookup"><span data-stu-id="ff65b-155">In the app settings file (*appsettings.json*) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="ff65b-156">Im folgenden Beispiel gibt es einen einzelnen Client.</span><span class="sxs-lookup"><span data-stu-id="ff65b-156">In the following example, there's a single client.</span></span> <span data-ttu-id="ff65b-157">Der Client Name entspricht dem APP-Namen und wird dem OAuth-Parameter gemäß der Konvention zugeordnet `ClientId` .</span><span class="sxs-lookup"><span data-stu-id="ff65b-157">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="ff65b-158">Das Profil gibt den App-Typ an, der konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="ff65b-158">The profile indicates the app type being configured.</span></span> <span data-ttu-id="ff65b-159">Das Profil wird intern verwendet, um Konventionen zu steuern, die den Konfigurationsprozess für den Server vereinfachen.</span><span class="sxs-lookup"><span data-stu-id="ff65b-159">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="ff65b-160">Der Platzhalter `{APP ASSEMBLY}` ist der AssemblyName der APP (z `BlazorSample.Client` . b.).</span><span class="sxs-lookup"><span data-stu-id="ff65b-160">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.Client`).</span></span>

## <a name="client-app-configuration"></a><span data-ttu-id="ff65b-161">Client-App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="ff65b-161">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="ff65b-162">Authentifizierungs Paket</span><span class="sxs-lookup"><span data-stu-id="ff65b-162">Authentication package</span></span>

<span data-ttu-id="ff65b-163">Wenn eine APP für die Verwendung einzelner Benutzerkonten () erstellt wird `Individual` , erhält die APP automatisch einen Paket Verweis für das [Microsoft. aspnetcore. Components. Webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) -Paket in der Projektdatei der app.</span><span class="sxs-lookup"><span data-stu-id="ff65b-163">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package in the app's project file.</span></span> <span data-ttu-id="ff65b-164">Das Paket enthält eine Reihe von primitiven, die der APP helfen, Benutzer zu authentifizieren und Token abzurufen, um geschützte APIs aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="ff65b-164">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="ff65b-165">Wenn Sie eine Authentifizierung zu einer APP hinzufügen, fügen Sie das Paket manuell zur Projektdatei der APP hinzu:</span><span class="sxs-lookup"><span data-stu-id="ff65b-165">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

### <a name="api-authorization-support"></a><span data-ttu-id="ff65b-166">API-Autorisierungs Unterstützung</span><span class="sxs-lookup"><span data-stu-id="ff65b-166">API authorization support</span></span>

<span data-ttu-id="ff65b-167">Die Unterstützung für die Authentifizierung von Benutzern wird durch die im [Microsoft. aspnetcore. Components. Webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) -Paket bereitgestellte Erweiterungsmethode an den Dienst Container angeschlossen.</span><span class="sxs-lookup"><span data-stu-id="ff65b-167">The support for authenticating users is plugged into the service container by the extension method provided inside the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span> <span data-ttu-id="ff65b-168">Diese Methode richtet die Dienste ein, die von der APP für die Interaktion mit dem vorhandenen Autorisierungssystem benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="ff65b-168">This method sets up the services required by the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="ff65b-169">Standardmäßig wird die Konfiguration für die APP gemäß der Konvention von geladen `_configuration/{client-id}` .</span><span class="sxs-lookup"><span data-stu-id="ff65b-169">By default, configuration for the app is loaded by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="ff65b-170">Gemäß der Konvention wird die Client-ID auf den Assemblynamen der APP festgelegt.</span><span class="sxs-lookup"><span data-stu-id="ff65b-170">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="ff65b-171">Diese URL kann geändert werden, um auf einen separaten Endpunkt zu verweisen, indem Sie die-Überladung mit-Optionen aufrufen.</span><span class="sxs-lookup"><span data-stu-id="ff65b-171">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="ff65b-172">Import Datei</span><span class="sxs-lookup"><span data-stu-id="ff65b-172">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="ff65b-173">Indexseite</span><span class="sxs-lookup"><span data-stu-id="ff65b-173">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="ff65b-174">App-Komponente</span><span class="sxs-lookup"><span data-stu-id="ff65b-174">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="ff65b-175">Redirecttologin-Komponente</span><span class="sxs-lookup"><span data-stu-id="ff65b-175">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="ff65b-176">Logindisplay-Komponente</span><span class="sxs-lookup"><span data-stu-id="ff65b-176">LoginDisplay component</span></span>

<span data-ttu-id="ff65b-177">Die `LoginDisplay` Komponente (*Shared/logindisplay. Razor*) wird in der `MainLayout` Komponente (*Shared/MainLayout. Razor*) gerendert und verwaltet die folgenden Verhaltensweisen:</span><span class="sxs-lookup"><span data-stu-id="ff65b-177">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="ff65b-178">Für authentifizierte Benutzer:</span><span class="sxs-lookup"><span data-stu-id="ff65b-178">For authenticated users:</span></span>
  * <span data-ttu-id="ff65b-179">Zeigt den aktuellen Benutzernamen an.</span><span class="sxs-lookup"><span data-stu-id="ff65b-179">Displays the current user name.</span></span>
  * <span data-ttu-id="ff65b-180">Bietet einen Link zur Benutzerprofil Seite in ASP.net Core Identity .</span><span class="sxs-lookup"><span data-stu-id="ff65b-180">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="ff65b-181">Bietet eine Schaltfläche, um sich von der APP abzumelden.</span><span class="sxs-lookup"><span data-stu-id="ff65b-181">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="ff65b-182">Für anonyme Benutzer:</span><span class="sxs-lookup"><span data-stu-id="ff65b-182">For anonymous users:</span></span>
  * <span data-ttu-id="ff65b-183">Bietet die Option zum Registrieren von.</span><span class="sxs-lookup"><span data-stu-id="ff65b-183">Offers the option to register.</span></span>
  * <span data-ttu-id="ff65b-184">Bietet die Option zum Anmelden.</span><span class="sxs-lookup"><span data-stu-id="ff65b-184">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="ff65b-185">Authentifizierungs Komponente</span><span class="sxs-lookup"><span data-stu-id="ff65b-185">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="ff65b-186">Fetchdata-Komponente</span><span class="sxs-lookup"><span data-stu-id="ff65b-186">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="ff65b-187">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="ff65b-187">Run the app</span></span>

<span data-ttu-id="ff65b-188">Führen Sie die APP aus dem Server Projekt aus.</span><span class="sxs-lookup"><span data-stu-id="ff65b-188">Run the app from the Server project.</span></span> <span data-ttu-id="ff65b-189">Wenn Sie Visual Studio verwenden, können Sie Folgendes tun:</span><span class="sxs-lookup"><span data-stu-id="ff65b-189">When using Visual Studio, either:</span></span>

* <span data-ttu-id="ff65b-190">Legen Sie die Dropdown Liste **Start Projekte** auf der Symbolleiste auf die *Server-API-APP* fest, und wählen Sie die Schaltfläche **Ausführen** .</span><span class="sxs-lookup"><span data-stu-id="ff65b-190">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="ff65b-191">Wählen Sie das Server Projekt in **Projektmappen-Explorer** aus, und klicken Sie auf der Symbolleiste auf die Schaltfläche **Ausführen** , oder starten Sie die APP im Menü **Debuggen**</span><span class="sxs-lookup"><span data-stu-id="ff65b-191">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

## <a name="name-and-role-claim-with-api-authorization"></a><span data-ttu-id="ff65b-192">Name und Rollen Anspruch mit API-Autorisierung</span><span class="sxs-lookup"><span data-stu-id="ff65b-192">Name and role claim with API authorization</span></span>

### <a name="custom-user-factory"></a><span data-ttu-id="ff65b-193">Benutzerdefinierte benutzerfactory</span><span class="sxs-lookup"><span data-stu-id="ff65b-193">Custom user factory</span></span>

<span data-ttu-id="ff65b-194">Erstellen Sie in der Client-App eine benutzerdefinierte benutzerfactory.</span><span class="sxs-lookup"><span data-stu-id="ff65b-194">In the Client app, create a custom user factory.</span></span> Identity<span data-ttu-id="ff65b-195">Der Server sendet mehrere Rollen als JSON-Array in einem einzelnen `role` Anspruch.</span><span class="sxs-lookup"><span data-stu-id="ff65b-195"> Server sends multiple roles as a JSON array in a single `role` claim.</span></span> <span data-ttu-id="ff65b-196">Eine einzelne Rolle wird als Zeichen folgen Wert im Anspruch gesendet.</span><span class="sxs-lookup"><span data-stu-id="ff65b-196">A single role is sent as a string value in the claim.</span></span> <span data-ttu-id="ff65b-197">Die Factory erstellt einen einzelnen `role` Anspruch für jede der Rollen des Benutzers.</span><span class="sxs-lookup"><span data-stu-id="ff65b-197">The factory creates an individual `role` claim for each of the user's roles.</span></span>

<span data-ttu-id="ff65b-198">*CustomUserFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="ff65b-198">*CustomUserFactory.cs*:</span></span>

```csharp
using System.Linq;
using System.Security.Claims;
using System.Text.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    public CustomUserFactory(IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var user = await base.CreateUserAsync(account, options);

        if (user.Identity.IsAuthenticated)
        {
            var identity = (ClaimsIdentity)user.Identity;
            var roleClaims = identity.FindAll(identity.RoleClaimType);

            if (roleClaims != null && roleClaims.Any())
            {
                foreach (var existingClaim in roleClaims)
                {
                    identity.RemoveClaim(existingClaim);
                }

                var rolesElem = account.AdditionalProperties[identity.RoleClaimType];

                if (rolesElem is JsonElement roles)
                {
                    if (roles.ValueKind == JsonValueKind.Array)
                    {
                        foreach (var role in roles.EnumerateArray())
                        {
                            identity.AddClaim(new Claim(options.RoleClaim, role.GetString()));
                        }
                    }
                    else
                    {
                        identity.AddClaim(new Claim(options.RoleClaim, roles.GetString()));
                    }
                }
            }
        }

        return user;
    }
}
```

<span data-ttu-id="ff65b-199">Registrieren Sie in der Client-App die Factory in `Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="ff65b-199">In the Client app, register the factory in `Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

<span data-ttu-id="ff65b-200">Wenden Sie in der Server-APP <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> für den Generator an Identity , der Rollen bezogene Dienste hinzufügt:</span><span class="sxs-lookup"><span data-stu-id="ff65b-200">In the Server app, call <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> on the Identity builder, which adds role-related services:</span></span>

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-identity-server"></a><span data-ttu-id="ff65b-201">IdentityServer konfigurieren</span><span class="sxs-lookup"><span data-stu-id="ff65b-201">Configure Identity Server</span></span>

<span data-ttu-id="ff65b-202">Verwenden Sie **einen** der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="ff65b-202">Use **one** of the following approaches:</span></span>

* [<span data-ttu-id="ff65b-203">API-Autorisierungs Optionen</span><span class="sxs-lookup"><span data-stu-id="ff65b-203">API authorization options</span></span>](#api-authorization-options)
* [<span data-ttu-id="ff65b-204">Profil Dienst</span><span class="sxs-lookup"><span data-stu-id="ff65b-204">Profile Service</span></span>](#profile-service)

#### <a name="api-authorization-options"></a><span data-ttu-id="ff65b-205">API-Autorisierungs Optionen</span><span class="sxs-lookup"><span data-stu-id="ff65b-205">API authorization options</span></span>

<span data-ttu-id="ff65b-206">In der Server-App:</span><span class="sxs-lookup"><span data-stu-id="ff65b-206">In the Server app:</span></span>

* <span data-ttu-id="ff65b-207">Konfigurieren Identity Sie den Server so, dass die `name` `role` Ansprüche und in das ID-Token und das Zugriffs Token eingefügt werden.</span><span class="sxs-lookup"><span data-stu-id="ff65b-207">Configure Identity Server to put the `name` and `role` claims into the ID token and access token.</span></span>
* <span data-ttu-id="ff65b-208">Hiermit wird die Standard Zuordnung für Rollen im JWT-Tokenhandler verhindert.</span><span class="sxs-lookup"><span data-stu-id="ff65b-208">Prevent the default mapping for roles in the JWT token handler.</span></span>

```csharp
using System.IdentityModel.Tokens.Jwt;
using System.Linq;

...

services.AddIdentityServer()
    .AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options => {
        options.IdentityResources["openid"].UserClaims.Add("name");
        options.ApiResources.Single().UserClaims.Add("name");
        options.IdentityResources["openid"].UserClaims.Add("role");
        options.ApiResources.Single().UserClaims.Add("role");
    });

JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Remove("role");
```

#### <a name="profile-service"></a><span data-ttu-id="ff65b-209">Profil Dienst</span><span class="sxs-lookup"><span data-stu-id="ff65b-209">Profile Service</span></span>

<span data-ttu-id="ff65b-210">Erstellen Sie in der Server-App eine- `ProfileService` Implementierung.</span><span class="sxs-lookup"><span data-stu-id="ff65b-210">In the Server app, create a `ProfileService` implementation.</span></span>

<span data-ttu-id="ff65b-211">*ProfileService.cs*:</span><span class="sxs-lookup"><span data-stu-id="ff65b-211">*ProfileService.cs*:</span></span>

```csharp
using IdentityModel;
using IdentityServer4.Models;
using IdentityServer4.Services;
using System.Threading.Tasks;

public class ProfileService : IProfileService
{
    public ProfileService()
    {
    }

    public Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        return Task.CompletedTask;
    }

    public Task IsActiveAsync(IsActiveContext context)
    {
        return Task.CompletedTask;
    }
}
```

<span data-ttu-id="ff65b-212">Registrieren Sie in der Server-App den Profil Dienst in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ff65b-212">In the Server app, register the Profile Service in `Startup.ConfigureServices`:</span></span>

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a><span data-ttu-id="ff65b-213">Verwenden von Autorisierungs Mechanismen</span><span class="sxs-lookup"><span data-stu-id="ff65b-213">Use authorization mechanisms</span></span>

<span data-ttu-id="ff65b-214">In der Client-App sind die Komponenten Autorisierungs Ansätze an dieser Stelle funktionsfähig.</span><span class="sxs-lookup"><span data-stu-id="ff65b-214">In the Client app, component authorization approaches are functional at this point.</span></span> <span data-ttu-id="ff65b-215">Jeder der Autorisierungs Mechanismen in-Komponenten kann eine Rolle verwenden, um den Benutzer zu autorisieren:</span><span class="sxs-lookup"><span data-stu-id="ff65b-215">Any of the authorization mechanisms in components can use a role to authorize the user:</span></span>

* <span data-ttu-id="ff65b-216">[Autorität View-Komponente](xref:security/blazor/index#authorizeview-component) (Beispiel: `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="ff65b-216">[AuthorizeView component](xref:security/blazor/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="ff65b-217">[ `[Authorize]` Attribute-Direktive](xref:security/blazor/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (Beispiel: `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="ff65b-217">[`[Authorize]` attribute directive](xref:security/blazor/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="ff65b-218">[Prozedurale Logik](xref:security/blazor/index#procedural-logic) (Beispiel: `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="ff65b-218">[Procedural logic](xref:security/blazor/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="ff65b-219">Mehrere Rollen Tests werden unterstützt:</span><span class="sxs-lookup"><span data-stu-id="ff65b-219">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

<span data-ttu-id="ff65b-220">`User.Identity.Name`wird in der Client-App mit dem Benutzernamen des Benutzers aufgefüllt, bei dem es sich in der Regel um Ihre Anmelde-e-Mail-Adresse handelt.</span><span class="sxs-lookup"><span data-stu-id="ff65b-220">`User.Identity.Name` is populated in the Client app with the user's user name, which is usually their sign-in email address.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="ff65b-221">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="ff65b-221">Additional resources</span></span>

* [<span data-ttu-id="ff65b-222">Bereitstellung für Azure App Service</span><span class="sxs-lookup"><span data-stu-id="ff65b-222">Deployment to Azure App Service</span></span>](xref:security/authentication/identity/spa#deploy-to-production)
* [<span data-ttu-id="ff65b-223">Importieren eines Zertifikats aus Key Vault (Azure-Dokumentation)</span><span class="sxs-lookup"><span data-stu-id="ff65b-223">Import a certificate from Key Vault (Azure documentation)</span></span>](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="ff65b-224">Nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen in einer APP mit einem sicheren Standard Client</span><span class="sxs-lookup"><span data-stu-id="ff65b-224">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)

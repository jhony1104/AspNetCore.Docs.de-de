---
title: Sichern einer gehosteten Blazor WebAssembly-App in ASP.NET Core mit IdentityServer
author: guardrex
description: In diesem Artikel erfahren Sie, wie Sie eine neue von Blazor gehostete App mit Authentifizierung in Visual Studio erstellen, die ein [IdentityServer](https://identityserver.io/)-Backend verwendet.
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
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: 0fdc88e8e50856fcc4da0beb74f03925ae24401e
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103284"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="231ed-103">Sichern einer gehosteten Blazor WebAssembly-App in ASP.NET Core mit IdentityServer</span><span class="sxs-lookup"><span data-stu-id="231ed-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="231ed-104">Von [Javier Calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="231ed-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="231ed-105">In diesem Artikel wird erläutert, wie eine neue von Blazor gehostete App erstellt wird, für die [IdentityServer](https://identityserver.io/) verwendet wird, um Benutzer und API-Aufrufe zu authentifizieren.</span><span class="sxs-lookup"><span data-stu-id="231ed-105">This article explains how to create a new Blazor hosted app that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="231ed-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="231ed-106">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="231ed-107">In Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="231ed-107">In Visual Studio:</span></span>

1. <span data-ttu-id="231ed-108">Erstellen Sie eine neue **Blazor WebAssembly**-App.</span><span class="sxs-lookup"><span data-stu-id="231ed-108">Create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="231ed-109">Weitere Informationen finden Sie unter <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="231ed-109">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="231ed-110">Klicken Sie im Dialogfeld **Neue Blazor-App erstellen** im Bereich **Authentifizierung** auf **Ändern**.</span><span class="sxs-lookup"><span data-stu-id="231ed-110">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="231ed-111">Klicken Sie zuerst auf **Einzelne Benutzerkonten** und dann auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="231ed-111">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="231ed-112">Aktivieren Sie im Bereich **Erweitert** das Kontrollkästchen **ASP.NET Core hosted** (In ASP.NET Core gehostet).</span><span class="sxs-lookup"><span data-stu-id="231ed-112">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="231ed-113">Wählen Sie die Schaltfläche **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="231ed-113">Select the **Create** button.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="231ed-114">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="231ed-114">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="231ed-115">Führen Sie zum Erstellen der App in einer Befehlsshell den folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="231ed-115">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="231ed-116">Zum Angeben eines Ausgabespeicherorts (wodurch ein Projektordner erstellt wird, falls noch nicht vorhanden) schließen Sie die Ausgabeoption mit einem Pfad (z. B. `-o BlazorSample`) in den Befehl ein.</span><span class="sxs-lookup"><span data-stu-id="231ed-116">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="231ed-117">Der Name des Ordners wird auch Teil des Projektnamens.</span><span class="sxs-lookup"><span data-stu-id="231ed-117">The folder name also becomes part of the project's name.</span></span>

---

## <a name="server-app-configuration"></a><span data-ttu-id="231ed-118">Server-App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="231ed-118">Server app configuration</span></span>

<span data-ttu-id="231ed-119">In den folgenden Abschnitten werden Ergänzungen zum Projekt erläutert, wenn die Authentifizierungsunterstützung eingeschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="231ed-119">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="231ed-120">Startklasse</span><span class="sxs-lookup"><span data-stu-id="231ed-120">Startup class</span></span>

<span data-ttu-id="231ed-121">Die `Startup`-Klasse verfügt über die folgenden Ergänzungen.</span><span class="sxs-lookup"><span data-stu-id="231ed-121">The `Startup` class has the following additions.</span></span>

* <span data-ttu-id="231ed-122">In `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="231ed-122">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="231ed-123">ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="231ed-123">ASP.NET Core Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="231ed-124">IdentityServer mit einer zusätzlichen <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>-Hilfsprogrammmethode, die ASP.NET Core-Standardkonventionen zusätzlich zu IdentityServer einrichtet:</span><span class="sxs-lookup"><span data-stu-id="231ed-124">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="231ed-125">Authentifizierung mit einer zusätzlichen <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>-Hilfsprogrammmethode, die die App so konfiguriert, dass sie von IdentityServer generierte JWT-Token überprüft:</span><span class="sxs-lookup"><span data-stu-id="231ed-125">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="231ed-126">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="231ed-126">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="231ed-127">Die IdentityServer-Middleware macht die Open ID Connect-Endpunkte (OIDC) verfügbar:</span><span class="sxs-lookup"><span data-stu-id="231ed-127">The IdentityServer middleware exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="231ed-128">Die Authentifizierungsmiddleware ist für die Überprüfung der Anforderungsanmeldeinformationen und für das Festlegen des Benutzers auf den Anforderungskontext verantwortlich:</span><span class="sxs-lookup"><span data-stu-id="231ed-128">The Authentication middleware is responsible for validating request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="231ed-129">Die Autorisierungsmiddleware aktiviert Autorisierungsfunktionen:</span><span class="sxs-lookup"><span data-stu-id="231ed-129">Authorization Middleware enables authorization capabilities:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="231ed-130">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="231ed-130">AddApiAuthorization</span></span>

<span data-ttu-id="231ed-131">Die <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>-Hilfsprogrammmethode konfiguriert [IdentityServer](https://identityserver.io/) für ASP.NET Core-Szenarios.</span><span class="sxs-lookup"><span data-stu-id="231ed-131">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="231ed-132">IdentityServer ist ein leistungsfähiges und erweiterbares Framework für Überlegungen zum Thema „App-Sicherheit“.</span><span class="sxs-lookup"><span data-stu-id="231ed-132">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="231ed-133">IdentityServer ist für die meisten gängigen Szenarios unnötig komplex.</span><span class="sxs-lookup"><span data-stu-id="231ed-133">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="231ed-134">Als Folge werden mehrere Konventionen und Konfigurationsoptionen bereitgestellt, die sich gut als Startpunkt eignen.</span><span class="sxs-lookup"><span data-stu-id="231ed-134">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="231ed-135">Sobald Ihre Authentifizierung geändert werden muss, stehen alle Funktionen von IdentityServer zur Verfügung, um die Authentifizierung so anzupassen, dass sie den Anforderungen der App entspricht.</span><span class="sxs-lookup"><span data-stu-id="231ed-135">Once your authentication needs change, the full power of IdentityServer is available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="231ed-136">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="231ed-136">AddIdentityServerJwt</span></span>

<span data-ttu-id="231ed-137">Die <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>-Hilfsprogrammmethode konfiguriert ein Richtlinienschema für die App als Standardauthentifizierungshandler.</span><span class="sxs-lookup"><span data-stu-id="231ed-137">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="231ed-138">Die Richtlinie ist so konfiguriert, dass Identity alle an beliebige Unterpfade im Identity-URL-Raum `/Identity` weitergeleiteten Anforderungen verarbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="231ed-138">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="231ed-139"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> verarbeitet alle anderen Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="231ed-139">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="231ed-140">Außerdem übernimmt diese Methode die folgenden Aufgaben:</span><span class="sxs-lookup"><span data-stu-id="231ed-140">Additionally, this method:</span></span>

* <span data-ttu-id="231ed-141">Registrieren einer `{APPLICATION NAME}API`-API-Ressource mit IdentityServer mit einem Standardbereich für `{APPLICATION NAME}API`</span><span class="sxs-lookup"><span data-stu-id="231ed-141">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="231ed-142">Konfigurieren der JWT-Bearertoken-Middleware zum Überprüfen der von IdentityServer für die App ausgegeben Token</span><span class="sxs-lookup"><span data-stu-id="231ed-142">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="231ed-143">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="231ed-143">WeatherForecastController</span></span>

<span data-ttu-id="231ed-144">In `WeatherForecastController` (*Controllers/WeatherForecastController.cs*), wird das [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)-Attribut auf die Klasse angewendet.</span><span class="sxs-lookup"><span data-stu-id="231ed-144">In the `WeatherForecastController` (*Controllers/WeatherForecastController.cs*), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="231ed-145">Das Attribut gibt an, dass der Benutzer basierend auf der Standardrichtlinie autorisiert werden muss, um auf die Ressource zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="231ed-145">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="231ed-146">Die Standardautorisierungsrichtlinie ist so konfiguriert, dass das Standardauthentifizierungsschema verwendet wird, das von <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> eingerichtet wird.</span><span class="sxs-lookup"><span data-stu-id="231ed-146">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>.</span></span> <span data-ttu-id="231ed-147">Die Hilfsprogrammmethode konfiguriert <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> als Standardhandler für Anforderungen an die App.</span><span class="sxs-lookup"><span data-stu-id="231ed-147">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="231ed-148">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="231ed-148">ApplicationDbContext</span></span>

<span data-ttu-id="231ed-149">In `ApplicationDbContext` (*Data/ApplicationDbContext.cs*) wird <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> durch <xref:Microsoft.EntityFrameworkCore.DbContext> erweitert, damit das Schema für IdentityServer eingeschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="231ed-149">In the `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), <xref:Microsoft.EntityFrameworkCore.DbContext> extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="231ed-150"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> wird von <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="231ed-150"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="231ed-151">Für eine vollständige Kontrolle des Datenbankschemas wird von einer der verfügbaren Identity-<xref:Microsoft.EntityFrameworkCore.DbContext>-Klassen geerbt, und der Kontext wird so konfiguriert, dass er das Identity-Schema einschließt, indem `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in der <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>-Methode aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="231ed-151">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="231ed-152">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="231ed-152">OidcConfigurationController</span></span>

<span data-ttu-id="231ed-153">In `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*) wird der Clientendpunkt bereitgestellt, um OIDC-Parameter bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="231ed-153">In the `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="231ed-154">App-Einstellungsdateien</span><span class="sxs-lookup"><span data-stu-id="231ed-154">App settings files</span></span>

<span data-ttu-id="231ed-155">In der App-Einstellungsdatei (*appsettings.json*) beschreibt der Bereich `IdentityServer` auf Projektstammebene die Liste der konfigurierten Clients.</span><span class="sxs-lookup"><span data-stu-id="231ed-155">In the app settings file (*appsettings.json*) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="231ed-156">Im folgenden Beispiel gibt es einen einzelnen Client.</span><span class="sxs-lookup"><span data-stu-id="231ed-156">In the following example, there's a single client.</span></span> <span data-ttu-id="231ed-157">Der Clientname entspricht dem App-Namen, und er wird durch Konventionen dem OAuth-`ClientId`-Parameter zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="231ed-157">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="231ed-158">Das Profil gibt den App-Typ an, der konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="231ed-158">The profile indicates the app type being configured.</span></span> <span data-ttu-id="231ed-159">Das Profil wird intern verwendet, um Konventionen zu etablieren, die den Konfigurationsprozess für den Server vereinfachen.</span><span class="sxs-lookup"><span data-stu-id="231ed-159">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="231ed-160">Der Platzhalter `{APP ASSEMBLY}` ist der Assemblyname der App (z. B. `BlazorSample.Client`).</span><span class="sxs-lookup"><span data-stu-id="231ed-160">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.Client`).</span></span>

## <a name="client-app-configuration"></a><span data-ttu-id="231ed-161">Client-App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="231ed-161">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="231ed-162">Authentifizierungspaket</span><span class="sxs-lookup"><span data-stu-id="231ed-162">Authentication package</span></span>

<span data-ttu-id="231ed-163">Wenn eine App erstellt wird, die einzelne Benutzerkonten (`Individual`) verwendet, erhält die App automatisch einen Paketverweis für das [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/)-Paket in der Projektdatei der App.</span><span class="sxs-lookup"><span data-stu-id="231ed-163">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package in the app's project file.</span></span> <span data-ttu-id="231ed-164">Das Paket stellt einige Primitive bereit, die der App beim Authentifizieren von Benutzern und beim Abrufen von Token zum Aufrufen geschützter APIs helfen.</span><span class="sxs-lookup"><span data-stu-id="231ed-164">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="231ed-165">Wenn Sie einer App eine Authentifizierung hinzufügen, fügen Sie das Paket manuell der Projektdatei der App hinzu:</span><span class="sxs-lookup"><span data-stu-id="231ed-165">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

### <a name="api-authorization-support"></a><span data-ttu-id="231ed-166">API-Autorisierungsunterstützung</span><span class="sxs-lookup"><span data-stu-id="231ed-166">API authorization support</span></span>

<span data-ttu-id="231ed-167">Die Unterstützung für das Authentifizieren von Benutzern wird im Dienstcontainer durch die Erweiterungsmethode integriert, die im Paket [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="231ed-167">The support for authenticating users is plugged into the service container by the extension method provided inside the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span> <span data-ttu-id="231ed-168">Diese Methode richtet die Dienste ein, die erforderlich sind, damit die App mit dem vorhandenen Autorisierungssystem interagiert.</span><span class="sxs-lookup"><span data-stu-id="231ed-168">This method sets up the services required by the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="231ed-169">Standardmäßig wird die Konfiguration für die App durch die Konvention von `_configuration/{client-id}` geladen.</span><span class="sxs-lookup"><span data-stu-id="231ed-169">By default, configuration for the app is loaded by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="231ed-170">Per Konvention wird die Client-ID auf den Assemblynamen der App festgelegt.</span><span class="sxs-lookup"><span data-stu-id="231ed-170">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="231ed-171">Diese URL kann so geändert werden, dass sie auf einen eigenen Endpunkt verweist, indem das Überladen mit Optionen aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="231ed-171">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="231ed-172">Imports-Datei</span><span class="sxs-lookup"><span data-stu-id="231ed-172">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="231ed-173">Indexseite</span><span class="sxs-lookup"><span data-stu-id="231ed-173">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="231ed-174">App-Komponente</span><span class="sxs-lookup"><span data-stu-id="231ed-174">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="231ed-175">RedirectToLogin-Komponente</span><span class="sxs-lookup"><span data-stu-id="231ed-175">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="231ed-176">LoginDisplay-Komponente</span><span class="sxs-lookup"><span data-stu-id="231ed-176">LoginDisplay component</span></span>

<span data-ttu-id="231ed-177">Die `LoginDisplay`-Komponente (*Shared/LoginDisplay.razor*) wird in der `MainLayout`-Komponente (*Shared/MainLayout.razor*) gerendert, und sie verwaltet die folgenden Verhaltensweisen:</span><span class="sxs-lookup"><span data-stu-id="231ed-177">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="231ed-178">Für authentifizierte Benutzer:</span><span class="sxs-lookup"><span data-stu-id="231ed-178">For authenticated users:</span></span>
  * <span data-ttu-id="231ed-179">Zeigt den aktuellen Benutzernamen an</span><span class="sxs-lookup"><span data-stu-id="231ed-179">Displays the current user name.</span></span>
  * <span data-ttu-id="231ed-180">Stellt einen Link zur Benutzerprofilseite in ASP.NET Core-Identity bereit</span><span class="sxs-lookup"><span data-stu-id="231ed-180">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="231ed-181">Bietet eine Schaltfläche zum Abmelden von der App</span><span class="sxs-lookup"><span data-stu-id="231ed-181">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="231ed-182">Für anonyme Benutzer:</span><span class="sxs-lookup"><span data-stu-id="231ed-182">For anonymous users:</span></span>
  * <span data-ttu-id="231ed-183">Bietet die Option zum Registrieren</span><span class="sxs-lookup"><span data-stu-id="231ed-183">Offers the option to register.</span></span>
  * <span data-ttu-id="231ed-184">Bietet die Option zur Anmeldung</span><span class="sxs-lookup"><span data-stu-id="231ed-184">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="231ed-185">Authentication-Komponente</span><span class="sxs-lookup"><span data-stu-id="231ed-185">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="231ed-186">FetchData-Komponente</span><span class="sxs-lookup"><span data-stu-id="231ed-186">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="231ed-187">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="231ed-187">Run the app</span></span>

<span data-ttu-id="231ed-188">Führen Sie die App aus dem Serverprojekt aus.</span><span class="sxs-lookup"><span data-stu-id="231ed-188">Run the app from the Server project.</span></span> <span data-ttu-id="231ed-189">Wenn Sie Visual Studio verwenden, haben Sie folgende beiden Möglichkeiten:</span><span class="sxs-lookup"><span data-stu-id="231ed-189">When using Visual Studio, either:</span></span>

* <span data-ttu-id="231ed-190">Wählen Sie in der Dropdownliste **Startprojekte** in der Symbolleiste die *Server-API-App* aus, und klicken Sie auf **Ausführen**.</span><span class="sxs-lookup"><span data-stu-id="231ed-190">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="231ed-191">Wählen Sie das Serverprojekt im **Projektmappen-Explorer** aus, und klicken Sie in der Symbolleiste auf **Ausführen**, oder starten Sie die App über das Menü **Debuggen**.</span><span class="sxs-lookup"><span data-stu-id="231ed-191">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

## <a name="name-and-role-claim-with-api-authorization"></a><span data-ttu-id="231ed-192">Namens- und Rollenanspruch mit API-Autorisierung</span><span class="sxs-lookup"><span data-stu-id="231ed-192">Name and role claim with API authorization</span></span>

### <a name="custom-user-factory"></a><span data-ttu-id="231ed-193">Benutzerdefinierte Benutzerfactory</span><span class="sxs-lookup"><span data-stu-id="231ed-193">Custom user factory</span></span>

<span data-ttu-id="231ed-194">Erstellen Sie in der Client-App eine benutzerdefinierte Benutzerfactory.</span><span class="sxs-lookup"><span data-stu-id="231ed-194">In the Client app, create a custom user factory.</span></span> Identity<span data-ttu-id="231ed-195">Server sendet mehrere Rollen als JSON-Array in einem einzelnen `role`-Anspruch.</span><span class="sxs-lookup"><span data-stu-id="231ed-195"> Server sends multiple roles as a JSON array in a single `role` claim.</span></span> <span data-ttu-id="231ed-196">Eine einzelne Rolle wird im Anspruch als Zeichenfolgenwert gesendet.</span><span class="sxs-lookup"><span data-stu-id="231ed-196">A single role is sent as a string value in the claim.</span></span> <span data-ttu-id="231ed-197">Die Factory erstellt einen einzelnen `role`-Anspruch für die einzelnen Rollen des Benutzers.</span><span class="sxs-lookup"><span data-stu-id="231ed-197">The factory creates an individual `role` claim for each of the user's roles.</span></span>

<span data-ttu-id="231ed-198">*CustomUserFactory.cs:*</span><span class="sxs-lookup"><span data-stu-id="231ed-198">*CustomUserFactory.cs*:</span></span>

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

<span data-ttu-id="231ed-199">Registrieren Sie in der Client-App die Factory in `Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="231ed-199">In the Client app, register the factory in `Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

<span data-ttu-id="231ed-200">Rufen Sie in der Server-App <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> für den Identity-Generator auf, der rollenbezogene Dienste hinzufügt:</span><span class="sxs-lookup"><span data-stu-id="231ed-200">In the Server app, call <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> on the Identity builder, which adds role-related services:</span></span>

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-identity-server"></a><span data-ttu-id="231ed-201">Konfigurieren von IdentityServer</span><span class="sxs-lookup"><span data-stu-id="231ed-201">Configure Identity Server</span></span>

<span data-ttu-id="231ed-202">Verwenden Sie **einen** der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="231ed-202">Use **one** of the following approaches:</span></span>

* [<span data-ttu-id="231ed-203">API-Autorisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="231ed-203">API authorization options</span></span>](#api-authorization-options)
* [<span data-ttu-id="231ed-204">Profildienst</span><span class="sxs-lookup"><span data-stu-id="231ed-204">Profile Service</span></span>](#profile-service)

#### <a name="api-authorization-options"></a><span data-ttu-id="231ed-205">API-Autorisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="231ed-205">API authorization options</span></span>

<span data-ttu-id="231ed-206">Führen Sie in der Server-App die folgenden Aktionen aus:</span><span class="sxs-lookup"><span data-stu-id="231ed-206">In the Server app:</span></span>

* <span data-ttu-id="231ed-207">Konfigurieren Sie IdentityServer so, dass der `name`- und der `role`-Anspruch in das ID-Token und das Zugriffstoken platziert werden.</span><span class="sxs-lookup"><span data-stu-id="231ed-207">Configure Identity Server to put the `name` and `role` claims into the ID token and access token.</span></span>
* <span data-ttu-id="231ed-208">Verhindern Sie die Standardzuordnung für Rollen im JWT-Tokenhandler.</span><span class="sxs-lookup"><span data-stu-id="231ed-208">Prevent the default mapping for roles in the JWT token handler.</span></span>

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

#### <a name="profile-service"></a><span data-ttu-id="231ed-209">Profildienst</span><span class="sxs-lookup"><span data-stu-id="231ed-209">Profile Service</span></span>

<span data-ttu-id="231ed-210">Erstellen Sie in der Server-App eine `ProfileService`-Implementierung.</span><span class="sxs-lookup"><span data-stu-id="231ed-210">In the Server app, create a `ProfileService` implementation.</span></span>

<span data-ttu-id="231ed-211">*ProfileService.cs:*</span><span class="sxs-lookup"><span data-stu-id="231ed-211">*ProfileService.cs*:</span></span>

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

<span data-ttu-id="231ed-212">Registrieren Sie in der Server-App den Profildienst in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="231ed-212">In the Server app, register the Profile Service in `Startup.ConfigureServices`:</span></span>

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a><span data-ttu-id="231ed-213">Verwenden der Autorisierungsmechanismen</span><span class="sxs-lookup"><span data-stu-id="231ed-213">Use authorization mechanisms</span></span>

<span data-ttu-id="231ed-214">Die Komponentenautorisierungsansätze in der Client-App sind Stand jetzt funktional.</span><span class="sxs-lookup"><span data-stu-id="231ed-214">In the Client app, component authorization approaches are functional at this point.</span></span> <span data-ttu-id="231ed-215">Jeder der Autorisierungsmechanismen bei den Komponenten kann eine Rolle verwenden, um den Benutzer zu autorisieren:</span><span class="sxs-lookup"><span data-stu-id="231ed-215">Any of the authorization mechanisms in components can use a role to authorize the user:</span></span>

* <span data-ttu-id="231ed-216">[AuthorizeView-Komponente](xref:blazor/security/index#authorizeview-component) (Beispiel: `<AuthorizeView Roles="admin">`)</span><span class="sxs-lookup"><span data-stu-id="231ed-216">[AuthorizeView component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="231ed-217">[`[Authorize]`-Attributanweisung](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Beispiel: `@attribute [Authorize(Roles = "admin")]`)</span><span class="sxs-lookup"><span data-stu-id="231ed-217">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="231ed-218">[Prozedurale Logik](xref:blazor/security/index#procedural-logic) (Beispiel: `if (user.IsInRole("admin")) { ... }`)</span><span class="sxs-lookup"><span data-stu-id="231ed-218">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="231ed-219">Mehrere Rollentests werden unterstützt:</span><span class="sxs-lookup"><span data-stu-id="231ed-219">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

<span data-ttu-id="231ed-220">`User.Identity.Name` wird in der Client-App mit dem Benutzernamen des Benutzers aufgefüllt. In der Regel handelt es sich dabei um die E-Mail-Adresse für die Anmeldung.</span><span class="sxs-lookup"><span data-stu-id="231ed-220">`User.Identity.Name` is populated in the Client app with the user's user name, which is usually their sign-in email address.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="231ed-221">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="231ed-221">Additional resources</span></span>

* [<span data-ttu-id="231ed-222">Bereitstellen für die Produktion</span><span class="sxs-lookup"><span data-stu-id="231ed-222">Deployment to Azure App Service</span></span>](xref:security/authentication/identity/spa#deploy-to-production)
* [<span data-ttu-id="231ed-223">Importieren eines Zertifikats aus Key Vault</span><span class="sxs-lookup"><span data-stu-id="231ed-223">Import a certificate from Key Vault (Azure documentation)</span></span>](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="231ed-224">Nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen in einer App mit einem sicheren Standardclient</span><span class="sxs-lookup"><span data-stu-id="231ed-224">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)

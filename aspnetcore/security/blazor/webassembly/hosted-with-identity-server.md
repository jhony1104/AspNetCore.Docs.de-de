---
title: Sichern einer Blazor ASP.NET gehosteten Core WebAssembly-App mit Identity Server
author: guardrex
description: So erstellen Blazor Sie eine neue gehostete App mit Authentifizierung aus Visual Studio, die ein [IdentityServer-Backend](https://identityserver.io/) verwendet
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: 832109530c4aac372fd75aa1a1d2edbe3768f55f
ms.sourcegitcommit: 1d8f1396ccc66a0c3fcb5e5f36ea29b50db6d92a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501280"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="85e7c-103">Sichern einer Blazor ASP.NET gehosteten Core WebAssembly-App mit Identity Server</span><span class="sxs-lookup"><span data-stu-id="85e7c-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="85e7c-104">Von [Javier Calvarro Nelson](https://github.com/javiercn) und Luke [Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="85e7c-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="85e7c-105">So erstellen Blazor Sie eine neue gehostete App in Visual Studio, die [IdentityServer](https://identityserver.io/) zum Authentifizieren von Benutzern und API-Aufrufen verwendet:</span><span class="sxs-lookup"><span data-stu-id="85e7c-105">To create a new Blazor hosted app in Visual Studio that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls:</span></span>

1. <span data-ttu-id="85e7c-106">Verwenden Sie Visual Studio, um eine neue \*\* Blazor WebAssembly-App\*\* zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="85e7c-106">Use Visual Studio to create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="85e7c-107">Weitere Informationen finden Sie unter <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="85e7c-107">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="85e7c-108">Wählen Sie im Dialogfeld **Erstellen einer neuen Blazor App** im Abschnitt **Authentifizierung** die Option **Ändern** aus.</span><span class="sxs-lookup"><span data-stu-id="85e7c-108">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="85e7c-109">Wählen Sie **Einzelne Benutzerkonten** gefolgt von **OK**aus.</span><span class="sxs-lookup"><span data-stu-id="85e7c-109">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="85e7c-110">Aktivieren Sie das Kontrollkästchen **ASP.NET gehosteten Core** im Abschnitt **Erweitert.**</span><span class="sxs-lookup"><span data-stu-id="85e7c-110">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="85e7c-111">Wählen Sie die Schaltfläche **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="85e7c-111">Select the **Create** button.</span></span>

<span data-ttu-id="85e7c-112">Um die App in einer Befehlsshell zu erstellen, führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="85e7c-112">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="85e7c-113">Um den Ausgabespeicherort anzugeben, der einen Projektordner erstellt, falls dieser nicht vorhanden ist, fügen `-o BlazorSample`Sie die Ausgabeoption in den Befehl mit einem Pfad ein (z. B. ).</span><span class="sxs-lookup"><span data-stu-id="85e7c-113">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="85e7c-114">Der Ordnername wird auch Teil des Projektnamens.</span><span class="sxs-lookup"><span data-stu-id="85e7c-114">The folder name also becomes part of the project's name.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="85e7c-115">Server-App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="85e7c-115">Server app configuration</span></span>

<span data-ttu-id="85e7c-116">In den folgenden Abschnitten werden Ergänzungen zum Projekt beschrieben, wenn authentifizierungsunterstützung enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="85e7c-116">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="85e7c-117">Startklasse</span><span class="sxs-lookup"><span data-stu-id="85e7c-117">Startup class</span></span>

<span data-ttu-id="85e7c-118">Die `Startup` Klasse hat die folgenden Ergänzungen:</span><span class="sxs-lookup"><span data-stu-id="85e7c-118">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="85e7c-119">In `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="85e7c-119">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="85e7c-120">Identität mit der Standardbenutzeroberfläche:</span><span class="sxs-lookup"><span data-stu-id="85e7c-120">Identity with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="85e7c-121">IdentityServer mit <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> einer zusätzlichen Hilfsmethode, die einige Standard-ASP.NET Core-Konventionen über IdentityServer einrichtet:</span><span class="sxs-lookup"><span data-stu-id="85e7c-121">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="85e7c-122">Authentifizierung mit <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> einer zusätzlichen Hilfsmethode, die die App so konfiguriert, dass JWT-Token überprüft werden, die von IdentityServer erstellt wurden:</span><span class="sxs-lookup"><span data-stu-id="85e7c-122">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="85e7c-123">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="85e7c-123">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="85e7c-124">Die Authentifizierungsmiddleware, die für die Überprüfung der Anforderungsanmeldeinformationen und das Festlegen des Benutzers für den Anforderungskontext zuständig ist:</span><span class="sxs-lookup"><span data-stu-id="85e7c-124">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="85e7c-125">Die IdentityServer-Middleware, die die OIDC-Endpunkte (Open ID Connect) verfügbar macht:</span><span class="sxs-lookup"><span data-stu-id="85e7c-125">The IdentityServer middleware that exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="85e7c-126">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="85e7c-126">AddApiAuthorization</span></span>

<span data-ttu-id="85e7c-127">Die <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> Hilfsmethode konfiguriert [IdentityServer](https://identityserver.io/) für ASP.NET Core-Szenarien.</span><span class="sxs-lookup"><span data-stu-id="85e7c-127">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="85e7c-128">IdentityServer ist ein leistungsstarkes und erweiterbares Framework für die Behandlung von Sicherheitsbedenken von Apps.</span><span class="sxs-lookup"><span data-stu-id="85e7c-128">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="85e7c-129">IdentityServer macht unnötige Komplexität für die häufigsten Szenarien verfügbar.</span><span class="sxs-lookup"><span data-stu-id="85e7c-129">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="85e7c-130">Folglich wird eine Reihe von Konventionen und Konfigurationsoptionen bereitgestellt, die wir als einen guten Ausgangspunkt betrachten.</span><span class="sxs-lookup"><span data-stu-id="85e7c-130">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="85e7c-131">Sobald sich Ihre Authentifizierungsanforderungen ändern, ist IdentityServer weiterhin mit voller Leistungsfähigkeit verfügbar, um die Authentifizierung an die Anforderungen einer App anzupassen.</span><span class="sxs-lookup"><span data-stu-id="85e7c-131">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="85e7c-132">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="85e7c-132">AddIdentityServerJwt</span></span>

<span data-ttu-id="85e7c-133">Die <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> Hilfsmethode konfiguriert ein Richtlinienschema für die App als Standardauthentifizierungshandler.</span><span class="sxs-lookup"><span data-stu-id="85e7c-133">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="85e7c-134">Die Richtlinie ist so konfiguriert, dass Identity alle Anforderungen verarbeiten kann, `/Identity`die an einen beliebigen Unterpfad im Identitäts-URL-Bereich weitergeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="85e7c-134">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="85e7c-135">Der <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> behandelt alle anderen Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="85e7c-135">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="85e7c-136">Darüber hinaus ist diese Methode:</span><span class="sxs-lookup"><span data-stu-id="85e7c-136">Additionally, this method:</span></span>

* <span data-ttu-id="85e7c-137">Registriert eine `{APPLICATION NAME}API` API-Ressource bei IdentityServer `{APPLICATION NAME}API`mit dem Standardbereich von .</span><span class="sxs-lookup"><span data-stu-id="85e7c-137">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="85e7c-138">Konfiguriert die JWT Bearer Token Middleware, um Token zu überprüfen, die von IdentityServer für die App ausgegeben wurden.</span><span class="sxs-lookup"><span data-stu-id="85e7c-138">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="85e7c-139">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="85e7c-139">WeatherForecastController</span></span>

<span data-ttu-id="85e7c-140">In `WeatherForecastController` der (*Controllers/WeatherForecastController.cs*) wird das [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) Attribut auf die Klasse angewendet.</span><span class="sxs-lookup"><span data-stu-id="85e7c-140">In the `WeatherForecastController` (*Controllers/WeatherForecastController.cs*), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="85e7c-141">Das Attribut gibt an, dass der Benutzer basierend auf der Standardrichtlinie autorisiert werden muss, um auf die Ressource zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="85e7c-141">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="85e7c-142">Die Standardautorisierungsrichtlinie ist für die Verwendung des Standardauthentifizierungsschemas konfiguriert, das für <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> das zuvor erwähnte Richtlinienschema eingerichtet wird.</span><span class="sxs-lookup"><span data-stu-id="85e7c-142">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> to the policy scheme that was mentioned earlier.</span></span> <span data-ttu-id="85e7c-143">Die Hilfsmethode konfiguriert <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> sich als Standardhandler für Anforderungen an die App.</span><span class="sxs-lookup"><span data-stu-id="85e7c-143">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="85e7c-144">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="85e7c-144">ApplicationDbContext</span></span>

<span data-ttu-id="85e7c-145">In `ApplicationDbContext` der (*Data/ApplicationDbContext.cs*) wird dasselbe <xref:Microsoft.EntityFrameworkCore.DbContext> in Identity <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> verwendet, mit der Ausnahme, dass sie sich auf das Schema für IdentityServer erstreckt.</span><span class="sxs-lookup"><span data-stu-id="85e7c-145">In the `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), the same <xref:Microsoft.EntityFrameworkCore.DbContext> is used in Identity with the exception that it extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="85e7c-146"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> wird von <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="85e7c-146"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="85e7c-147">Um die vollständige Kontrolle über das Datenbankschema zu erhalten, erben Sie von einer der verfügbaren Identitätsklassen, <xref:Microsoft.EntityFrameworkCore.DbContext> und konfigurieren Sie den Kontext so, dass das Identitätsschema durch Aufrufen der `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` `OnModelCreating` Methode eingeschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="85e7c-147">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="85e7c-148">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="85e7c-148">OidcConfigurationController</span></span>

<span data-ttu-id="85e7c-149">Im `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*) wird der Clientendpunkt bereitgestellt, um OIDC-Parameter zu bedienen.</span><span class="sxs-lookup"><span data-stu-id="85e7c-149">In the `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="85e7c-150">App-Einstellungsdateien</span><span class="sxs-lookup"><span data-stu-id="85e7c-150">App settings files</span></span>

<span data-ttu-id="85e7c-151">In der App-Einstellungsdatei (*appsettings.json* `IdentityServer` ) am Projektstamm wird im Abschnitt die Liste der konfigurierten Clients beschrieben.</span><span class="sxs-lookup"><span data-stu-id="85e7c-151">In the app settings file (*appsettings.json*) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="85e7c-152">Im folgenden Beispiel gibt es einen einzelnen Client.</span><span class="sxs-lookup"><span data-stu-id="85e7c-152">In the following example, there's a single client.</span></span> <span data-ttu-id="85e7c-153">Der Clientname entspricht dem App-Namen und wird durch `ClientId` Konvention dem OAuth-Parameter zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="85e7c-153">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="85e7c-154">Das Profil gibt den konfigurierten App-Typ an.</span><span class="sxs-lookup"><span data-stu-id="85e7c-154">The profile indicates the app type being configured.</span></span> <span data-ttu-id="85e7c-155">Das Profil wird intern verwendet, um Konventionen zu unterstützen, die den Konfigurationsprozess für den Server vereinfachen.</span><span class="sxs-lookup"><span data-stu-id="85e7c-155">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "BlazorApplicationWithAuthentication.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="85e7c-156">In der App-Einstellungsdatei für die Entwicklungsumgebung *(Appsettings. Development.json*) im Projektstamm `IdentityServer` beschreibt der Abschnitt den Schlüssel, der zum Signieren von Token verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="85e7c-156">In the Development environment app settings file (*appsettings.Development.json*) at the project root, the `IdentityServer` section describes the key used to sign tokens.</span></span> <!-- When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section. -->

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="85e7c-157">Client-App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="85e7c-157">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="85e7c-158">Authentifizierungspaket</span><span class="sxs-lookup"><span data-stu-id="85e7c-158">Authentication package</span></span>

<span data-ttu-id="85e7c-159">Wenn eine App erstellt wird,`Individual`um einzelne Benutzerkonten ( zu `Microsoft.AspNetCore.Components.WebAssembly.Authentication` verwenden), erhält die App automatisch eine Paketreferenz für das Paket in der Projektdatei der App.</span><span class="sxs-lookup"><span data-stu-id="85e7c-159">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="85e7c-160">Das Paket stellt eine Reihe von Primitiven bereit, die der App helfen, Benutzer zu authentifizieren und Token zum Aufrufen geschützter APIs zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="85e7c-160">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="85e7c-161">Wenn Sie einer App eine Authentifizierung hinzufügen, fügen Sie das Paket manuell zur Projektdatei der App hinzu:</span><span class="sxs-lookup"><span data-stu-id="85e7c-161">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="85e7c-162">Ersetzen `{VERSION}` Sie im vorherigen Paketverweis `Microsoft.AspNetCore.Blazor.Templates` durch die <xref:blazor/get-started> im Artikel gezeigte Version des Pakets.</span><span class="sxs-lookup"><span data-stu-id="85e7c-162">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="85e7c-163">UNTERSTÜTZUNG für API-Autorisierungen</span><span class="sxs-lookup"><span data-stu-id="85e7c-163">API authorization support</span></span>

<span data-ttu-id="85e7c-164">Die Unterstützung für die Authentifizierung von Benutzern wird durch die `Microsoft.AspNetCore.Components.WebAssembly.Authentication` im Paket bereitgestellte Erweiterungsmethode an den Dienstcontainer angeschlossen.</span><span class="sxs-lookup"><span data-stu-id="85e7c-164">The support for authenticating users is plugged into the service container by the extension method provided inside the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="85e7c-165">Diese Methode richtet alle Dienste ein, die für die App erforderlich sind, um mit dem vorhandenen Autorisierungssystem zu interagieren.</span><span class="sxs-lookup"><span data-stu-id="85e7c-165">This method sets up all the services needed for the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="85e7c-166">Standardmäßig wird die Konfiguration für die App `_configuration/{client-id}`nach Konvention aus geladen.</span><span class="sxs-lookup"><span data-stu-id="85e7c-166">By default, it loads the configuration for the app by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="85e7c-167">Konventionell wird die Client-ID auf den Assemblynamen der App festgelegt.</span><span class="sxs-lookup"><span data-stu-id="85e7c-167">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="85e7c-168">Diese URL kann geändert werden, um auf einen separaten Endpunkt zu verweisen, indem die Überladung mit Optionen aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="85e7c-168">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="85e7c-169">Imports-Datei</span><span class="sxs-lookup"><span data-stu-id="85e7c-169">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="85e7c-170">Indexseite</span><span class="sxs-lookup"><span data-stu-id="85e7c-170">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="85e7c-171">App-Komponente</span><span class="sxs-lookup"><span data-stu-id="85e7c-171">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="85e7c-172">RedirectToLogin-Komponente</span><span class="sxs-lookup"><span data-stu-id="85e7c-172">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="85e7c-173">LoginDisplay-Komponente</span><span class="sxs-lookup"><span data-stu-id="85e7c-173">LoginDisplay component</span></span>

<span data-ttu-id="85e7c-174">Die `LoginDisplay` Komponente (*Shared/LoginDisplay.razor*) `MainLayout` wird in der Komponente (*Shared/MainLayout.razor*) gerendert und verwaltet die folgenden Verhaltensweisen:</span><span class="sxs-lookup"><span data-stu-id="85e7c-174">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="85e7c-175">Für authentifizierte Benutzer:</span><span class="sxs-lookup"><span data-stu-id="85e7c-175">For authenticated users:</span></span>
  * <span data-ttu-id="85e7c-176">Zeigt den aktuellen Benutzernamen an.</span><span class="sxs-lookup"><span data-stu-id="85e7c-176">Displays the current user name.</span></span>
  * <span data-ttu-id="85e7c-177">Bietet einen Link zur Benutzerprofilseite in ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="85e7c-177">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="85e7c-178">Bietet eine Schaltfläche zum Abmelden aus der App.</span><span class="sxs-lookup"><span data-stu-id="85e7c-178">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="85e7c-179">Für anonyme Benutzer:</span><span class="sxs-lookup"><span data-stu-id="85e7c-179">For anonymous users:</span></span>
  * <span data-ttu-id="85e7c-180">Bietet die Möglichkeit, sich zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="85e7c-180">Offers the option to register.</span></span>
  * <span data-ttu-id="85e7c-181">Bietet die Möglichkeit, sich anzumelden.</span><span class="sxs-lookup"><span data-stu-id="85e7c-181">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="85e7c-182">Authentifizierungskomponente</span><span class="sxs-lookup"><span data-stu-id="85e7c-182">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="85e7c-183">FetchData-Komponente</span><span class="sxs-lookup"><span data-stu-id="85e7c-183">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="85e7c-184">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="85e7c-184">Run the app</span></span>

<span data-ttu-id="85e7c-185">Führen Sie die App aus dem Serverprojekt aus.</span><span class="sxs-lookup"><span data-stu-id="85e7c-185">Run the app from the Server project.</span></span> <span data-ttu-id="85e7c-186">Wenn Sie Visual Studio verwenden, wählen Sie das Serverprojekt im **Projektmappen-Explorer** aus, und wählen Sie die Schaltfläche **Ausführen** in der Symbolleiste aus, oder starten Sie die App im **Menü Debug.**</span><span class="sxs-lookup"><span data-stu-id="85e7c-186">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="85e7c-187">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="85e7c-187">Additional resources</span></span>

* [<span data-ttu-id="85e7c-188">Zusätzliche Zugriffstoken anfordern</span><span class="sxs-lookup"><span data-stu-id="85e7c-188">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)

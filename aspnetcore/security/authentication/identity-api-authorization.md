---
title: Einführung in die Authentifizierung für Single Page Applications on ASP.NET Core
author: ''
description: Verwenden Sie die Identität mit einer einseitigen Anwendung gehostet in einer ASP.NET Core-app.
ms.author: ''
ms.date: 03/05/2018
uid: security/authentication/identity/spa
ms.openlocfilehash: cf04ec1ff0ae9afea066fd1864ab0a7956ace32c
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57346751"
---
# <a name="authentication-and-authorization-for-spas"></a><span data-ttu-id="c96b7-103">Authentifizierung und Autorisierung für SPAs</span><span class="sxs-lookup"><span data-stu-id="c96b7-103">Authentication and authorization for SPAs</span></span>

<span data-ttu-id="c96b7-104">Unterstützung für die Authentifizierung in einseitigen Anwendungen verwenden unsere neue Unterstützung für die Autorisierung für API-werden in ASP.NET 3.0 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="c96b7-104">In ASP.NET 3.0 we are introducing support for authentication in single page applications using our new support for API authorization.</span></span> <span data-ttu-id="c96b7-105">Diese Unterstützung basiert auf einer Kombination von ASP.NET Core-Identität für die Authentifizierung und das Speichern von Benutzern und Identity Server für die Implementierung des Open ID Connect.</span><span class="sxs-lookup"><span data-stu-id="c96b7-105">This support is based on a combination of ASP.NET Core Identity for authenticating and storing users and Identity Server for implementing Open ID Connect.</span></span>

<span data-ttu-id="c96b7-106">Wir haben einen neuen Authentifizierungsparameter hinzugefügt, um unsere Angular und React-Vorlagen, die die Authentifizierungsparameter in unserer Mvc und Razor-Seiten-Vorlagen mit ähnelt zulässige Werte "None" und "Einzeln".</span><span class="sxs-lookup"><span data-stu-id="c96b7-106">We have added a new authentication parameter to our Angular and React templates that is similar to the authentication parameter in our mvc and razor pages templates with allowed values 'None' and 'Individual'.</span></span>

## <a name="create-an-angular-app-with-api-authorization-support"></a><span data-ttu-id="c96b7-107">Erstellen Sie eine Angular-app mit Unterstützung für die Autorisierung-API</span><span class="sxs-lookup"><span data-stu-id="c96b7-107">Create an Angular app with API authorization support</span></span>

<span data-ttu-id="c96b7-108">Um eine neue Angular-app mit Unterstützung für die Authentifizierung und Autorisierung von Benutzern zu erstellen, öffnen Sie eine Befehlsshell, und führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="c96b7-108">To create a new Angular app with support for authentication and authorization of users, open a command shell and run the following command:</span></span>

```console
dotnet new angular -o <output_directory_name> -au Individual
```

<span data-ttu-id="c96b7-109">Der vorherige Befehl erstellt eine ASP.NET Core-app mit einem *ClientApp* Verzeichnis mit der Angular-app.</span><span class="sxs-lookup"><span data-stu-id="c96b7-109">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the Angular app.</span></span>

## <a name="create-a-react-app-with-api-authorization-support"></a><span data-ttu-id="c96b7-110">Erstellen Sie eine React-app mit Unterstützung für die Autorisierung-API</span><span class="sxs-lookup"><span data-stu-id="c96b7-110">Create a React app with API authorization support</span></span>

<span data-ttu-id="c96b7-111">Um eine neue React-app mit Unterstützung für die Authentifizierung und Autorisierung von Benutzern zu erstellen, öffnen Sie eine Befehlsshell, und führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="c96b7-111">To create a new React app with support for authentication and authorization of users, open a command shell and run the following command:</span></span>

```console
dotnet new react -o <output_directory_name> -au Individual
```

<span data-ttu-id="c96b7-112">Der vorherige Befehl erstellt eine ASP.NET Core-app mit einem *ClientApp* Verzeichnis mit der React-app.</span><span class="sxs-lookup"><span data-stu-id="c96b7-112">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the React app.</span></span>

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a><span data-ttu-id="c96b7-113">Allgemeine Beschreibung von ASP.NET Core-Komponenten der app</span><span class="sxs-lookup"><span data-stu-id="c96b7-113">General description of the ASP.NET Core components of the app</span></span>

<span data-ttu-id="c96b7-114">Es gibt mehrere Erweiterungen für das Projekt auf, wenn wir Unterstützung für die Authentifizierung schließen:</span><span class="sxs-lookup"><span data-stu-id="c96b7-114">There are several additions to the project when we include support for authentication:</span></span>

### <a name="startup-class"></a><span data-ttu-id="c96b7-115">Startup-Klasse</span><span class="sxs-lookup"><span data-stu-id="c96b7-115">Startup class</span></span>

<span data-ttu-id="c96b7-116">Wenn wir uns den Code in der Startup-Klasse, die unten ansehen können die folgenden Einschlüsse Danke, dass:</span><span class="sxs-lookup"><span data-stu-id="c96b7-116">If we look at the code in the Startup class below we can appreciate the following inclusions:</span></span>
* <span data-ttu-id="c96b7-117">In `public void ConfigureServices(IServiceCollection services)`:</span><span class="sxs-lookup"><span data-stu-id="c96b7-117">Inside `public void ConfigureServices(IServiceCollection services)`:</span></span>
  * <span data-ttu-id="c96b7-118">Die Identität mit der standardmäßigen UI.</span><span class="sxs-lookup"><span data-stu-id="c96b7-118">Identity with the default UI.</span></span>
    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
      options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
      .AddDefaultUI(UIFramework.Bootstrap4)
      .AddEntityFrameworkStores<ApplicationDbContext>();
    ```
  * <span data-ttu-id="c96b7-119">Identitätsserver eine zusätzliche AddApiAuthorization-Hilfsmethode dieses Setups einige Konventionen von ASP.NET über Identity Server Standard.</span><span class="sxs-lookup"><span data-stu-id="c96b7-119">Identity Server with an additional AddApiAuthorization helper method that setups some default ASP.NET Conventions on top of Identity Server.</span></span>
    ```csharp
    services.AddIdentityServer()
      .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```
  * <span data-ttu-id="c96b7-120">Authentifizierung mit einer zusätzlichen AddIdentityServerJwt-Hilfsmethode, die die Anwendung zum Überprüfen von Jwt-Tokens erstellt, die von Identity Server konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="c96b7-120">Authentication with an additional AddIdentityServerJwt helper method that configures the application to validate Jwt tokens produced by Identity Server.</span></span> 
    ```csharp
    services.AddAuthentication()
      .AddIdentityServerJwt();
    ```
* <span data-ttu-id="c96b7-121">In `public void Configure(IApplicationBuilder app)`:</span><span class="sxs-lookup"><span data-stu-id="c96b7-121">Inside `public void Configure(IApplicationBuilder app)`:</span></span>
  * <span data-ttu-id="c96b7-122">Die authentifizierungsmiddleware, die für die Anmeldeinformationen in der eingehenden Anforderung überprüft und Festlegen des Benutzers im Kontext der Anforderung verantwortlich ist.</span><span class="sxs-lookup"><span data-stu-id="c96b7-122">The authentication middleware that is responsible for validating the credentials in the incoming request and setting the user on the request context.</span></span>
    ```csharp
    app.UseAuthentication();
    ```
  * <span data-ttu-id="c96b7-123">Die Identity Server-Middleware, die die Open ID Connect-Endpunkte verfügbar macht.</span><span class="sxs-lookup"><span data-stu-id="c96b7-123">The identity server middleware that exposes the Open ID Connect endpoints.</span></span>
    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="c96b7-124">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="c96b7-124">AddApiAuthorization</span></span> 
<span data-ttu-id="c96b7-125">Diese Hilfsmethode konfiguriert die Identity-Server, um unsere unterstützte Konfiguration zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="c96b7-125">This helper method configures Identity Server to use our supported configuration.</span></span> <span data-ttu-id="c96b7-126">Identitätsserver ist ein sehr leistungsfähigen und erweiterbaren Framework für die Behandlung der Anwendung die Sicherheitsrisiken, aber zur gleichen Zeit, die einen Großteil der Komplexität verfügbar macht, die wir nicht für die häufigsten Szenarien kennen müssen, also wählen wir einen Satz von Konventionen und Konfigurationsoptionen für Sie, die wir zu berücksichtigen sind ein guter Ausgangspunkt.</span><span class="sxs-lookup"><span data-stu-id="c96b7-126">Identity Server is a very powerful and extensible framework for handling application security concerns but at the same time that exposes a lot of complexity that we don't need to know about for the most common scenarios, so we choose a set of conventions and configuration options for you that we consider are a good starting point.</span></span> <span data-ttu-id="c96b7-127">Nach Bedarf für die Authentifizierung ist das volle Potenzial von Identity Server weiterhin zur Verfügung, damit Sie sie entsprechend Ihren Anforderungen anpassen können.</span><span class="sxs-lookup"><span data-stu-id="c96b7-127">Once your authentication needs change the full power of Identity Server is still available to you so you can customize it to suit your needs.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="c96b7-128">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="c96b7-128">AddIdentityServerJwt</span></span>
<span data-ttu-id="c96b7-129">Diese Hilfsmethode wird ein Schema der Richtlinie für die Anwendung als der Standardhandler für die Authentifizierung konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="c96b7-129">This helper method configures a policy scheme for the application as the default authentication handler.</span></span> <span data-ttu-id="c96b7-130">Die Richtlinie ist so konfiguriert, dass die Identität, die alle Anforderungen zu verarbeiten, die zu jedem untergeordneten Pfad im Bereich der Url des Identitätsanbieters führen können "/ Identity" und die JwtBearerHandler alle anderen Anforderungen verarbeiten zu können.</span><span class="sxs-lookup"><span data-stu-id="c96b7-130">The policy is configured to let identity handle all the requests that go to any subpath in the Identity url space "/Identity" and to let the JwtBearerHandler handle all other requests.</span></span>
<span data-ttu-id="c96b7-131">Addionally, die diese Methode registriert eine `<<ApplicationName>>API` -API-Ressource mit identitätsserver mit einem Standardbereich des `<<ApplicationName>>API` und konfiguriert die JWT-Trägertoken-Middleware zum Überprüfen von Identity Server für die Anwendung ausgestellten Tokens.</span><span class="sxs-lookup"><span data-stu-id="c96b7-131">Addionally this method registers an `<<ApplicationName>>API` Api resource with identity server with a default scope of `<<ApplicationName>>API` and configures the JWT Bearer token middleware to validate tokens issued by Identity Server for the application.</span></span>

### <a name="sampledatacontroller"></a><span data-ttu-id="c96b7-132">SampleDataController</span><span class="sxs-lookup"><span data-stu-id="c96b7-132">SampleDataController</span></span>
<span data-ttu-id="c96b7-133">Wenn Sie die Datei Controllers\SampleDataController.cs betrachten können wir verfolgen die `[Authorize]` -Attribut nicht angewendet werden, auf die Klasse, der angibt, dass der Benutzer autorisiert werden muss, basierend auf die Standardrichtlinie auf die Ressource zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="c96b7-133">If we look at the file Controllers\SampleDataController.cs we can observe the `[Authorize]` attribute applied to the class that indicates that the user needs to be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="c96b7-134">Die Standardrichtlinie für die Autorisierung konfiguriert werden, um das Standardschema für die Authentifizierung verwenden, die vom eingerichtet ist, geschieht `AddIdentityServerJwt` mit dem Schema der Richtlinie, die wir weiter oben erwähnt, wodurch die JwtBearer-Handler konfiguriert, indem diese Hilfsmethode den Standardhandler für Anforderungen an die app.</span><span class="sxs-lookup"><span data-stu-id="c96b7-134">The default authorization policy happens to be configured to use the default authentication scheme which is set up by `AddIdentityServerJwt` to the policy scheme that we mentioned above, making the JwtBearer handler configured by such helper method the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="c96b7-135">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="c96b7-135">ApplicationDbContext</span></span>
<span data-ttu-id="c96b7-136">Wenn Sie die Datei im Data\ApplicationDbContext.cs betrachten sehen die gleichen "DbContext" wir in die Identität mit der Ausnahme verwenden, dass es sich um ApiAuthorizationDbContext (einen stärker abgeleiteten Klasse von IdentityDbContext) erweitert das Schema für Identity Server enthält.</span><span class="sxs-lookup"><span data-stu-id="c96b7-136">If we look at the file in Data\ApplicationDbContext.cs we can see the same DbContext we use in identity with the exception that it extends ApiAuthorizationDbContext (a more derived class from IdentityDbContext) to include the schema for Identity Server.</span></span>
<span data-ttu-id="c96b7-137">Wenn Vollzugriff auf das Datenbankschema sollten wir können einfach eine der verfügbaren Identity "DbContext"-Klassen erben und konfigurieren Sie den Kontext, um das identitätsschema durch Aufrufen von aufzunehmen `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` auf die `OnModelCreating` Methode.</span><span class="sxs-lookup"><span data-stu-id="c96b7-137">If we want full control of the database schema we can simply inherit from one of the available Identity DbContext classes and configure the context to include the identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` on the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="c96b7-138">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="c96b7-138">OidcConfigurationController</span></span>
<span data-ttu-id="c96b7-139">Wenn sehen wir uns an die Datei Controllers\OidcConfigurationController.cs, wie Sie sehen den Endpunkt, dass wir einsatzbesprechungen die OIDC-Parameter bereitstellen, die der Client verwenden muss.</span><span class="sxs-lookup"><span data-stu-id="c96b7-139">If we look at the file Controllers\OidcConfigurationController.cs we can see the endpoint that we stand-up to serve the OIDC parameters that the client needs to use.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="c96b7-140">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="c96b7-140">appsettings.json</span></span>
<span data-ttu-id="c96b7-141">Wenn Sie die Datei "appsettings.json" im Stammverzeichnis des Projekts betrachten, wie Sie sehen einen neuen `IdentityServer` Abschnitt mit die Liste der Beschreibungen konfigurierten Clients und wir können sehen, dass es ein einzelnen Client.</span><span class="sxs-lookup"><span data-stu-id="c96b7-141">If we look at the appsettings.json file on the root of the project, we can see a new `IdentityServer` section that describes the list of configured clients and we can see that there is a single client.</span></span> <span data-ttu-id="c96b7-142">Der Name des Clients entspricht dem Namen der Anwendung und gemäß der Konvention an den oAuth-Client-ID-Parameter zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="c96b7-142">The name of the client corresponds to the name of the application and is mapped by convention to the oAuth ClientId parameter.</span></span> <span data-ttu-id="c96b7-143">Das Profil gibt an, welche Art von Anwendung, die wir konfigurieren, und wir es intern Laufwerk-Konventionen, die den Konfigurationsvorgang für den Server zu vereinfachen.</span><span class="sxs-lookup"><span data-stu-id="c96b7-143">The profile indicates what type of application we are configuring, and we use it internally to drive conventions that simplify the configuration process for the server.</span></span> <span data-ttu-id="c96b7-144">Es gibt mehrere Profile verfügbar im folgenden Abschnitt erläutert.</span><span class="sxs-lookup"><span data-stu-id="c96b7-144">There are several profiles available explained in the section below.</span></span>

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a><span data-ttu-id="c96b7-145">appsettings.Development.json</span><span class="sxs-lookup"><span data-stu-id="c96b7-145">appsettings.Development.json</span></span>
<span data-ttu-id="c96b7-146">Wenn Sie den "appSettings" betrachten. Development.JSON-Datei im Stammverzeichnis des Projekts, wie Sie sehen einen neuen `IdentityServer` Abschnitt, in dem den Schlüssel wird beschrieben, wir zum Signieren von Token verwenden.</span><span class="sxs-lookup"><span data-stu-id="c96b7-146">If we look at the appsettings.Development.json file on the root of the project, we can see a new `IdentityServer` section that describes the key we are using to sign tokens.</span></span> <span data-ttu-id="c96b7-147">Wenn Sie in der produktionsumgebung bereitstellen muss ein Schlüssel bereitgestellt werden, zusammen mit der Anwendung bereitgestellt wird, wie nachstehend beschrieben.</span><span class="sxs-lookup"><span data-stu-id="c96b7-147">When deploying to production a key needs to be provisioned and deployed alongside the application as explained below.</span></span>

```json
  "IdentityServer": {
    "Key": {
      "Type": "Development"
    }
  }
}
```

## <a name="general-description-of-the-angular-application"></a><span data-ttu-id="c96b7-148">Allgemeine Beschreibung der Angular-Anwendung</span><span class="sxs-lookup"><span data-stu-id="c96b7-148">General description of the Angular application</span></span>
<span data-ttu-id="c96b7-149">Die Unterstützung für die Authentifizierung und Autorisierung der API in der Angular-Vorlage befindet sich in eine eigene Angular-Modul.</span><span class="sxs-lookup"><span data-stu-id="c96b7-149">The support for authentication and API authorization in the Angular template lives in its own Angular module.</span></span> <span data-ttu-id="c96b7-150">Unter ClientApp\src\api-Autorisierung, und es besteht aus den folgenden Elementen:</span><span class="sxs-lookup"><span data-stu-id="c96b7-150">Under ClientApp\src\api-authorization and it is composed of the following elements:</span></span>
* <span data-ttu-id="c96b7-151">3-Komponenten:</span><span class="sxs-lookup"><span data-stu-id="c96b7-151">3 Components:</span></span>
  * <span data-ttu-id="c96b7-152">Login-Komponente: Behandelt die Anmeldung für die app an.</span><span class="sxs-lookup"><span data-stu-id="c96b7-152">Login component: Handles the login flow for the app.</span></span>
  * <span data-ttu-id="c96b7-153">Logout-Komponente: Verarbeitet die Abmeldung Flow für die app an.</span><span class="sxs-lookup"><span data-stu-id="c96b7-153">Logout component: Handles the logout flow for the app.</span></span>
  * <span data-ttu-id="c96b7-154">Login-Menü-Komponente: Ein Widget, das zeigt den aktuellen authentifizierten Benutzers mit Links zum Benutzerprofil zu verwalten und Abmelden oder links zum Anmelden oder registrieren, wenn der Benutzer nicht authentifiziert ist.</span><span class="sxs-lookup"><span data-stu-id="c96b7-154">Login menu component: A widget that displays the current authenticated user with links to manage the user profile and log out or links to log in or register when the user is not authenticated.</span></span>
* <span data-ttu-id="c96b7-155">Eine Route Guard `AuthorizeGuard` , die hinzugefügt werden können, um die sprachliche und erfordert, dass einen Benutzer authentifiziert werden, bevor der Zugriff auf die Route.</span><span class="sxs-lookup"><span data-stu-id="c96b7-155">A route guard `AuthorizeGuard` that can be added to routes and requires a user to be authenticated before visiting the route.</span></span>
* <span data-ttu-id="c96b7-156">Ein http-Interceptor `AuthorizeInterceptor` , die das Zugriffstoken auf ausgehenden HTTP-Anforderungen, die für die API aus, wenn der Benutzer authentifiziert ist angefügt.</span><span class="sxs-lookup"><span data-stu-id="c96b7-156">An http interceptor `AuthorizeInterceptor` that attaches the access token to outgoing HTTP requests targeting the API when the user is authenticated.</span></span>
* <span data-ttu-id="c96b7-157">Ein Dienst `AuthorizeService` , behandelt die Details auf niedriger Ebene des Authentifizierungsprozesses und Informationen zu den authentifizierten Benutzer auf den Rest der Anwendung für die Nutzung verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="c96b7-157">A service `AuthorizeService` that handles the lower level details of the authentication process and exposes information about the authenticated user to the rest of the application for consumption.</span></span>
* <span data-ttu-id="c96b7-158">Ein angular-Modul, die Routen, die die Authentifizierung Teile der Anwendung definiert und verfügbar macht, die Anmeldung im Menü-Komponente, den Interceptor, den Wächter und der Dienst für die Nutzung vom Rest der Anwendung.</span><span class="sxs-lookup"><span data-stu-id="c96b7-158">An angular module that defines routes associated with the authentication parts of the application and exposes the login menu component, the interceptor, the guard and the service for consumption from the rest of the application.</span></span>

## <a name="general-description-of-the-react-application"></a><span data-ttu-id="c96b7-159">Allgemeine Beschreibung der React-Anwendung</span><span class="sxs-lookup"><span data-stu-id="c96b7-159">General description of the React application</span></span>
<span data-ttu-id="c96b7-160">Die Unterstützung für die Authentifizierung und Autorisierung der API in der React-Vorlage befindet sich unter ClientApp\src\components\api-Authorization\ und besteht aus den folgenden Elementen:</span><span class="sxs-lookup"><span data-stu-id="c96b7-160">The support for authentication and API authorization in the React template lives under ClientApp\src\components\api-authorization\ and it is composed of the following elements:</span></span>
* <span data-ttu-id="c96b7-161">4-Komponenten:</span><span class="sxs-lookup"><span data-stu-id="c96b7-161">4 Components:</span></span>
  * <span data-ttu-id="c96b7-162">Login-Komponente: Behandelt die Anmeldung für die app an.</span><span class="sxs-lookup"><span data-stu-id="c96b7-162">Login component: Handles the login flow for the app.</span></span>
  * <span data-ttu-id="c96b7-163">Logout-Komponente: Verarbeitet die Abmeldung Flow für die app an.</span><span class="sxs-lookup"><span data-stu-id="c96b7-163">Logout component: Handles the logout flow for the app.</span></span>
  * <span data-ttu-id="c96b7-164">Login-Menü-Komponente: Ein Widget, das zeigt den aktuellen authentifizierten Benutzers mit Links zum Benutzerprofil zu verwalten und Abmelden oder links zum Anmelden oder registrieren, wenn der Benutzer nicht authentifiziert ist.</span><span class="sxs-lookup"><span data-stu-id="c96b7-164">Login menu component: A widget that displays the current authenticated user with links to manage the user profile and log out or links to log in or register when the user is not authenticated.</span></span>
  * <span data-ttu-id="c96b7-165">AuthorizeRoute: Eine Route-Komponente, die ein Benutzer authentifiziert werden, vor dem Rendern der Komponente benötigt, die in der Component-Parameter angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="c96b7-165">AuthorizeRoute: A route component that requires a user to be authenticated before rendering the component indicated in the Component parameter.</span></span>
  * <span data-ttu-id="c96b7-166">Eine exportierte `authService` Instanz der Klasse `AuthorizeService` , behandelt die Details auf niedriger Ebene des Authentifizierungsprozesses und Informationen zu den authentifizierten Benutzer auf den Rest der Anwendung für die Nutzung verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="c96b7-166">An exported `authService` instance of class `AuthorizeService` that handles the lower level details of the authentication process and exposes information about the authenticated user to the rest of the application for consumption.</span></span>

<span data-ttu-id="c96b7-167">Nun, da wir die Hauptkomponenten der Lösung gesehen haben, können wir ein bestimmtes Erscheinungsbild auf einzelne Szenarien für die Anwendung ausführen:</span><span class="sxs-lookup"><span data-stu-id="c96b7-167">Now that we've seen the main components of the solution, we can take a specific look at individual scenarios for the application:</span></span>

## <a name="requiring-authorization-on-a-new-api"></a><span data-ttu-id="c96b7-168">Eine Autorisierung auf eine neue API erfordert</span><span class="sxs-lookup"><span data-stu-id="c96b7-168">Requiring authorization on a new API</span></span>
<span data-ttu-id="c96b7-169">Das System ist aus, die es einfach, eine Autorisierung erforderlich ist, für die neuen APIs machen konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="c96b7-169">The system is configured out of the box to make it trivial to require authorization for new APIs.</span></span> <span data-ttu-id="c96b7-170">Zu diesem Zweck erstellen Sie einen neuen Controller, und fügen die `[Authorize]` -Attribut zur Controllerklasse oder auf eine beliebige Aktion im Controller.</span><span class="sxs-lookup"><span data-stu-id="c96b7-170">In order to do so, simply create a new controller and add the `[Authorize]` attribute to the controller class or to any action within the controller.</span></span>

## <a name="protecting-a-client-side-route-angular"></a><span data-ttu-id="c96b7-171">Schützen eine Route für die clientseitige (Angular)</span><span class="sxs-lookup"><span data-stu-id="c96b7-171">Protecting a client-side route (Angular)</span></span>
<span data-ttu-id="c96b7-172">Schützen eine Client-Side-Route erfolgt durch Hinzufügen der Authorize-Guard zur Liste der Wächter ausgeführt wird, wenn Sie eine Route zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="c96b7-172">Protecting a client side route is done by adding the authorize guard to the list of guards to run when configuring a route.</span></span> <span data-ttu-id="c96b7-173">Als Beispiel sehen Sie, wie die Route Abrufen der Daten in der Haupt-app-angular-Modul konfiguriert ist:</span><span class="sxs-lookup"><span data-stu-id="c96b7-173">As an example you can see how the fetch-data route is configured within the main app angular module:</span></span>

```ts
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

<span data-ttu-id="c96b7-174">Es ist wichtig zu erwähnen, dass eine Route schützen nicht den tatsächlichen Endpunkt schützen (erfordert immer noch eine `[Authorize]` angewendet werden), aber es nur verhindert, dass den Benutzer auf die Route der angegebenen Client-Seite navigieren, wenn sie nicht authentifiziert ist.</span><span class="sxs-lookup"><span data-stu-id="c96b7-174">It is important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client side route when it is not authenticated.</span></span>

## <a name="authenticate-api-requests-angular"></a><span data-ttu-id="c96b7-175">Authentifizieren der API-Anforderungen (Angular)</span><span class="sxs-lookup"><span data-stu-id="c96b7-175">Authenticate API requests (Angular)</span></span>

<span data-ttu-id="c96b7-176">Authentifizieren von Anforderungen, für APIs, die auf Seite gehostet wird, dass die Anwendung erfolgt automatisch durch die Verwendung der HTTP-Client-Interceptor, die von der Anwendung definiert.</span><span class="sxs-lookup"><span data-stu-id="c96b7-176">Authenticating requests to APIs hosted along side the application is done automatically through the use of the HTTP client interceptor defined by the application.</span></span>

## <a name="protect-a-client-side-route-react"></a><span data-ttu-id="c96b7-177">Schützen Sie eine Route für die clientseitige (React)</span><span class="sxs-lookup"><span data-stu-id="c96b7-177">Protect a client-side route (React)</span></span>

<span data-ttu-id="c96b7-178">Schützen eine Client-Side-Route erfolgt mithilfe der Komponente AuthorizeRoute anstelle der einfachen Route-Komponente.</span><span class="sxs-lookup"><span data-stu-id="c96b7-178">Protecting a client side route is done by using the AuthorizeRoute component instead of the plain Route component.</span></span> <span data-ttu-id="c96b7-179">Als Beispiel sehen Sie, wie die Route Abrufen der Daten innerhalb der App-Komponente konfiguriert ist:</span><span class="sxs-lookup"><span data-stu-id="c96b7-179">As an example you can see how the fetch-data route is configured within the App component:</span></span>

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

<span data-ttu-id="c96b7-180">Es ist wichtig zu erwähnen, dass eine Route schützen nicht den tatsächlichen Endpunkt schützen (erfordert immer noch eine `[Authorize]` angewendet werden), aber es nur verhindert, dass den Benutzer auf die Route der angegebenen Client-Seite navigieren, wenn sie nicht authentifiziert ist.</span><span class="sxs-lookup"><span data-stu-id="c96b7-180">It is important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client side route when it is not authenticated.</span></span>

## <a name="authenticate-api-requests-react"></a><span data-ttu-id="c96b7-181">Authentifizieren der API-Anforderungen (React)</span><span class="sxs-lookup"><span data-stu-id="c96b7-181">Authenticate API requests (React)</span></span>

<span data-ttu-id="c96b7-182">Authentifizieren von Anforderungen mit React erfolgt durch das erste Importieren der `authService` -Instanz aus der `AuthorizeService` zum Abrufen des Zugriffstokens aus den AuthService und Anfügen an die Anforderung wie unten dargestellt.</span><span class="sxs-lookup"><span data-stu-id="c96b7-182">Authenticating requests with react is done by first importing the `authService` instance from the `AuthorizeService` and then retrieving the access token from the authService and attaching it to the request as shown below.</span></span> <span data-ttu-id="c96b7-183">In React-Komponenten. Dies erfolgt in der Regel in der ComponentDidMount Lebenszyklus-Methode oder als Ergebnis von Benutzerinteraktion.</span><span class="sxs-lookup"><span data-stu-id="c96b7-183">In react components this is typically done in the componentDidMount lifecycle method or as the result from some user interaction.</span></span>

### <a name="import-the-authservice-into-your-component"></a><span data-ttu-id="c96b7-184">Importieren Sie die AuthService in Ihre Komponente</span><span class="sxs-lookup"><span data-stu-id="c96b7-184">Import the authService into your component</span></span>

```js
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a><span data-ttu-id="c96b7-185">Abzurufen Sie, und fügen Sie das Zugriffstoken an die Antwort</span><span class="sxs-lookup"><span data-stu-id="c96b7-185">Retrieve and attach the access token to the response</span></span>

```js
async populateWeatherData() {
  const token = await authService.getAccessToken();
  const response = await fetch('api/SampleData/WeatherForecasts', {
    headers: !token ? {} : { 'Authorization': `Bearer ${token}` }
  });
  const data = await response.json();
  this.setState({ forecasts: data, loading: false });
}
```

## <a name="deploy-into-production"></a><span data-ttu-id="c96b7-186">In der Produktion bereitstellen</span><span class="sxs-lookup"><span data-stu-id="c96b7-186">Deploy into production</span></span>

<span data-ttu-id="c96b7-187">Um die Anwendung in die Produktion bereitstellen müssen wir mehrere Ressourcen bereitstellen:</span><span class="sxs-lookup"><span data-stu-id="c96b7-187">In order to deploy the application into production we need to provision several resources:</span></span>
* <span data-ttu-id="c96b7-188">Eine Datenbank zum Speichern der Identity-Benutzerkonten und die identitätsserver gewährt.</span><span class="sxs-lookup"><span data-stu-id="c96b7-188">A database to store the Identity user accounts and the identity server grants.</span></span>
* <span data-ttu-id="c96b7-189">Ein Production-Zertifikat zum Signieren von Token verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="c96b7-189">A production certificate to use for signing tokens.</span></span>
  * <span data-ttu-id="c96b7-190">Es gibt keine speziellen Anforderungen für dieses Zertifikat ein. Sie können ein selbstsigniertes Zertifikat oder ein Zertifikat mit einer Stammzertifizierungsstelle bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="c96b7-190">There are no specific requirements for this certificate; it can be a self-signed certificate or a certificate provisioned through a CA authority.</span></span>
  * <span data-ttu-id="c96b7-191">Sie können mithilfe von standard-Tools wie Powershell oder Openssl generiert werden.</span><span class="sxs-lookup"><span data-stu-id="c96b7-191">It can be generated through standard tools like powershell or openssl.</span></span>
  * <span data-ttu-id="c96b7-192">Sie können in den Zertifikatspeicher auf den Zielcomputern installiert wird, oder als PFX-Datei mit einem sicheren Kennwort bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="c96b7-192">It can be installed into the certificate store on the target machines or deployed as a pfx file with a strong password.</span></span>

### <a name="example-deploy-into-azure-websites"></a><span data-ttu-id="c96b7-193">Beispiel: Bereitstellen in Azure-Websites</span><span class="sxs-lookup"><span data-stu-id="c96b7-193">Example: Deploy into Azure Websites</span></span>

<span data-ttu-id="c96b7-194">In diesem Abschnitt werden wir zum Bereitstellen der Anwendung auf Azure-Websites mithilfe eines Zertifikats im Zertifikatspeicher gespeichert.</span><span class="sxs-lookup"><span data-stu-id="c96b7-194">In this section we are going to deploy the application to Azure websites using a certificate stored in the certificate store.</span></span> <span data-ttu-id="c96b7-195">Wir müssen so ändern Sie die Anwendung aus dem Zertifikatspeicher ein Zertifikat zu laden.</span><span class="sxs-lookup"><span data-stu-id="c96b7-195">We need to modify the application to load a certicate from the certificate store.</span></span> <span data-ttu-id="c96b7-196">Zu diesem Zweck muss unsere app Service-Plan mindestens auf Tarif "standard" sein, wenn wir in einem späteren Schritt konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="c96b7-196">To do so, our app service plan needs to be at least on the standard tier when we configure in a later step.</span></span> <span data-ttu-id="c96b7-197">In unserer Anwendung müssen wir einfach so ändern Sie im Abschnitt "Identity Server" auf "appSettings.JSON", um die wichtigsten Details enthalten:</span><span class="sxs-lookup"><span data-stu-id="c96b7-197">In our application we simply need to modify the IdentityServer section on appsettings.json to include the key details:</span></span>
```json
  "IdentityServer": {
    "Key": {
      "Type": "Store",
      "StoreName": "My",
      "StoreLocation": "CurrentUser",
      "Name": "CN=MyApplication"
    }
  }
}
```
* <span data-ttu-id="c96b7-198">Die Name-Eigenschaft für Zertifikat entspricht, mit dem distinguished Antragsteller des Zertifikats.</span><span class="sxs-lookup"><span data-stu-id="c96b7-198">The name property on certificate corresponds with the distinguished subject for the certificate.</span></span>
* <span data-ttu-id="c96b7-199">Der Speicherort darstellt, wo das Zertifikat aus ("CurrentUrser" oder "LocalMachine") geladen.</span><span class="sxs-lookup"><span data-stu-id="c96b7-199">The store location represents where to load the certificate from (CurrentUrser or LocalMachine).</span></span>
* <span data-ttu-id="c96b7-200">Der Speichername stellt es sich um den Namen des Zertifikatspeichers an, in dem das Zertifikat gespeichert wird, in diesem Fall, die sie in den persönlichen Speicher verweist.</span><span class="sxs-lookup"><span data-stu-id="c96b7-200">The store name represents the name of the certificate store where the certificate is stored, in this case it points to the personal user store.</span></span>

<span data-ttu-id="c96b7-201">Um auf Azure Websites bereitstellen, stellen Sie die app, die die Schritte in [Bereitstellen der app in Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) erforderlichen Azure-Ressourcen erstellen und Bereitstellen der app für die Produktion.</span><span class="sxs-lookup"><span data-stu-id="c96b7-201">To deploy to Azure Websites, deploy the app following the steps in [Deploy the app to Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) to create the necessary Azure resources and deploy the app to production.</span></span>

<span data-ttu-id="c96b7-202">Nachdem Sie auf diese Weise wird die Anwendung in Azure bereitgestellt wird, aber Sie ist noch nicht vollständig funktionsfähig, da wir benötigen Sie das Zertifikat von der Anwendung einrichten.</span><span class="sxs-lookup"><span data-stu-id="c96b7-202">After doing this, the application is deployed into Azure but is not yet completely functional as we still need to setup the certificate to be used by the application.</span></span> <span data-ttu-id="c96b7-203">Zu diesem Zweck müssen wir den Fingerabdruck des Zertifikats haben, werden wir verwenden, und führen die Schritte [laden Ihrer Zertifikate](/azure/app-service/app-service-web-ssl-cert-load#load-your-certificates).</span><span class="sxs-lookup"><span data-stu-id="c96b7-203">To do so, we need to have the thumbprint for the certificate we are going to use and follow the steps described in [Load your certificates](/azure/app-service/app-service-web-ssl-cert-load#load-your-certificates).</span></span>

<span data-ttu-id="c96b7-204">Während Schritten SSL erwähnt, besteht ein Abschnitt "Private Zertifikate" im Portal, in dem wir unsere bereitgestellte Zertifikat für die Verwendung mit Ihrer app hochladen können.</span><span class="sxs-lookup"><span data-stu-id="c96b7-204">While these steps mention SSL, there is a "Private certificates" section on the portal where we can upload our provisioned certificate to use with our app.</span></span>

<span data-ttu-id="c96b7-205">Nach diesem Schritt wir sollten in der Lage, die Anwendung neu zu starten, und wird vollständig funktionsfähig.</span><span class="sxs-lookup"><span data-stu-id="c96b7-205">After this step, we should be able to restart our application and it should be completely functional.</span></span>

## <a name="other-configuration-options"></a><span data-ttu-id="c96b7-206">Andere Konfigurationsoptionen</span><span class="sxs-lookup"><span data-stu-id="c96b7-206">Other configuration options</span></span>
<span data-ttu-id="c96b7-207">Unsere Unterstützung für die Autorisierung für API-builds über Identity Server mit einem Satz von Konventionen, Standardwerte und Verbesserungen an die Oberfläche für Single Page Applications zu vereinfachen.</span><span class="sxs-lookup"><span data-stu-id="c96b7-207">Our support for API authorization builds on top of Identity Server with a set of conventions, default values and enhancements to simplify the experience for Single Page Applications.</span></span> <span data-ttu-id="c96b7-208">Natürlich ist das volle Potenzial von Identity Server verfügbar ist, hinter den Kulissen, wenn die Integrationen, die wir anbieten Ihr Szenario nicht abdecken.</span><span class="sxs-lookup"><span data-stu-id="c96b7-208">Needless to say, the full power of Identity Server is available behind the scenes if the integrations that we offer don't cover your scenario.</span></span> <span data-ttu-id="c96b7-209">Unser Support ist auf "Erstanbieter"-Anwendungen, sogenannte, in denen alle Anwendungen erstellt und bereitgestellt, die von der Organisation ausgerichtet.</span><span class="sxs-lookup"><span data-stu-id="c96b7-209">Our support is focused on what we call "first-party" applications, where all the applications are created and deployed by our organization.</span></span> <span data-ttu-id="c96b7-210">Daher bieten wir keine Unterstützung für Aufgaben wie die Zustimmung oder in einem Verbund.</span><span class="sxs-lookup"><span data-stu-id="c96b7-210">As such we don't offer support for things like consent or federation.</span></span> <span data-ttu-id="c96b7-211">Für diese Szenarien ist unsere Empfehlung zum Verwenden von Identity Server ein, und führen ihre Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="c96b7-211">For those scenarios our recommendation is to use Identity Server and follow their documentation.</span></span>

### <a name="application-profiles"></a><span data-ttu-id="c96b7-212">Anwendungsprofile</span><span class="sxs-lookup"><span data-stu-id="c96b7-212">Application profiles</span></span>
<span data-ttu-id="c96b7-213">Anwendungsprofile werden vordefinierte Konfigurationen für Anwendungen, die ihre Parameter weiter definieren.</span><span class="sxs-lookup"><span data-stu-id="c96b7-213">Application profiles are predefined configurations for applications that further define their parameters.</span></span> <span data-ttu-id="c96b7-214">Zurzeit unterstützen wir zwei Profile:</span><span class="sxs-lookup"><span data-stu-id="c96b7-214">At this time we support two profiles:</span></span>
* <span data-ttu-id="c96b7-215">IdentityServerSPA: Stellt eine einseitige Anwendung gehostet wird, zusammen mit der Identity-Server als einzelne Einheit dar.</span><span class="sxs-lookup"><span data-stu-id="c96b7-215">IdentityServerSPA: Represents a single page application hosted alongside Identity Server as a single unit.</span></span>
  * <span data-ttu-id="c96b7-216">Der umleitungs-URI ist standardmäßig `/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="c96b7-216">The redirect_uri defaults to `/authentication/login-callback`.</span></span>
  * <span data-ttu-id="c96b7-217">Standardmäßig den Post_logout_redirect_uri `/authentication/logout-callback`.</span><span class="sxs-lookup"><span data-stu-id="c96b7-217">The post_logout_redirect_uri defaults to `/authentication/logout-callback`.</span></span>
  * <span data-ttu-id="c96b7-218">Der Satz von Bereichen umfasst die `openid`, `profile`, und alle Bereiche, die für die APIs in der Anwendung definiert.</span><span class="sxs-lookup"><span data-stu-id="c96b7-218">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the application.</span></span>
  * <span data-ttu-id="c96b7-219">Der Satz von zulässigen OIDC-Antworttypen ist `id_token token` oder jeweils einzeln (`id_token`, `token`).</span><span class="sxs-lookup"><span data-stu-id="c96b7-219">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="c96b7-220">Die zulässigen Antwortmodus ist `fragment`.</span><span class="sxs-lookup"><span data-stu-id="c96b7-220">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="c96b7-221">SPA: Stellt eine einseitige Anwendung, die nicht mit Identity Server gehostet wird.</span><span class="sxs-lookup"><span data-stu-id="c96b7-221">SPA: Represents a single page application that is not hosted with Identity Server.</span></span>
  * <span data-ttu-id="c96b7-222">Der Satz von Bereichen umfasst die `openid`, `profile`, und alle Bereiche, die für die APIs in der Anwendung definiert.</span><span class="sxs-lookup"><span data-stu-id="c96b7-222">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the application.</span></span>
  * <span data-ttu-id="c96b7-223">Der Satz von zulässigen OIDC-Antworttypen ist `id_token token` oder jeweils einzeln (`id_token`, `token`).</span><span class="sxs-lookup"><span data-stu-id="c96b7-223">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="c96b7-224">Die zulässigen Antwortmodus ist `fragment`.</span><span class="sxs-lookup"><span data-stu-id="c96b7-224">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="c96b7-225">IdentityServerJwt: Stellt eine API, die zusammen mit gehostet wird, wird mit Identity Server dar.</span><span class="sxs-lookup"><span data-stu-id="c96b7-225">IdentityServerJwt: Represents an API that is hosted alongside with Identity Server.</span></span>
  * <span data-ttu-id="c96b7-226">Die Anwendung ist konfiguriert, um einen Bereich verfügen, der standardmäßig auf den Namen der Anwendung.</span><span class="sxs-lookup"><span data-stu-id="c96b7-226">The application is configured to have a single scope that defaults to the application name.</span></span>
* <span data-ttu-id="c96b7-227">API: Stellt eine API, die nicht gehostet wird, wird mit Identity Server dar.</span><span class="sxs-lookup"><span data-stu-id="c96b7-227">API: Represents an API that is not hosted with Identity Server.</span></span>
  * <span data-ttu-id="c96b7-228">Die Anwendung ist konfiguriert, um einen Bereich verfügen, der standardmäßig auf den Namen der Anwendung.</span><span class="sxs-lookup"><span data-stu-id="c96b7-228">The application is configured to have a single scope that defaults to the application name.</span></span>

### <a name="configuration-through-appsettings"></a><span data-ttu-id="c96b7-229">Die Konfiguration über "appSettings"</span><span class="sxs-lookup"><span data-stu-id="c96b7-229">Configuration through AppSettings</span></span>
<span data-ttu-id="c96b7-230">Wir können die Anwendungen über unser Konfigurationssystem bzw. der Liste der Clients oder Ressourcen hinzufügen konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="c96b7-230">We can configure the applications through our configuration system by adding them to the list of Clients or Resources respectively.</span></span> 

<span data-ttu-id="c96b7-231">Konfiguration von Clients konfigurieren wir die `redirect_uri` und `post_logout_redirect_uri` wie unten dargestellt:</span><span class="sxs-lookup"><span data-stu-id="c96b7-231">When configuring clients we can configure the `redirect_uri` and the `post_logout_redirect_uri` as shown below:</span></span>
```json
  "IdentityServer": {
    "Clients": {
      "MySPA": {
        "Profile": "SPA",
        "RedirectUri": "https://www.example.com/authentication/login-callback",
        "LogoutUri": "https://www.example.com/authentication/logout-callback"
      }
    }
  }
```

<span data-ttu-id="c96b7-232">Beim Konfigurieren der Ressourcen können wir die Bereiche für die Ressource konfigurieren, wie unten dargestellt:</span><span class="sxs-lookup"><span data-stu-id="c96b7-232">When configuring resources we can configure the scopes for the resource as shown below:</span></span>
```json
"IdentityServer": {
  "Resources": {
    "MyExternalApi": {
      "Profile": "API",
      "Scopes": "a b c",
    }
  }
}
```

### <a name="configuration-through-code"></a><span data-ttu-id="c96b7-233">Die Konfiguration über code</span><span class="sxs-lookup"><span data-stu-id="c96b7-233">Configuration through code</span></span>
<span data-ttu-id="c96b7-234">Wir können auch konfigurieren, die Clients und Ressourcen mithilfe von Code mithilfe einer Überladung der AddApiAuthorization, die eine Aktion, um Optionen zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="c96b7-234">We can also configure the clients and resources through code using an overload of AddApiAuthorization that takes an action to configure options.</span></span>
```csharp
AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options =>
{
    options.Clients.AddSPA(
        "My SPA",
        spa => spa.WithRedirectUri("http://www.example.com/authentication/login-callback")
            .WithLogoutRedirectUri("http://www.example.com/authentication/logout-callback"));

    options.ApiResources.AddApiResource("MyExternalApi", resource => resource.WithScopes("a", "b", "c"));
});
```

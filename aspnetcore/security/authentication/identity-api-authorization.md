---
title: Einführung in die Authentifizierung für einzelne Page-Apps in ASP.NET Core
author: javiercn
description: Verwenden-Identität über eine einseitige Anwendung, die in einer ASP.NET Core-app gehostet.
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 03/07/2019
uid: security/authentication/identity/spa
ms.openlocfilehash: 4afc9ac0a3c54b452c6a1b23e4de31d7e2fc5284
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64894147"
---
# <a name="authentication-and-authorization-for-spas"></a><span data-ttu-id="d933f-103">Authentifizierung und Autorisierung für SPAs</span><span class="sxs-lookup"><span data-stu-id="d933f-103">Authentication and authorization for SPAs</span></span>

<span data-ttu-id="d933f-104">ASP.NET Core 3.0 oder höher bietet die Authentifizierung in einzelnen-Page-Webanwendungen (SPAs) mithilfe der Unterstützung für die API-Autorisierung.</span><span class="sxs-lookup"><span data-stu-id="d933f-104">ASP.NET Core 3.0 or later offers authentication in Single Page Apps (SPAs) using the support for API authorization.</span></span> <span data-ttu-id="d933f-105">ASP.NET Core-Identität für die Authentifizierung und Speichern von Benutzern mit kombiniert [IdentityServer](https://identityserver.io/) für die Implementierung des Open ID Connect.</span><span class="sxs-lookup"><span data-stu-id="d933f-105">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer](https://identityserver.io/) for implementing Open ID Connect.</span></span>

<span data-ttu-id="d933f-106">Ein Authentifizierungsparameter hinzugefügt wurde die **Angular** und **reagieren** -Projektvorlagen, die an den Parameter "Authentication" in ähnelt der **Webanwendung (Model-View-Controller)**  (MVC) und **Webanwendung** (Razor Pages)-Projektvorlagen.</span><span class="sxs-lookup"><span data-stu-id="d933f-106">An authentication parameter was added to the **Angular** and **React** project templates that is similar to the authentication parameter in the **Web Application (Model-View-Controller)** (MVC) and **Web Application** (Razor Pages) project templates.</span></span> <span data-ttu-id="d933f-107">Die zulässige Parameterwerte sind **keine** und **einzelne**.</span><span class="sxs-lookup"><span data-stu-id="d933f-107">The allowed parameter values are **None** and **Individual**.</span></span> <span data-ttu-id="d933f-108">Die **React.js und Redux** Projektvorlage nicht den Authentifizierungsparameter zu diesem Zeitpunkt nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="d933f-108">The **React.js and Redux** project template doesn't support the authentication parameter at this time.</span></span>

## <a name="create-an-app-with-api-authorization-support"></a><span data-ttu-id="d933f-109">Erstellen einer app mit Unterstützung für die Autorisierung-API</span><span class="sxs-lookup"><span data-stu-id="d933f-109">Create an app with API authorization support</span></span>

<span data-ttu-id="d933f-110">Benutzerauthentifizierung und-Autorisierung können mit Angular und React-SPAs verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="d933f-110">User authentication and authorization can be used with both Angular and React SPAs.</span></span> <span data-ttu-id="d933f-111">Öffnen Sie eine Befehlsshell, und führen Sie den folgenden Befehl:</span><span class="sxs-lookup"><span data-stu-id="d933f-111">Open a command shell, and run the following command:</span></span>

<span data-ttu-id="d933f-112">**Angular**:</span><span class="sxs-lookup"><span data-stu-id="d933f-112">**Angular**:</span></span>

```console
dotnet new angular -o <output_directory_name> -au Individual
```

<span data-ttu-id="d933f-113">**REACT**:</span><span class="sxs-lookup"><span data-stu-id="d933f-113">**React**:</span></span>

```console
dotnet new react -o <output_directory_name> -au Individual
```

<span data-ttu-id="d933f-114">Der vorherige Befehl erstellt eine ASP.NET Core-app mit einem *ClientApp* Verzeichnis mit der einseitigen Anwendung.</span><span class="sxs-lookup"><span data-stu-id="d933f-114">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the SPA.</span></span>

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a><span data-ttu-id="d933f-115">Allgemeine Beschreibung von ASP.NET Core-Komponenten der app</span><span class="sxs-lookup"><span data-stu-id="d933f-115">General description of the ASP.NET Core components of the app</span></span>

<span data-ttu-id="d933f-116">Den folgenden Abschnitten werden die Erweiterungen für das Projekt, bei der Unterstützung der Authentifizierung enthalten ist:</span><span class="sxs-lookup"><span data-stu-id="d933f-116">The following sections describe additions to the project when authentication support is included:</span></span>

### <a name="startup-class"></a><span data-ttu-id="d933f-117">Startup-Klasse</span><span class="sxs-lookup"><span data-stu-id="d933f-117">Startup class</span></span>

<span data-ttu-id="d933f-118">Die `Startup` -Klasse verfügt über den folgenden Ergänzungen:</span><span class="sxs-lookup"><span data-stu-id="d933f-118">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="d933f-119">In der `Startup.ConfigureServices` Methode:</span><span class="sxs-lookup"><span data-stu-id="d933f-119">Inside the `Startup.ConfigureServices` method:</span></span>
  * <span data-ttu-id="d933f-120">Mit der standardmäßigen UI-Identität:</span><span class="sxs-lookup"><span data-stu-id="d933f-120">Identity with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="d933f-121">Identity Server mit einem zusätzlichen `AddApiAuthorization` Hilfsmethode dieses Setups einige standardmäßige ASP.NET Core-Konventionen über Identity Server:</span><span class="sxs-lookup"><span data-stu-id="d933f-121">IdentityServer with an additional `AddApiAuthorization` helper method that setups some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="d933f-122">Authentifizierung mit einem zusätzlichen `AddIdentityServerJwt` Hilfsmethode, die die app, um das Überprüfen von JWT-Token konfiguriert, die von Identity Server erzeugt:</span><span class="sxs-lookup"><span data-stu-id="d933f-122">Authentication with an additional `AddIdentityServerJwt` helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="d933f-123">In der `Startup.Configure` Methode:</span><span class="sxs-lookup"><span data-stu-id="d933f-123">Inside the `Startup.Configure` method:</span></span>
  * <span data-ttu-id="d933f-124">Die authentifizierungsmiddleware, die für die Anmeldeinformationen für den Anforderung überprüft und Festlegen des Benutzers im Kontext der Anforderung verantwortlich ist:</span><span class="sxs-lookup"><span data-stu-id="d933f-124">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="d933f-125">Die Identity Server-Middleware, die die Open ID Connect-Endpunkte verfügbar macht:</span><span class="sxs-lookup"><span data-stu-id="d933f-125">The IdentityServer middleware that exposes the Open ID Connect endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="d933f-126">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="d933f-126">AddApiAuthorization</span></span>

<span data-ttu-id="d933f-127">Diese Hilfsmethode konfiguriert Identity Server, um unsere unterstützte Konfiguration zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="d933f-127">This helper method configures IdentityServer to use our supported configuration.</span></span> <span data-ttu-id="d933f-128">Identity Server ist ein leistungsfähiges und erweiterbares Framework für die Behandlung von Sicherheitsrisiken für die app.</span><span class="sxs-lookup"><span data-stu-id="d933f-128">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="d933f-129">Zur gleichen Zeit verfügbar macht, die unnötigen Komplexität für die häufigsten Szenarien.</span><span class="sxs-lookup"><span data-stu-id="d933f-129">At the same time, that exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="d933f-130">Daher ist ein Satz von Konventionen und Konfigurationsoptionen für Sie bereitgestellt, die einen guten Ausgangspunkt betrachtet werden.</span><span class="sxs-lookup"><span data-stu-id="d933f-130">Consequently, a set of conventions and configuration options is provided to you that are considered a good starting point.</span></span> <span data-ttu-id="d933f-131">Nach Ihrer Authentifizierung Änderungen erfordert, ist das volle Potenzial von Identity Server weiterhin verfügbar, um die Authentifizierung entsprechend Ihren Anforderungen anzupassen.</span><span class="sxs-lookup"><span data-stu-id="d933f-131">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit your needs.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="d933f-132">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="d933f-132">AddIdentityServerJwt</span></span>

<span data-ttu-id="d933f-133">Diese Hilfsmethode wird ein Schema der Richtlinie für die app als der Standardhandler für die Authentifizierung konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="d933f-133">This helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="d933f-134">Die Richtlinie ist so konfiguriert, dass die Identität, die alle Anforderungen an alle untergeordneten Pfad im Bereich der URL des Identitätsanbieters weitergeleitet zu verarbeiten können "/ Identity".</span><span class="sxs-lookup"><span data-stu-id="d933f-134">The policy is configured to let Identity handle all requests routed to any subpath in the Identity URL space "/Identity".</span></span> <span data-ttu-id="d933f-135">Die `JwtBearerHandler` verarbeitet alle anderen Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="d933f-135">The `JwtBearerHandler` handles all other requests.</span></span> <span data-ttu-id="d933f-136">Darüber hinaus diese Methode registriert eine `<<ApplicationName>>API` -API-Ressource mit Identity Server mit einem Standardbereich des `<<ApplicationName>>API` und die JWT-Trägertoken-Middleware zum Überprüfen von Token, die von Identity Server ausgestellt wird, für die app konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="d933f-136">Additionally, this method registers an `<<ApplicationName>>API` API resource with IdentityServer with a default scope of `<<ApplicationName>>API` and configures the JWT Bearer token middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="sampledatacontroller"></a><span data-ttu-id="d933f-137">SampleDataController</span><span class="sxs-lookup"><span data-stu-id="d933f-137">SampleDataController</span></span>

<span data-ttu-id="d933f-138">In der *Controllers\SampleDataController.cs* Datei, beachten Sie die `[Authorize]` -Attribut nicht angewendet werden, auf die Klasse, der angibt, dass der Benutzer autorisiert werden muss, basierend auf die Standardrichtlinie auf die Ressource zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="d933f-138">In the *Controllers\SampleDataController.cs* file, notice the `[Authorize]` attribute applied to the class that indicates that the user needs to be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="d933f-139">Die Standardrichtlinie für die Autorisierung konfiguriert werden, um das Standardschema für die Authentifizierung, verwenden, die vom eingerichtet ist, dass geschieht `AddIdentityServerJwt` mit dem Richtlinien-Schema, das weiter oben erwähnt wurde, wodurch die `JwtBearerHandler` konfiguriert, indem diese Hilfsmethode den Standardhandler für Anforderungen an die app.</span><span class="sxs-lookup"><span data-stu-id="d933f-139">The default authorization policy happens to be configured to use the default authentication scheme, which is set up by `AddIdentityServerJwt` to the policy scheme that was mentioned above, making the `JwtBearerHandler` configured by such helper method the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="d933f-140">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="d933f-140">ApplicationDbContext</span></span>

<span data-ttu-id="d933f-141">In der *Data\ApplicationDbContext.cs* Datei, die gleichen `DbContext` werden in der Identität mit der Ausnahme, die sie erweitert `ApiAuthorizationDbContext` (eine abgeleitete Klasse von `IdentityDbContext`), die das Schema für IdentityServer zu müssen.</span><span class="sxs-lookup"><span data-stu-id="d933f-141">In the *Data\ApplicationDbContext.cs* file, notice the same `DbContext` is used in Identity with the exception that it extends `ApiAuthorizationDbContext` (a more derived class from `IdentityDbContext`) to include the schema for IdentityServer.</span></span>

<span data-ttu-id="d933f-142">Um vollständige Kontrolle über das Datenbankschema zu erhalten, erben von einem der verfügbaren Identität `DbContext` Klassen aus, und konfigurieren Sie den Kontext, um das Schema für die Identität durch Aufrufen von aufzunehmen `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` auf die `OnModelCreating` Methode.</span><span class="sxs-lookup"><span data-stu-id="d933f-142">To gain full control of the database schema, inherit from one of the available Identity `DbContext` classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` on the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="d933f-143">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="d933f-143">OidcConfigurationController</span></span>

<span data-ttu-id="d933f-144">In der *Controllers\OidcConfigurationController.cs* Datei, beachten Sie, dass den Endpunkt, der bereitgestellt wird, um die OIDC-Parameter zu verarbeiten, die der Client verwenden muss.</span><span class="sxs-lookup"><span data-stu-id="d933f-144">In the *Controllers\OidcConfigurationController.cs* file, notice the endpoint that's provisioned to serve the OIDC parameters that the client needs to use.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="d933f-145">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="d933f-145">appsettings.json</span></span>

<span data-ttu-id="d933f-146">In der *"appSettings.JSON"* Datei von das Stammverzeichnis des Projekts, es gibt eine neue `IdentityServer` Abschnitt mit die Liste der Beschreibungen konfigurierten Clients.</span><span class="sxs-lookup"><span data-stu-id="d933f-146">In the *appsettings.json* file of the project root, there's a new `IdentityServer` section that describes the list of configured clients.</span></span> <span data-ttu-id="d933f-147">Im folgenden Beispiel wird ein einzelner Client vorhanden.</span><span class="sxs-lookup"><span data-stu-id="d933f-147">In the following example, there's a single client.</span></span> <span data-ttu-id="d933f-148">Clientname entspricht der Name der app und wird gemäß der Konvention, die OAuth zugeordnet `ClientId` Parameter.</span><span class="sxs-lookup"><span data-stu-id="d933f-148">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="d933f-149">Das Profil gibt an, der app-Typ, der konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="d933f-149">The profile indicates the app type being configured.</span></span> <span data-ttu-id="d933f-150">Sie wird intern zum Laufwerk Konventionen verwendet, die den Konfigurationsprozess für den Server zu vereinfachen.</span><span class="sxs-lookup"><span data-stu-id="d933f-150">It's used internally to drive conventions that simplify the configuration process for the server.</span></span> <span data-ttu-id="d933f-151">Es gibt mehrere Profile verfügbar sind, wie unter der [Anwendungsprofile](#application-profiles) Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="d933f-151">There are several profiles available, as explained in the [Application profiles](#application-profiles) section.</span></span>

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a><span data-ttu-id="d933f-152">appsettings.Development.json</span><span class="sxs-lookup"><span data-stu-id="d933f-152">appsettings.Development.json</span></span>

<span data-ttu-id="d933f-153">In der *"appSettings". Development.JSON* Datei, der das Stammverzeichnis des Projekts, gibt es eine `IdentityServer` Abschnitt, den Schlüssel zum Signieren von Token beschrieben.</span><span class="sxs-lookup"><span data-stu-id="d933f-153">In the *appsettings.Development.json* file of the project root, there's an `IdentityServer` section that describes the key used to sign tokens.</span></span> <span data-ttu-id="d933f-154">Wenn Sie in der produktionsumgebung bereitstellen, ein Schlüssel muss bereitgestellt und zusammen mit der app bereitgestellt werden wie unter der [bereitstellen, in der Produktion](#deploy-to-production) Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="d933f-154">When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section.</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a><span data-ttu-id="d933f-155">Allgemeine Beschreibung von der Angular-app</span><span class="sxs-lookup"><span data-stu-id="d933f-155">General description of the Angular app</span></span>

<span data-ttu-id="d933f-156">Die Authentifizierung und Autorisierung für API-Unterstützung in der Angular Vorlage befindet sich im eigenen Angular-Modul in die *ClientApp\src\api-Authorization* Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="d933f-156">The authentication and API authorization support in the Angular template resides in its own Angular module in the *ClientApp\src\api-authorization* directory.</span></span> <span data-ttu-id="d933f-157">Das Modul besteht aus den folgenden Elementen:</span><span class="sxs-lookup"><span data-stu-id="d933f-157">The module is composed of the following elements:</span></span>

* <span data-ttu-id="d933f-158">3-Komponenten:</span><span class="sxs-lookup"><span data-stu-id="d933f-158">3 components:</span></span>
  * <span data-ttu-id="d933f-159">*login.component.ts*: Verarbeitet die app Anmeldung.</span><span class="sxs-lookup"><span data-stu-id="d933f-159">*login.component.ts*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="d933f-160">*logout.component.ts*: Der app Abmelden Flow wird verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="d933f-160">*logout.component.ts*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="d933f-161">*login-menu.component.ts*: Ein Widget, das eine der folgenden Links angezeigt:</span><span class="sxs-lookup"><span data-stu-id="d933f-161">*login-menu.component.ts*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="d933f-162">Benutzerprofilverwaltung, und melden Sie sich Links, wenn der Benutzer authentifiziert ist.</span><span class="sxs-lookup"><span data-stu-id="d933f-162">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="d933f-163">Die Registrierung und Anmeldung über die Links, wenn der Benutzer nicht authentifiziert ist.</span><span class="sxs-lookup"><span data-stu-id="d933f-163">Registration and log in links when the user isn't authenticated.</span></span>
* <span data-ttu-id="d933f-164">Eine Route Guard `AuthorizeGuard` , die hinzugefügt werden können, um die sprachliche und erfordert, dass einen Benutzer authentifiziert werden, bevor der Zugriff auf die Route.</span><span class="sxs-lookup"><span data-stu-id="d933f-164">A route guard `AuthorizeGuard` that can be added to routes and requires a user to be authenticated before visiting the route.</span></span>
* <span data-ttu-id="d933f-165">Ein HTTP-Interceptor `AuthorizeInterceptor` , die das Zugriffstoken auf ausgehenden HTTP-Anforderungen, die für die API aus, wenn der Benutzer authentifiziert ist angefügt.</span><span class="sxs-lookup"><span data-stu-id="d933f-165">An HTTP interceptor `AuthorizeInterceptor` that attaches the access token to outgoing HTTP requests targeting the API when the user is authenticated.</span></span>
* <span data-ttu-id="d933f-166">Ein Dienst `AuthorizeService` , kümmert sich um die Low-Level-Details des Authentifizierungsprozesses und Informationen zu den authentifizierten Benutzer auf den Rest der app für die Nutzung verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="d933f-166">A service `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>
* <span data-ttu-id="d933f-167">Ein Angular-Modul, die Routen, die die Authentifizierung Teile der app definiert.</span><span class="sxs-lookup"><span data-stu-id="d933f-167">An Angular module that defines routes associated with the authentication parts of the app.</span></span> <span data-ttu-id="d933f-168">Es stellt die Anmeldung im Menü-Komponente, den Interceptor, den Wächter und der Dienst für die Nutzung vom Rest der app.</span><span class="sxs-lookup"><span data-stu-id="d933f-168">It exposes the login menu component, the interceptor, the guard, and the service for consumption from the rest of the app.</span></span>

## <a name="general-description-of-the-react-app"></a><span data-ttu-id="d933f-169">Allgemeine Beschreibung der React-app</span><span class="sxs-lookup"><span data-stu-id="d933f-169">General description of the React app</span></span>

<span data-ttu-id="d933f-170">Die Unterstützung für die Authentifizierung und Autorisierung der API in der React-Vorlage befindet sich in der *ClientApp\src\components\api-Authorization* Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="d933f-170">The support for authentication and API authorization in the React template resides in the *ClientApp\src\components\api-authorization* directory.</span></span> <span data-ttu-id="d933f-171">Es besteht aus den folgenden Elementen:</span><span class="sxs-lookup"><span data-stu-id="d933f-171">It's composed of the following elements:</span></span>

* <span data-ttu-id="d933f-172">4-Komponenten:</span><span class="sxs-lookup"><span data-stu-id="d933f-172">4 components:</span></span>
  * <span data-ttu-id="d933f-173">*Login.js*: Verarbeitet die app Anmeldung.</span><span class="sxs-lookup"><span data-stu-id="d933f-173">*Login.js*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="d933f-174">*Logout.js*: Der app Abmelden Flow wird verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="d933f-174">*Logout.js*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="d933f-175">*LoginMenu.js*: Ein Widget, das eine der folgenden Links angezeigt:</span><span class="sxs-lookup"><span data-stu-id="d933f-175">*LoginMenu.js*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="d933f-176">Benutzerprofilverwaltung, und melden Sie sich Links, wenn der Benutzer authentifiziert ist.</span><span class="sxs-lookup"><span data-stu-id="d933f-176">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="d933f-177">Die Registrierung und Anmeldung über die Links, wenn der Benutzer nicht authentifiziert ist.</span><span class="sxs-lookup"><span data-stu-id="d933f-177">Registration and log in links when the user isn't authenticated.</span></span>
  * <span data-ttu-id="d933f-178">*AuthorizeRoute.js*: Eine Route-Komponente, die benötigt ein Benutzer authentifiziert werden, vor dem Rendern der Komponente angegeben, der `Component` Parameter.</span><span class="sxs-lookup"><span data-stu-id="d933f-178">*AuthorizeRoute.js*: A route component that requires a user to be authenticated before rendering the component indicated in the `Component` parameter.</span></span>
* <span data-ttu-id="d933f-179">Eine exportierte `authService` Instanz der Klasse `AuthorizeService` , kümmert sich um die Low-Level-Details des Authentifizierungsprozesses und Informationen zu den authentifizierten Benutzer auf den Rest der app für die Nutzung verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="d933f-179">An exported `authService` instance of class `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>

<span data-ttu-id="d933f-180">Nun, da Sie die Hauptkomponenten der Lösung gesehen haben, bringen Sie einen tieferen Einblick in die einzelnen Szenarien für die app.</span><span class="sxs-lookup"><span data-stu-id="d933f-180">Now that you've seen the main components of the solution, you can take a deeper look at individual scenarios for the app.</span></span>

## <a name="require-authorization-on-a-new-api"></a><span data-ttu-id="d933f-181">Eine Autorisierung auf einer neuen API</span><span class="sxs-lookup"><span data-stu-id="d933f-181">Require authorization on a new API</span></span>

<span data-ttu-id="d933f-182">Standardmäßig konfiguriert das System ist einfach der Autorisierung für die neuen APIs erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="d933f-182">By default, the system is configured to easily require authorization for new APIs.</span></span> <span data-ttu-id="d933f-183">Zu diesem Zweck erstellen Sie einen neuen Controller, und fügen die `[Authorize]` -Attribut zur Controllerklasse oder auf eine beliebige Aktion im Controller.</span><span class="sxs-lookup"><span data-stu-id="d933f-183">To do so, create a new controller and add the `[Authorize]` attribute to the controller class or to any action within the controller.</span></span>

## <a name="protect-a-client-side-route-angular"></a><span data-ttu-id="d933f-184">Schützen Sie eine Route für die clientseitige (Angular)</span><span class="sxs-lookup"><span data-stu-id="d933f-184">Protect a client-side route (Angular)</span></span>

<span data-ttu-id="d933f-185">Schützen eine Route für die clientseitige erfolgt durch Hinzufügen der Authorize-Guard zur Liste der Wächter ausgeführt wird, wenn Sie eine Route zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="d933f-185">Protecting a client-side route is done by adding the authorize guard to the list of guards to run when configuring a route.</span></span> <span data-ttu-id="d933f-186">Beispielsweise können Sie sehen die `fetch-data` Route in der Haupt-app-Angular-Modul konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="d933f-186">As an example, you can see how the `fetch-data` route is configured within the main app Angular module:</span></span>

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

<span data-ttu-id="d933f-187">Es ist wichtig zu erwähnen, dass eine Route schützen nicht den tatsächlichen Endpunkt schützen (erfordert immer noch eine `[Authorize]` angewendet werden), aber es nur verhindert, dass den Benutzer navigieren zu der angegebenen Client-Side-Route, wenn sie nicht authentifiziert ist.</span><span class="sxs-lookup"><span data-stu-id="d933f-187">It's important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-angular"></a><span data-ttu-id="d933f-188">Authentifizieren der API-Anforderungen (Angular)</span><span class="sxs-lookup"><span data-stu-id="d933f-188">Authenticate API requests (Angular)</span></span>

<span data-ttu-id="d933f-189">Authentifizieren von Anforderungen für APIs, die gemeinsam mit der app gehostet erfolgt automatisch durch die Verwendung der HTTP-Client-Interceptor, die von der app definiert.</span><span class="sxs-lookup"><span data-stu-id="d933f-189">Authenticating requests to APIs hosted alongside the app is done automatically through the use of the HTTP client interceptor defined by the app.</span></span>

## <a name="protect-a-client-side-route-react"></a><span data-ttu-id="d933f-190">Schützen Sie eine Route für die clientseitige (React)</span><span class="sxs-lookup"><span data-stu-id="d933f-190">Protect a client-side route (React)</span></span>

<span data-ttu-id="d933f-191">Schützen Sie eine Client-Side-Route mithilfe der `AuthorizeRoute` anstelle der einfachen Komponente `Route` Komponente.</span><span class="sxs-lookup"><span data-stu-id="d933f-191">Protect a client-side route by using the `AuthorizeRoute` component instead of the plain `Route` component.</span></span> <span data-ttu-id="d933f-192">Sehen Sie sich beispielsweise wie die `fetch-data` Route konfiguriert ist, in der `App` Komponente:</span><span class="sxs-lookup"><span data-stu-id="d933f-192">For example, notice how the `fetch-data` route is configured within the `App` component:</span></span>

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

<span data-ttu-id="d933f-193">Schützen eine Route:</span><span class="sxs-lookup"><span data-stu-id="d933f-193">Protecting a route:</span></span>

* <span data-ttu-id="d933f-194">Schützt nicht den tatsächlichen Endpunkt (erfordert immer noch eine `[Authorize]` angewendet werden).</span><span class="sxs-lookup"><span data-stu-id="d933f-194">Doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it).</span></span>
* <span data-ttu-id="d933f-195">Nur verhindert, dass den Benutzer navigieren zu der angegebenen Client-Side-Route, wenn sie nicht authentifiziert ist.</span><span class="sxs-lookup"><span data-stu-id="d933f-195">Only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-react"></a><span data-ttu-id="d933f-196">Authentifizieren der API-Anforderungen (React)</span><span class="sxs-lookup"><span data-stu-id="d933f-196">Authenticate API requests (React)</span></span>

<span data-ttu-id="d933f-197">Authentifizieren von Anforderungen mit React erfolgt durch das erste Importieren der `authService` -Instanz aus der `AuthorizeService`.</span><span class="sxs-lookup"><span data-stu-id="d933f-197">Authenticating requests with React is done by first importing the `authService` instance from the `AuthorizeService`.</span></span> <span data-ttu-id="d933f-198">Das Zugriffstoken wird abgerufen, von der `authService` und auf die Anforderung angefügt ist, wie unten dargestellt.</span><span class="sxs-lookup"><span data-stu-id="d933f-198">The access token is retrieved from the `authService` and is attached to the request as shown below.</span></span> <span data-ttu-id="d933f-199">React-Komponenten, diese Arbeit erfolgt in der Regel der `componentDidMount` Lebenszyklusmethode oder als Ergebnis eine Benutzerinteraktion.</span><span class="sxs-lookup"><span data-stu-id="d933f-199">In React components, this work is typically done in the `componentDidMount` lifecycle method or as the result from some user interaction.</span></span>

### <a name="import-the-authservice-into-your-component"></a><span data-ttu-id="d933f-200">Importieren Sie die AuthService in Ihre Komponente</span><span class="sxs-lookup"><span data-stu-id="d933f-200">Import the authService into your component</span></span>

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a><span data-ttu-id="d933f-201">Abzurufen Sie, und fügen Sie das Zugriffstoken an die Antwort</span><span class="sxs-lookup"><span data-stu-id="d933f-201">Retrieve and attach the access token to the response</span></span>

```javascript
async populateWeatherData() {
  const token = await authService.getAccessToken();
  const response = await fetch('api/SampleData/WeatherForecasts', {
    headers: !token ? {} : { 'Authorization': `Bearer ${token}` }
  });
  const data = await response.json();
  this.setState({ forecasts: data, loading: false });
}
```

## <a name="deploy-to-production"></a><span data-ttu-id="d933f-202">Für die Produktion bereitstellen</span><span class="sxs-lookup"><span data-stu-id="d933f-202">Deploy to production</span></span>

<span data-ttu-id="d933f-203">Um die app für die Produktion bereitstellen zu können, müssen die folgenden Ressourcen bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="d933f-203">To deploy the app to production, the following resources need to be provisioned:</span></span>

* <span data-ttu-id="d933f-204">Eine Datenbank zum Speichern der Identity-Benutzerkonten und die Identity Server gewährt.</span><span class="sxs-lookup"><span data-stu-id="d933f-204">A database to store the Identity user accounts and the IdentityServer grants.</span></span>
* <span data-ttu-id="d933f-205">Ein Production-Zertifikat zum Signieren von Token verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="d933f-205">A production certificate to use for signing tokens.</span></span>
  * <span data-ttu-id="d933f-206">Es gibt keine speziellen Anforderungen für dieses Zertifikat ein. Sie können ein selbstsigniertes Zertifikat oder ein Zertifikat mit einer Stammzertifizierungsstelle bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="d933f-206">There are no specific requirements for this certificate; it can be a self-signed certificate or a certificate provisioned through a CA authority.</span></span>
  * <span data-ttu-id="d933f-207">Sie können mithilfe von standard-Tools wie PowerShell oder OpenSSL generiert werden.</span><span class="sxs-lookup"><span data-stu-id="d933f-207">It can be generated through standard tools like PowerShell or OpenSSL.</span></span>
  * <span data-ttu-id="d933f-208">Es kann in den Zertifikatspeicher auf den Zielcomputern installiert wird, oder als eine *PFX* Datei mit einem sicheren Kennwort.</span><span class="sxs-lookup"><span data-stu-id="d933f-208">It can be installed into the certificate store on the target machines or deployed as a *.pfx* file with a strong password.</span></span>

### <a name="example-deploy-to-azure-websites"></a><span data-ttu-id="d933f-209">Beispiel: Bereitstellen auf Azure Websites</span><span class="sxs-lookup"><span data-stu-id="d933f-209">Example: Deploy to Azure Websites</span></span>

<span data-ttu-id="d933f-210">Dieser Abschnitt beschreibt das Bereitstellen der app auf Azure-Websites mithilfe eines Zertifikats im Zertifikatspeicher gespeichert.</span><span class="sxs-lookup"><span data-stu-id="d933f-210">This section describes deploying the app to Azure websites using a certificate stored in the certificate store.</span></span> <span data-ttu-id="d933f-211">Um die app zum Laden eines Zertifikats aus dem Zertifikatspeicher zu ändern, muss die App Service-Plan auf mindestens den Tarif "Standard" sein, wenn Sie in einem späteren Schritt konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="d933f-211">To modify the app to load a certificate from the certificate store, the App Service plan needs to be on at least the Standard tier when you configure in a later step.</span></span> <span data-ttu-id="d933f-212">In der app *"appSettings.JSON"* Datei, ändern Sie die `IdentityServer` Abschnitt aus, um die wichtigsten Details enthalten:</span><span class="sxs-lookup"><span data-stu-id="d933f-212">In the app's *appsettings.json* file, modify the `IdentityServer` section to include the key details:</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "My",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

* <span data-ttu-id="d933f-213">Die Name-Eigenschaft für Zertifikat entspricht, mit dem distinguished Antragsteller des Zertifikats.</span><span class="sxs-lookup"><span data-stu-id="d933f-213">The name property on certificate corresponds with the distinguished subject for the certificate.</span></span>
* <span data-ttu-id="d933f-214">Der Speicherort darstellt, wo Sie das Zertifikat zu laden (`CurrentUser` oder `LocalMachine`).</span><span class="sxs-lookup"><span data-stu-id="d933f-214">The store location represents where to load the certificate from (`CurrentUser` or `LocalMachine`).</span></span>
* <span data-ttu-id="d933f-215">Der Speichername stellt es sich um den Namen des Zertifikatspeichers an, in dem das Zertifikat gespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="d933f-215">The store name represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="d933f-216">In diesem Fall zeigt es in den persönlichen Speicher.</span><span class="sxs-lookup"><span data-stu-id="d933f-216">In this case, it points to the personal user store.</span></span>

<span data-ttu-id="d933f-217">Um auf Azure Websites bereitstellen, stellen Sie die app, die die Schritte in [Bereitstellen der app in Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) erforderlichen Azure-Ressourcen erstellen und Bereitstellen der app für die Produktion.</span><span class="sxs-lookup"><span data-stu-id="d933f-217">To deploy to Azure Websites, deploy the app following the steps in [Deploy the app to Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) to create the necessary Azure resources and deploy the app to production.</span></span>

<span data-ttu-id="d933f-218">Nach dem Ausführen der zuvor beschriebenen Anweisungen, die app in Azure bereitgestellt wird, aber noch nicht funktionsfähig.</span><span class="sxs-lookup"><span data-stu-id="d933f-218">After following the preceding instructions, the app is deployed to Azure but isn't yet functional.</span></span> <span data-ttu-id="d933f-219">Das von der app verwendete Zertifikat muss dennoch eingerichtet werden.</span><span class="sxs-lookup"><span data-stu-id="d933f-219">The certificate used by the app still needs to be set up.</span></span> <span data-ttu-id="d933f-220">Suchen Sie den Fingerabdruck des Zertifikats verwendet werden, und führen Sie die Schritte [laden Ihrer Zertifikate](/azure/app-service/app-service-web-ssl-cert-load#load-your-certificates).</span><span class="sxs-lookup"><span data-stu-id="d933f-220">Locate the thumbprint for the certificate to be used, and follow the steps described in [Load your certificates](/azure/app-service/app-service-web-ssl-cert-load#load-your-certificates).</span></span>

<span data-ttu-id="d933f-221">Während Sie diese Schritte ganz zu schweigen SSL, gibt es eine **Zertifikate mit privaten** Abschnitt im Portal, in dem Sie das bereitgestellte Zertifikat zur Verwendung mit der app hochladen können.</span><span class="sxs-lookup"><span data-stu-id="d933f-221">While these steps mention SSL, there's a **Private certificates** section on the portal where you can upload the provisioned certificate to use with the app.</span></span>

<span data-ttu-id="d933f-222">Nach diesem Schritt wird die app neu starten und funktionsfähig sein.</span><span class="sxs-lookup"><span data-stu-id="d933f-222">After this step, restart the app and it should be functional.</span></span>

## <a name="other-configuration-options"></a><span data-ttu-id="d933f-223">Andere Konfigurationsoptionen</span><span class="sxs-lookup"><span data-stu-id="d933f-223">Other configuration options</span></span>

<span data-ttu-id="d933f-224">Die Unterstützung für die Autorisierung für API-builds über Identity Server mit einem Satz von Konventionen, Standardwerte und Verbesserungen an die Oberfläche für SPAs zu vereinfachen.</span><span class="sxs-lookup"><span data-stu-id="d933f-224">The support for API authorization builds on top of IdentityServer with a set of conventions, default values, and enhancements to simplify the experience for SPAs.</span></span> <span data-ttu-id="d933f-225">Natürlich ist das volle Potenzial von Identity Server hinter den Kulissen verfügbar, wenn die ASP.NET Core-Integrationen Ihr Szenario nicht abdecken.</span><span class="sxs-lookup"><span data-stu-id="d933f-225">Needless to say, the full power of IdentityServer is available behind the scenes if the ASP.NET Core integrations don't cover your scenario.</span></span> <span data-ttu-id="d933f-226">Die Unterstützung von ASP.NET Core konzentriert sich auf "Erstanbieter"-apps, in der alle apps erstellt und von der Organisation bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="d933f-226">The ASP.NET Core support is focused on "first-party" apps, where all the apps are created and deployed by our organization.</span></span> <span data-ttu-id="d933f-227">Support ist nicht als solche, für Aspekte wie die Zustimmung oder in einem Verbund angeboten.</span><span class="sxs-lookup"><span data-stu-id="d933f-227">As such, support isn't offered for things like consent or federation.</span></span> <span data-ttu-id="d933f-228">Verwenden Sie für diese Szenarien Identity Server, und befolgen Sie die Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="d933f-228">For those scenarios, use IdentityServer and follow their documentation.</span></span>

### <a name="application-profiles"></a><span data-ttu-id="d933f-229">Anwendungsprofile</span><span class="sxs-lookup"><span data-stu-id="d933f-229">Application profiles</span></span>

<span data-ttu-id="d933f-230">Anwendungsprofile werden vordefinierte Konfigurationen für apps, die ihre Parameter weiter definieren.</span><span class="sxs-lookup"><span data-stu-id="d933f-230">Application profiles are predefined configurations for apps that further define their parameters.</span></span> <span data-ttu-id="d933f-231">Zu diesem Zeitpunkt werden die folgenden Profile unterstützt:</span><span class="sxs-lookup"><span data-stu-id="d933f-231">At this time, the following profiles are supported:</span></span>

* <span data-ttu-id="d933f-232">`IdentityServerSPA`: Stellt eine SPA gehostet werden, zusammen mit Identity Server als einzelne Einheit dar.</span><span class="sxs-lookup"><span data-stu-id="d933f-232">`IdentityServerSPA`: Represents a SPA hosted alongside IdentityServer as a single unit.</span></span>
  * <span data-ttu-id="d933f-233">Die `redirect_uri` standardmäßig `/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="d933f-233">The `redirect_uri` defaults to `/authentication/login-callback`.</span></span>
  * <span data-ttu-id="d933f-234">Die `post_logout_redirect_uri` standardmäßig `/authentication/logout-callback`.</span><span class="sxs-lookup"><span data-stu-id="d933f-234">The `post_logout_redirect_uri` defaults to `/authentication/logout-callback`.</span></span>
  * <span data-ttu-id="d933f-235">Der Satz von Bereichen umfasst die `openid`, `profile`, und alle Bereiche, die für die APIs in der app definiert.</span><span class="sxs-lookup"><span data-stu-id="d933f-235">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="d933f-236">Der Satz von zulässigen OIDC-Antworttypen ist `id_token token` oder jeweils einzeln (`id_token`, `token`).</span><span class="sxs-lookup"><span data-stu-id="d933f-236">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="d933f-237">Die zulässigen Antwortmodus ist `fragment`.</span><span class="sxs-lookup"><span data-stu-id="d933f-237">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="d933f-238">`SPA`: Stellt eine SPA, die gehostet wird, ist nicht mit Identity Server dar.</span><span class="sxs-lookup"><span data-stu-id="d933f-238">`SPA`: Represents a SPA that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="d933f-239">Der Satz von Bereichen umfasst die `openid`, `profile`, und alle Bereiche, die für die APIs in der app definiert.</span><span class="sxs-lookup"><span data-stu-id="d933f-239">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="d933f-240">Der Satz von zulässigen OIDC-Antworttypen ist `id_token token` oder jeweils einzeln (`id_token`, `token`).</span><span class="sxs-lookup"><span data-stu-id="d933f-240">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="d933f-241">Die zulässigen Antwortmodus ist `fragment`.</span><span class="sxs-lookup"><span data-stu-id="d933f-241">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="d933f-242">`IdentityServerJwt`: Stellt eine API, die zusammen mit gehostet wird, wird mit Identity Server dar.</span><span class="sxs-lookup"><span data-stu-id="d933f-242">`IdentityServerJwt`: Represents an API that is hosted alongside with IdentityServer.</span></span>
  * <span data-ttu-id="d933f-243">Die app ist für einen Bereich verfügen, der standardmäßig auf den Namen der app konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="d933f-243">The app is configured to have a single scope that defaults to the app name.</span></span>
* <span data-ttu-id="d933f-244">`API`: Stellt eine API, die gehostet wird, ist nicht mit Identity Server dar.</span><span class="sxs-lookup"><span data-stu-id="d933f-244">`API`: Represents an API that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="d933f-245">Die app ist für einen Bereich verfügen, der standardmäßig auf den Namen der app konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="d933f-245">The app is configured to have a single scope that defaults to the app name.</span></span>

### <a name="configuration-through-appsettings"></a><span data-ttu-id="d933f-246">Die Konfiguration über "appSettings"</span><span class="sxs-lookup"><span data-stu-id="d933f-246">Configuration through AppSettings</span></span>

<span data-ttu-id="d933f-247">Konfigurieren Sie die apps über das Konfigurationssystem, indem Sie sie in der Werteliste hinzufügen `Clients` oder `Resources`.</span><span class="sxs-lookup"><span data-stu-id="d933f-247">Configure the apps through the configuration system by adding them to the list of `Clients` or `Resources`.</span></span>

<span data-ttu-id="d933f-248">Konfigurieren des Clients `redirect_uri` und `post_logout_redirect_uri` -Eigenschaft, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="d933f-248">Configure each client's `redirect_uri` and `post_logout_redirect_uri` property, as shown in the following example:</span></span>

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

<span data-ttu-id="d933f-249">Wenn Sie Ressourcen zu konfigurieren, können Sie die Bereiche für die Ressource konfigurieren, wie unten dargestellt:</span><span class="sxs-lookup"><span data-stu-id="d933f-249">When configuring resources, you can configure the scopes for the resource as shown below:</span></span>

```json
"IdentityServer": {
  "Resources": {
    "MyExternalApi": {
      "Profile": "API",
      "Scopes": "a b c"
    }
  }
}
```

### <a name="configuration-through-code"></a><span data-ttu-id="d933f-250">Die Konfiguration über code</span><span class="sxs-lookup"><span data-stu-id="d933f-250">Configuration through code</span></span>

<span data-ttu-id="d933f-251">Sie können auch konfigurieren, die Clients und Ressourcen mithilfe von Code mithilfe einer Überladung der `AddApiAuthorization` , die immer dann eine Aktion aus, um Optionen zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="d933f-251">You can also configure the clients and resources through code using an overload of `AddApiAuthorization` that takes an action to configure options.</span></span>

```csharp
AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options =>
{
    options.Clients.AddSPA(
        "My SPA", spa =>
        spa.WithRedirectUri("http://www.example.com/authentication/login-callback")
           .WithLogoutRedirectUri(
               "http://www.example.com/authentication/logout-callback"));

    options.ApiResources.AddApiResource("MyExternalApi", resource =>
        resource.WithScopes("a", "b", "c"));
});
```

## <a name="additional-resources"></a><span data-ttu-id="d933f-252">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="d933f-252">Additional resources</span></span>

* <xref:spa/angular>
* <xref:spa/react>

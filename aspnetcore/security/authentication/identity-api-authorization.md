---
title: Einführung in die Authentifizierung für Single-Page-apps auf ASP.net Core
author: javiercn
description: Verwenden Sie die Identität mit einer einseitigen APP, die in einer ASP.net Core-App gehostet wird.
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 08/05/2019
uid: security/authentication/identity/spa
ms.openlocfilehash: 6b8818cc89a87e66ecec445ff8071348aacde64a
ms.sourcegitcommit: 2eb605f4f20ac4dd9de6c3b3e3453e108a357a21
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68819929"
---
# <a name="authentication-and-authorization-for-spas"></a><span data-ttu-id="ec25d-103">Authentifizierung und Autorisierung für Spas</span><span class="sxs-lookup"><span data-stu-id="ec25d-103">Authentication and authorization for SPAs</span></span>

<span data-ttu-id="ec25d-104">ASP.net Core 3,0 oder höher bietet die Authentifizierung in Single-Page-Apps (Spas) mithilfe der Unterstützung für die API-Autorisierung.</span><span class="sxs-lookup"><span data-stu-id="ec25d-104">ASP.NET Core 3.0 or later offers authentication in Single Page Apps (SPAs) using the support for API authorization.</span></span> <span data-ttu-id="ec25d-105">ASP.net Core Identität für das Authentifizieren und Speichern von Benutzern wird mit [identityserver](https://identityserver.io/) für die Implementierung von Open ID Connect kombiniert.</span><span class="sxs-lookup"><span data-stu-id="ec25d-105">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer](https://identityserver.io/) for implementing Open ID Connect.</span></span>

<span data-ttu-id="ec25d-106">Der **Angular** -und der-Projektvorlagen, die dem Authentifizierungs Parameter in der **Webanwendung (Model-View-Controller)** (MVC) und der **Webanwendung** (Razor Pages) ähneln, wurde ein Authentifizierungs Parameter hinzugefügt. Projektvorlagen.</span><span class="sxs-lookup"><span data-stu-id="ec25d-106">An authentication parameter was added to the **Angular** and **React** project templates that is similar to the authentication parameter in the **Web Application (Model-View-Controller)** (MVC) and **Web Application** (Razor Pages) project templates.</span></span> <span data-ttu-id="ec25d-107">Die zulässigen Parameterwerte sind **None** und **Individual**.</span><span class="sxs-lookup"><span data-stu-id="ec25d-107">The allowed parameter values are **None** and **Individual**.</span></span> <span data-ttu-id="ec25d-108">Die Projektvorlage "" "" "" "" "" "" "" "" "" "" ""</span><span class="sxs-lookup"><span data-stu-id="ec25d-108">The **React.js and Redux** project template doesn't support the authentication parameter at this time.</span></span>

## <a name="create-an-app-with-api-authorization-support"></a><span data-ttu-id="ec25d-109">Erstellen einer APP mit Unterstützung für die API-Autorisierung</span><span class="sxs-lookup"><span data-stu-id="ec25d-109">Create an app with API authorization support</span></span>

<span data-ttu-id="ec25d-110">Benutzerauthentifizierung und-Autorisierung können mit Angular-und reagingspas verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="ec25d-110">User authentication and authorization can be used with both Angular and React SPAs.</span></span> <span data-ttu-id="ec25d-111">Öffnen Sie eine Befehlsshell, und führen Sie den folgenden Befehl:</span><span class="sxs-lookup"><span data-stu-id="ec25d-111">Open a command shell, and run the following command:</span></span>

<span data-ttu-id="ec25d-112">**Angular**:</span><span class="sxs-lookup"><span data-stu-id="ec25d-112">**Angular**:</span></span>

```console
dotnet new angular -o <output_directory_name> -au Individual
```

<span data-ttu-id="ec25d-113">**Reagieren**:</span><span class="sxs-lookup"><span data-stu-id="ec25d-113">**React**:</span></span>

```console
dotnet new react -o <output_directory_name> -au Individual
```

<span data-ttu-id="ec25d-114">Der vorherige Befehl erstellt eine ASP.net Core-App mit einem *ClientApp* -Verzeichnis, das die Spa enthält.</span><span class="sxs-lookup"><span data-stu-id="ec25d-114">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the SPA.</span></span>

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a><span data-ttu-id="ec25d-115">Allgemeine Beschreibung der ASP.net Core Komponenten der APP</span><span class="sxs-lookup"><span data-stu-id="ec25d-115">General description of the ASP.NET Core components of the app</span></span>

<span data-ttu-id="ec25d-116">In den folgenden Abschnitten werden Ergänzungen zum-Projekt beschrieben, wenn die Authentifizierungs Unterstützung enthalten ist:</span><span class="sxs-lookup"><span data-stu-id="ec25d-116">The following sections describe additions to the project when authentication support is included:</span></span>

### <a name="startup-class"></a><span data-ttu-id="ec25d-117">Startup-Klasse</span><span class="sxs-lookup"><span data-stu-id="ec25d-117">Startup class</span></span>

<span data-ttu-id="ec25d-118">Die `Startup` -Klasse verfügt über die folgenden Ergänzungen:</span><span class="sxs-lookup"><span data-stu-id="ec25d-118">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="ec25d-119">In der `Startup.ConfigureServices` -Methode:</span><span class="sxs-lookup"><span data-stu-id="ec25d-119">Inside the `Startup.ConfigureServices` method:</span></span>
  * <span data-ttu-id="ec25d-120">Identität mit der Standardbenutzer Oberfläche:</span><span class="sxs-lookup"><span data-stu-id="ec25d-120">Identity with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="ec25d-121">Identityserver mit einer zusätzlichen `AddApiAuthorization` Hilfsmethode, mit der einige Standard ASP.net Core Konventionen auf identityserver fest verankert werden:</span><span class="sxs-lookup"><span data-stu-id="ec25d-121">IdentityServer with an additional `AddApiAuthorization` helper method that setups some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="ec25d-122">Authentifizierung mit einer zusätzlichen `AddIdentityServerJwt` Hilfsmethode, die die APP für die Validierung der von identityserver erstellten JWT-Token konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="ec25d-122">Authentication with an additional `AddIdentityServerJwt` helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="ec25d-123">In der `Startup.Configure` -Methode:</span><span class="sxs-lookup"><span data-stu-id="ec25d-123">Inside the `Startup.Configure` method:</span></span>
  * <span data-ttu-id="ec25d-124">Die Authentifizierungs Middleware, die für die Validierung der Anforderungs Anmelde Informationen und das Festlegen des Benutzers im Anforderungs Kontext zuständig ist:</span><span class="sxs-lookup"><span data-stu-id="ec25d-124">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="ec25d-125">Die identityserver-Middleware, die die Open ID Connect-Endpunkte verfügbar macht:</span><span class="sxs-lookup"><span data-stu-id="ec25d-125">The IdentityServer middleware that exposes the Open ID Connect endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="ec25d-126">Addapiauthorization</span><span class="sxs-lookup"><span data-stu-id="ec25d-126">AddApiAuthorization</span></span>

<span data-ttu-id="ec25d-127">Diese Hilfsmethode konfiguriert identityserver für die Verwendung der unterstützten Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="ec25d-127">This helper method configures IdentityServer to use our supported configuration.</span></span> <span data-ttu-id="ec25d-128">Identityserver ist ein leistungsfähiges und erweiterbares Framework für die Behandlung von App-Sicherheitsbedenken.</span><span class="sxs-lookup"><span data-stu-id="ec25d-128">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="ec25d-129">Gleichzeitig stellt dies unnötige Komplexität für die gängigsten Szenarien bereit.</span><span class="sxs-lookup"><span data-stu-id="ec25d-129">At the same time, that exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="ec25d-130">Folglich werden Ihnen eine Reihe von Konventionen und Konfigurationsoptionen zur Verfügung gestellt, die als guter Ausgangspunkt angesehen werden.</span><span class="sxs-lookup"><span data-stu-id="ec25d-130">Consequently, a set of conventions and configuration options is provided to you that are considered a good starting point.</span></span> <span data-ttu-id="ec25d-131">Nachdem sich Ihre Authentifizierung geändert hat, ist die volle Leistungsfähigkeit von identityserver weiterhin verfügbar, um die Authentifizierung an Ihre Bedürfnisse anzupassen.</span><span class="sxs-lookup"><span data-stu-id="ec25d-131">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit your needs.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="ec25d-132">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="ec25d-132">AddIdentityServerJwt</span></span>

<span data-ttu-id="ec25d-133">Diese Hilfsmethode konfiguriert ein Richtlinien Schema für die APP als Standard Authentifizierungs Handler.</span><span class="sxs-lookup"><span data-stu-id="ec25d-133">This helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="ec25d-134">Die Richtlinie ist so konfiguriert, dass die Identität alle Anforderungen verarbeitet, die an einen untergeordneten Pfad im Identitäts-URL-Bereich "/Identity" weitergeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="ec25d-134">The policy is configured to let Identity handle all requests routed to any subpath in the Identity URL space "/Identity".</span></span> <span data-ttu-id="ec25d-135">Der `JwtBearerHandler` verarbeitet alle anderen Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="ec25d-135">The `JwtBearerHandler` handles all other requests.</span></span> <span data-ttu-id="ec25d-136">Darüber hinaus registriert diese Methode eine `<<ApplicationName>>API` API-Ressource bei identityserver mit einem Standardbereich `<<ApplicationName>>API` von und konfiguriert die JWT-bearertoken-Middleware, um von identityserver für die APP ausgegebene Token zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="ec25d-136">Additionally, this method registers an `<<ApplicationName>>API` API resource with IdentityServer with a default scope of `<<ApplicationName>>API` and configures the JWT Bearer token middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="sampledatacontroller"></a><span data-ttu-id="ec25d-137">Sampledatacontroller</span><span class="sxs-lookup"><span data-stu-id="ec25d-137">SampleDataController</span></span>

<span data-ttu-id="ec25d-138">Beachten Sie in der Datei " *controllers\sampledatacontroller.cs* " das `[Authorize]` -Attribut, das auf die-Klasse angewendet wird, die angibt, dass der Benutzer auf der Basis der Standard Richtlinie für den Zugriff auf die Ressource autorisiert werden muss.</span><span class="sxs-lookup"><span data-stu-id="ec25d-138">In the *Controllers\SampleDataController.cs* file, notice the `[Authorize]` attribute applied to the class that indicates that the user needs to be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="ec25d-139">Die Standard Autorisierungs Richtlinie wird so konfiguriert, dass das standardmäßige Authentifizierungsschema verwendet wird, das von `AddIdentityServerJwt` dem oben erwähnten Richtlinien Schema festgelegt wird. Dadurch wird `JwtBearerHandler` die von dieser Hilfsmethode konfigurierte Standard Handler für verwendet. Anforderungen an die app.</span><span class="sxs-lookup"><span data-stu-id="ec25d-139">The default authorization policy happens to be configured to use the default authentication scheme, which is set up by `AddIdentityServerJwt` to the policy scheme that was mentioned above, making the `JwtBearerHandler` configured by such helper method the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="ec25d-140">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="ec25d-140">ApplicationDbContext</span></span>

<span data-ttu-id="ec25d-141">Beachten Sie in der Datei *data\applicationdbcontext.cs* , dass `DbContext` in der Identität dasselbe verwendet wird, mit der Ausnahme `ApiAuthorizationDbContext` , dass Sie erweitert wird ( `IdentityDbContext`eine stärker abgeleitete Klasse von), um das Schema für identityserver einzuschließen.</span><span class="sxs-lookup"><span data-stu-id="ec25d-141">In the *Data\ApplicationDbContext.cs* file, notice the same `DbContext` is used in Identity with the exception that it extends `ApiAuthorizationDbContext` (a more derived class from `IdentityDbContext`) to include the schema for IdentityServer.</span></span>

<span data-ttu-id="ec25d-142">Um die vollständige Kontrolle über das Datenbankschema zu erhalten, erben Sie von `DbContext` einer der verfügbaren Identitäts Klassen und konfigurieren den Kontext so, `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` dass das `OnModelCreating` Identitäts Schema durch Aufrufen von für die-Methode eingeschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="ec25d-142">To gain full control of the database schema, inherit from one of the available Identity `DbContext` classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` on the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="ec25d-143">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="ec25d-143">OidcConfigurationController</span></span>

<span data-ttu-id="ec25d-144">Beachten Sie in der Datei " *controllers\oidcconfigurationcontroller.cs* " den Endpunkt, der bereitgestellt wird, um die oidc-Parameter bereitzustellen, die vom Client verwendet werden müssen.</span><span class="sxs-lookup"><span data-stu-id="ec25d-144">In the *Controllers\OidcConfigurationController.cs* file, notice the endpoint that's provisioned to serve the OIDC parameters that the client needs to use.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="ec25d-145">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="ec25d-145">appsettings.json</span></span>

<span data-ttu-id="ec25d-146">In der *appSettings. JSON* -Datei des Projekt Stamms gibt es einen neuen `IdentityServer` Abschnitt, in dem die Liste der konfigurierten Clients beschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="ec25d-146">In the *appsettings.json* file of the project root, there's a new `IdentityServer` section that describes the list of configured clients.</span></span> <span data-ttu-id="ec25d-147">Im folgenden Beispiel gibt es einen einzelnen Client.</span><span class="sxs-lookup"><span data-stu-id="ec25d-147">In the following example, there's a single client.</span></span> <span data-ttu-id="ec25d-148">Der Client Name entspricht dem APP-Namen und wird dem OAuth `ClientId` -Parameter gemäß der Konvention zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="ec25d-148">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="ec25d-149">Das Profil gibt den App-Typ an, der konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="ec25d-149">The profile indicates the app type being configured.</span></span> <span data-ttu-id="ec25d-150">Sie wird intern verwendet, um Konventionen zu fördern, die den Konfigurationsprozess für den Server vereinfachen.</span><span class="sxs-lookup"><span data-stu-id="ec25d-150">It's used internally to drive conventions that simplify the configuration process for the server.</span></span> <span data-ttu-id="ec25d-151">Es stehen mehrere Profile zur Verfügung, wie im Abschnitt [Anwendungsprofile](#application-profiles) erläutert.</span><span class="sxs-lookup"><span data-stu-id="ec25d-151">There are several profiles available, as explained in the [Application profiles](#application-profiles) section.</span></span>

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a><span data-ttu-id="ec25d-152">appsettings.Development.json</span><span class="sxs-lookup"><span data-stu-id="ec25d-152">appsettings.Development.json</span></span>

<span data-ttu-id="ec25d-153">In *appSettings. Development. JSON* -Datei des Projekt Stamms. es gibt `IdentityServer` einen Abschnitt, in dem der Schlüssel zum Signieren von Token beschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="ec25d-153">In the *appsettings.Development.json* file of the project root, there's an `IdentityServer` section that describes the key used to sign tokens.</span></span> <span data-ttu-id="ec25d-154">Beim Bereitstellen in der Produktion muss neben der APP ein Schlüssel bereitgestellt und bereitgestellt werden, wie im Abschnitt bereitstellen [in der Produktion](#deploy-to-production) erläutert.</span><span class="sxs-lookup"><span data-stu-id="ec25d-154">When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section.</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a><span data-ttu-id="ec25d-155">Allgemeine Beschreibung der Angular-App</span><span class="sxs-lookup"><span data-stu-id="ec25d-155">General description of the Angular app</span></span>

<span data-ttu-id="ec25d-156">Die Unterstützung von Authentifizierung und API-Autorisierung in der Angular-Vorlage befindet sich in einem eigenen Angular-Modul im Verzeichnis *clientapp\src\api-Authorization* .</span><span class="sxs-lookup"><span data-stu-id="ec25d-156">The authentication and API authorization support in the Angular template resides in its own Angular module in the *ClientApp\src\api-authorization* directory.</span></span> <span data-ttu-id="ec25d-157">Das Modul besteht aus den folgenden Elementen:</span><span class="sxs-lookup"><span data-stu-id="ec25d-157">The module is composed of the following elements:</span></span>

* <span data-ttu-id="ec25d-158">3 Komponenten:</span><span class="sxs-lookup"><span data-stu-id="ec25d-158">3 components:</span></span>
  * <span data-ttu-id="ec25d-159">*login.component.ts*: Behandelt den Anmelde Fluss der app.</span><span class="sxs-lookup"><span data-stu-id="ec25d-159">*login.component.ts*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="ec25d-160">*logout.component.ts*: Verarbeitet den Abmelde Fluss der app.</span><span class="sxs-lookup"><span data-stu-id="ec25d-160">*logout.component.ts*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="ec25d-161">*login-menu.component.ts*: Ein Widget, das eine der folgenden Gruppen von Links anzeigt:</span><span class="sxs-lookup"><span data-stu-id="ec25d-161">*login-menu.component.ts*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="ec25d-162">Benutzerprofil Verwaltung und Abmelde Links, wenn der Benutzer authentifiziert ist.</span><span class="sxs-lookup"><span data-stu-id="ec25d-162">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="ec25d-163">Links zur Registrierung und Anmeldung, wenn der Benutzer nicht authentifiziert ist.</span><span class="sxs-lookup"><span data-stu-id="ec25d-163">Registration and log in links when the user isn't authenticated.</span></span>
* <span data-ttu-id="ec25d-164">Ein Weiterleitungs `AuthorizeGuard` Wächter, der Routen hinzugefügt werden kann und erfordert, dass ein Benutzer vor dem Besuch der Route authentifiziert wird.</span><span class="sxs-lookup"><span data-stu-id="ec25d-164">A route guard `AuthorizeGuard` that can be added to routes and requires a user to be authenticated before visiting the route.</span></span>
* <span data-ttu-id="ec25d-165">Ein HTTP-Interceptor `AuthorizeInterceptor` , der das Zugriffs Token an ausgehende HTTP-Anforderungen anbindet, die auf die API abzielen, wenn der Benutzer authentifiziert wird.</span><span class="sxs-lookup"><span data-stu-id="ec25d-165">An HTTP interceptor `AuthorizeInterceptor` that attaches the access token to outgoing HTTP requests targeting the API when the user is authenticated.</span></span>
* <span data-ttu-id="ec25d-166">Ein Dienst `AuthorizeService` , der die Details des Authentifizierungsprozesses auf niedrigerer Ebene behandelt und Informationen über den authentifizierten Benutzer für den Rest der App zur Nutzung verfügbar macht.</span><span class="sxs-lookup"><span data-stu-id="ec25d-166">A service `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>
* <span data-ttu-id="ec25d-167">Ein Angular-Modul, das Routen definiert, die den Authentifizierungs Teilen der APP zugeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="ec25d-167">An Angular module that defines routes associated with the authentication parts of the app.</span></span> <span data-ttu-id="ec25d-168">Er macht die Anmelde Menü Komponente, den Interceptor, den Wächter und den Dienst für die Nutzung durch den Rest der app verfügbar.</span><span class="sxs-lookup"><span data-stu-id="ec25d-168">It exposes the login menu component, the interceptor, the guard, and the service for consumption from the rest of the app.</span></span>

## <a name="general-description-of-the-react-app"></a><span data-ttu-id="ec25d-169">Allgemeine Beschreibung der App "reagieren"</span><span class="sxs-lookup"><span data-stu-id="ec25d-169">General description of the React app</span></span>

<span data-ttu-id="ec25d-170">Die Unterstützung für die Authentifizierung und API-Autorisierung in der "reagieren"-Vorlage befindet sich im Verzeichnis " *clientapp\src\components\api-Authorization* ".</span><span class="sxs-lookup"><span data-stu-id="ec25d-170">The support for authentication and API authorization in the React template resides in the *ClientApp\src\components\api-authorization* directory.</span></span> <span data-ttu-id="ec25d-171">Sie besteht aus den folgenden Elementen:</span><span class="sxs-lookup"><span data-stu-id="ec25d-171">It's composed of the following elements:</span></span>

* <span data-ttu-id="ec25d-172">4 Komponenten:</span><span class="sxs-lookup"><span data-stu-id="ec25d-172">4 components:</span></span>
  * <span data-ttu-id="ec25d-173">*Login.js*: Behandelt den Anmelde Fluss der app.</span><span class="sxs-lookup"><span data-stu-id="ec25d-173">*Login.js*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="ec25d-174">*Logout. js*: Verarbeitet den Abmelde Fluss der app.</span><span class="sxs-lookup"><span data-stu-id="ec25d-174">*Logout.js*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="ec25d-175">*LoginMenu.js*: Ein Widget, das eine der folgenden Gruppen von Links anzeigt:</span><span class="sxs-lookup"><span data-stu-id="ec25d-175">*LoginMenu.js*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="ec25d-176">Benutzerprofil Verwaltung und Abmelde Links, wenn der Benutzer authentifiziert ist.</span><span class="sxs-lookup"><span data-stu-id="ec25d-176">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="ec25d-177">Links zur Registrierung und Anmeldung, wenn der Benutzer nicht authentifiziert ist.</span><span class="sxs-lookup"><span data-stu-id="ec25d-177">Registration and log in links when the user isn't authenticated.</span></span>
  * <span data-ttu-id="ec25d-178">*Autorizeroute. js*: Eine Routen Komponente, bei der ein Benutzer vor dem Rendern der im `Component` -Parameter angegeben Komponente authentifiziert werden muss.</span><span class="sxs-lookup"><span data-stu-id="ec25d-178">*AuthorizeRoute.js*: A route component that requires a user to be authenticated before rendering the component indicated in the `Component` parameter.</span></span>
* <span data-ttu-id="ec25d-179">Eine exportierte `authService` Instanz der `AuthorizeService` -Klasse, die die Details des Authentifizierungsprozesses auf niedrigerer Ebene behandelt und Informationen über den authentifizierten Benutzer für den Rest der App zur Nutzung verfügbar macht.</span><span class="sxs-lookup"><span data-stu-id="ec25d-179">An exported `authService` instance of class `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>

<span data-ttu-id="ec25d-180">Nachdem Sie die Hauptkomponenten der Lösung kennengelernt haben, können Sie sich einen tieferen Einblick in die einzelnen Szenarios für die APP machen.</span><span class="sxs-lookup"><span data-stu-id="ec25d-180">Now that you've seen the main components of the solution, you can take a deeper look at individual scenarios for the app.</span></span>

## <a name="require-authorization-on-a-new-api"></a><span data-ttu-id="ec25d-181">Autorisierung für eine neue API erforderlich</span><span class="sxs-lookup"><span data-stu-id="ec25d-181">Require authorization on a new API</span></span>

<span data-ttu-id="ec25d-182">Standardmäßig ist das System so konfiguriert, dass die Autorisierung für neue APIs problemlos erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="ec25d-182">By default, the system is configured to easily require authorization for new APIs.</span></span> <span data-ttu-id="ec25d-183">Erstellen Sie hierzu einen neuen Controller, und fügen Sie das `[Authorize]` -Attribut zur Controller Klasse oder zu einer beliebigen Aktion im Controller hinzu.</span><span class="sxs-lookup"><span data-stu-id="ec25d-183">To do so, create a new controller and add the `[Authorize]` attribute to the controller class or to any action within the controller.</span></span>

## <a name="protect-a-client-side-route-angular"></a><span data-ttu-id="ec25d-184">Schützen einer Client seitigen Route (Angular)</span><span class="sxs-lookup"><span data-stu-id="ec25d-184">Protect a client-side route (Angular)</span></span>

<span data-ttu-id="ec25d-185">Der Schutz einer Client seitigen Route erfolgt durch Hinzufügen von Autorisierungs Wächter zur Liste der Wächter, die beim Konfigurieren einer Route ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="ec25d-185">Protecting a client-side route is done by adding the authorize guard to the list of guards to run when configuring a route.</span></span> <span data-ttu-id="ec25d-186">Beispielsweise können Sie sehen, wie die `fetch-data` Route innerhalb des Haupt-App-Angular-Moduls konfiguriert ist:</span><span class="sxs-lookup"><span data-stu-id="ec25d-186">As an example, you can see how the `fetch-data` route is configured within the main app Angular module:</span></span>

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

<span data-ttu-id="ec25d-187">Beachten Sie, dass der Schutz einer Route nicht den eigentlichen Endpunkt schützt (für den immer noch ein `[Authorize]` angewendetes Attribut erforderlich ist), sondern dass der Benutzer nur die Navigation zur angegebenen Client seitigen Route verhindert, wenn er nicht authentifiziert ist.</span><span class="sxs-lookup"><span data-stu-id="ec25d-187">It's important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-angular"></a><span data-ttu-id="ec25d-188">Authentifizieren von API-Anforderungen (Angular)</span><span class="sxs-lookup"><span data-stu-id="ec25d-188">Authenticate API requests (Angular)</span></span>

<span data-ttu-id="ec25d-189">Die Authentifizierung von Anforderungen an APIs, die zusammen mit der APP gehostet werden, erfolgt automatisch durch die Verwendung des von der APP definierten http-Client-Interceptors.</span><span class="sxs-lookup"><span data-stu-id="ec25d-189">Authenticating requests to APIs hosted alongside the app is done automatically through the use of the HTTP client interceptor defined by the app.</span></span>

## <a name="protect-a-client-side-route-react"></a><span data-ttu-id="ec25d-190">Schützen einer Client seitigen Route (reagieren)</span><span class="sxs-lookup"><span data-stu-id="ec25d-190">Protect a client-side route (React)</span></span>

<span data-ttu-id="ec25d-191">Schützen Sie eine Client seitige Route mithilfe der `AuthorizeRoute` Komponente anstelle der einfachen `Route` Komponente.</span><span class="sxs-lookup"><span data-stu-id="ec25d-191">Protect a client-side route by using the `AuthorizeRoute` component instead of the plain `Route` component.</span></span> <span data-ttu-id="ec25d-192">Beachten Sie z. b. `fetch-data` , wie die Route innerhalb `App` der-Komponente konfiguriert ist:</span><span class="sxs-lookup"><span data-stu-id="ec25d-192">For example, notice how the `fetch-data` route is configured within the `App` component:</span></span>

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

<span data-ttu-id="ec25d-193">Schützen einer Route:</span><span class="sxs-lookup"><span data-stu-id="ec25d-193">Protecting a route:</span></span>

* <span data-ttu-id="ec25d-194">Schützt nicht den eigentlichen Endpunkt (für den immer noch `[Authorize]` ein angewendetes Attribut erforderlich ist).</span><span class="sxs-lookup"><span data-stu-id="ec25d-194">Doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it).</span></span>
* <span data-ttu-id="ec25d-195">Verhindert, dass der Benutzer zur angegebenen Client seitigen Route navigiert, wenn er nicht authentifiziert ist.</span><span class="sxs-lookup"><span data-stu-id="ec25d-195">Only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-react"></a><span data-ttu-id="ec25d-196">Authentifizieren von API-Anforderungen (reagieren)</span><span class="sxs-lookup"><span data-stu-id="ec25d-196">Authenticate API requests (React)</span></span>

<span data-ttu-id="ec25d-197">Das Authentifizieren von Anforderungen mit reagieren erfolgt durch das erste `authService` Importieren der- `AuthorizeService`Instanz aus.</span><span class="sxs-lookup"><span data-stu-id="ec25d-197">Authenticating requests with React is done by first importing the `authService` instance from the `AuthorizeService`.</span></span> <span data-ttu-id="ec25d-198">Das Zugriffs Token wird aus dem `authService` abgerufen und wie unten dargestellt an die Anforderung angefügt.</span><span class="sxs-lookup"><span data-stu-id="ec25d-198">The access token is retrieved from the `authService` and is attached to the request as shown below.</span></span> <span data-ttu-id="ec25d-199">In reagiere Komponenten wird diese Aufgabe in der Regel in `componentDidMount` der Lebenszyklus Methode oder als Ergebnis einer Benutzerinteraktion ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="ec25d-199">In React components, this work is typically done in the `componentDidMount` lifecycle method or as the result from some user interaction.</span></span>

### <a name="import-the-authservice-into-your-component"></a><span data-ttu-id="ec25d-200">Importieren Sie den authService in Ihre Komponente.</span><span class="sxs-lookup"><span data-stu-id="ec25d-200">Import the authService into your component</span></span>

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a><span data-ttu-id="ec25d-201">Abrufen und Anfügen des Zugriffs Tokens an die Antwort</span><span class="sxs-lookup"><span data-stu-id="ec25d-201">Retrieve and attach the access token to the response</span></span>

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

## <a name="deploy-to-production"></a><span data-ttu-id="ec25d-202">In Produktionsumgebungen bereitstellen</span><span class="sxs-lookup"><span data-stu-id="ec25d-202">Deploy to production</span></span>

<span data-ttu-id="ec25d-203">Um die app in der Produktionsumgebung bereitzustellen, müssen die folgenden Ressourcen bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="ec25d-203">To deploy the app to production, the following resources need to be provisioned:</span></span>

* <span data-ttu-id="ec25d-204">Eine Datenbank zum Speichern der Identitäts Benutzerkonten und identityserver-Zuweisungen.</span><span class="sxs-lookup"><span data-stu-id="ec25d-204">A database to store the Identity user accounts and the IdentityServer grants.</span></span>
* <span data-ttu-id="ec25d-205">Ein Produktions Zertifikat, das zum Signieren von Token verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="ec25d-205">A production certificate to use for signing tokens.</span></span>
  * <span data-ttu-id="ec25d-206">Es gibt keine speziellen Anforderungen für dieses Zertifikat. Dabei kann es sich um ein selbst signiertes Zertifikat oder um ein Zertifikat handeln, das über eine Zertifizierungsstelle bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="ec25d-206">There are no specific requirements for this certificate; it can be a self-signed certificate or a certificate provisioned through a CA authority.</span></span>
  * <span data-ttu-id="ec25d-207">Sie kann über Standard Tools wie PowerShell oder OpenSSL generiert werden.</span><span class="sxs-lookup"><span data-stu-id="ec25d-207">It can be generated through standard tools like PowerShell or OpenSSL.</span></span>
  * <span data-ttu-id="ec25d-208">Sie kann im Zertifikat Speicher auf den Ziel Computern installiert oder als *PFX* -Datei mit einem sicheren Kennwort bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="ec25d-208">It can be installed into the certificate store on the target machines or deployed as a *.pfx* file with a strong password.</span></span>

### <a name="example-deploy-to-azure-websites"></a><span data-ttu-id="ec25d-209">Beispiel: Bereitstellen auf Azure-Websites</span><span class="sxs-lookup"><span data-stu-id="ec25d-209">Example: Deploy to Azure Websites</span></span>

<span data-ttu-id="ec25d-210">In diesem Abschnitt wird beschrieben, wie Sie die app in Azure Websites mithilfe eines Zertifikats bereitstellen, das im Zertifikat Speicher gespeichert ist</span><span class="sxs-lookup"><span data-stu-id="ec25d-210">This section describes deploying the app to Azure websites using a certificate stored in the certificate store.</span></span> <span data-ttu-id="ec25d-211">Um die APP so zu ändern, dass ein Zertifikat aus dem Zertifikat Speicher geladen wird, muss sich der APP Service Plan mindestens auf dem Standard-Tarif befinden, wenn Sie in einem späteren Schritt konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="ec25d-211">To modify the app to load a certificate from the certificate store, the App Service plan needs to be on at least the Standard tier when you configure in a later step.</span></span> <span data-ttu-id="ec25d-212">Ändern`IdentityServer` Sie in der Datei *appSettings. JSON* der APP den Abschnitt, um die Schlüssel Details einzuschließen:</span><span class="sxs-lookup"><span data-stu-id="ec25d-212">In the app's *appsettings.json* file, modify the `IdentityServer` section to include the key details:</span></span>

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

* <span data-ttu-id="ec25d-213">Die Name-Eigenschaft für das Zertifikat entspricht dem Distinguished Subject für das Zertifikat.</span><span class="sxs-lookup"><span data-stu-id="ec25d-213">The name property on certificate corresponds with the distinguished subject for the certificate.</span></span>
* <span data-ttu-id="ec25d-214">Der Speicherort stellt dar, wo das Zertifikat geladen werden`CurrentUser` soll `LocalMachine`(oder).</span><span class="sxs-lookup"><span data-stu-id="ec25d-214">The store location represents where to load the certificate from (`CurrentUser` or `LocalMachine`).</span></span>
* <span data-ttu-id="ec25d-215">Der Speicher Name steht für den Namen des Zertifikat Speicher, in dem das Zertifikat gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="ec25d-215">The store name represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="ec25d-216">In diesem Fall verweist Sie auf den persönlichen Benutzerspeicher.</span><span class="sxs-lookup"><span data-stu-id="ec25d-216">In this case, it points to the personal user store.</span></span>

<span data-ttu-id="ec25d-217">Stellen Sie die APP bereit, indem Sie die Schritte unter Bereitstellen [der app in Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) bereitstellen, um die erforderlichen Azure-Ressourcen zu erstellen und die app in der Produktionsumgebung bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="ec25d-217">To deploy to Azure Websites, deploy the app following the steps in [Deploy the app to Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) to create the necessary Azure resources and deploy the app to production.</span></span>

<span data-ttu-id="ec25d-218">Nachdem Sie die obigen Anweisungen ausgeführt haben, wird die app in Azure bereitgestellt, ist aber noch nicht funktionsfähig.</span><span class="sxs-lookup"><span data-stu-id="ec25d-218">After following the preceding instructions, the app is deployed to Azure but isn't yet functional.</span></span> <span data-ttu-id="ec25d-219">Das von der APP verwendete Zertifikat muss noch eingerichtet werden.</span><span class="sxs-lookup"><span data-stu-id="ec25d-219">The certificate used by the app still needs to be set up.</span></span> <span data-ttu-id="ec25d-220">Suchen Sie den Fingerabdruck des zu verwendenden Zertifikats, und befolgen Sie die Schritte unter [Laden von Zertifikaten](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span><span class="sxs-lookup"><span data-stu-id="ec25d-220">Locate the thumbprint for the certificate to be used, and follow the steps described in [Load your certificates](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span></span>

<span data-ttu-id="ec25d-221">Obwohl diese Schritte SSL erwähnen, gibt es einen Abschnitt mit **privaten Zertifikaten** im Portal, in dem Sie das bereitgestellte Zertifikat für die Verwendung mit der APP hochladen können.</span><span class="sxs-lookup"><span data-stu-id="ec25d-221">While these steps mention SSL, there's a **Private certificates** section on the portal where you can upload the provisioned certificate to use with the app.</span></span>

<span data-ttu-id="ec25d-222">Starten Sie die APP nach diesem Schritt neu, damit Sie funktionsfähig ist.</span><span class="sxs-lookup"><span data-stu-id="ec25d-222">After this step, restart the app and it should be functional.</span></span>

## <a name="other-configuration-options"></a><span data-ttu-id="ec25d-223">Andere Konfigurationsoptionen</span><span class="sxs-lookup"><span data-stu-id="ec25d-223">Other configuration options</span></span>

<span data-ttu-id="ec25d-224">Die Unterstützung für die API-Autorisierung basiert auf identityserver mit einem Satz von Konventionen, Standardwerten und Verbesserungen, um die Bedienung für Spas zu vereinfachen.</span><span class="sxs-lookup"><span data-stu-id="ec25d-224">The support for API authorization builds on top of IdentityServer with a set of conventions, default values, and enhancements to simplify the experience for SPAs.</span></span> <span data-ttu-id="ec25d-225">Natürlich ist die volle Leistungsfähigkeit von identityserver im Hintergrund verfügbar, wenn die ASP.net Core Integrationen Ihr Szenario nicht abdecken.</span><span class="sxs-lookup"><span data-stu-id="ec25d-225">Needless to say, the full power of IdentityServer is available behind the scenes if the ASP.NET Core integrations don't cover your scenario.</span></span> <span data-ttu-id="ec25d-226">Die ASP.net Core-Unterstützung konzentriert sich auf "erst Anbieter-Apps", bei denen alle apps von unserer Organisation erstellt und bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="ec25d-226">The ASP.NET Core support is focused on "first-party" apps, where all the apps are created and deployed by our organization.</span></span> <span data-ttu-id="ec25d-227">Daher wird die Unterstützung für Dinge wie die Zustimmung oder den Verbund nicht angeboten.</span><span class="sxs-lookup"><span data-stu-id="ec25d-227">As such, support isn't offered for things like consent or federation.</span></span> <span data-ttu-id="ec25d-228">Verwenden Sie für diese Szenarios identityserver, und befolgen Sie die zugehörigen Dokumentationen.</span><span class="sxs-lookup"><span data-stu-id="ec25d-228">For those scenarios, use IdentityServer and follow their documentation.</span></span>

### <a name="application-profiles"></a><span data-ttu-id="ec25d-229">Anwendungsprofile</span><span class="sxs-lookup"><span data-stu-id="ec25d-229">Application profiles</span></span>

<span data-ttu-id="ec25d-230">Anwendungsprofile sind vordefinierte Konfigurationen für apps, die ihre Parameter weiter definieren.</span><span class="sxs-lookup"><span data-stu-id="ec25d-230">Application profiles are predefined configurations for apps that further define their parameters.</span></span> <span data-ttu-id="ec25d-231">Zu diesem Zeitpunkt werden die folgenden Profile unterstützt:</span><span class="sxs-lookup"><span data-stu-id="ec25d-231">At this time, the following profiles are supported:</span></span>

* <span data-ttu-id="ec25d-232">`IdentityServerSPA`: Stellt eine von identityserver gehostete Spa als einzelne Einheit dar.</span><span class="sxs-lookup"><span data-stu-id="ec25d-232">`IdentityServerSPA`: Represents a SPA hosted alongside IdentityServer as a single unit.</span></span>
  * <span data-ttu-id="ec25d-233">`redirect_uri` Der`/authentication/login-callback`Standardwert ist.</span><span class="sxs-lookup"><span data-stu-id="ec25d-233">The `redirect_uri` defaults to `/authentication/login-callback`.</span></span>
  * <span data-ttu-id="ec25d-234">`post_logout_redirect_uri` Der`/authentication/logout-callback`Standardwert ist.</span><span class="sxs-lookup"><span data-stu-id="ec25d-234">The `post_logout_redirect_uri` defaults to `/authentication/logout-callback`.</span></span>
  * <span data-ttu-id="ec25d-235">Der Satz von Bereichen umfasst `openid`, und alle Bereiche, die für die APIs in der APP definiert sind. `profile`</span><span class="sxs-lookup"><span data-stu-id="ec25d-235">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="ec25d-236">Der Satz zulässiger oidc-Antworttypen `id_token token` ist oder jeweils einzeln (`id_token`, `token`).</span><span class="sxs-lookup"><span data-stu-id="ec25d-236">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="ec25d-237">Der zulässige Antwortmodus ist `fragment`.</span><span class="sxs-lookup"><span data-stu-id="ec25d-237">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="ec25d-238">`SPA`: Stellt eine Spa dar, die nicht mit identityserver gehostet wird.</span><span class="sxs-lookup"><span data-stu-id="ec25d-238">`SPA`: Represents a SPA that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="ec25d-239">Der Satz von Bereichen umfasst `openid`, und alle Bereiche, die für die APIs in der APP definiert sind. `profile`</span><span class="sxs-lookup"><span data-stu-id="ec25d-239">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="ec25d-240">Der Satz zulässiger oidc-Antworttypen `id_token token` ist oder jeweils einzeln (`id_token`, `token`).</span><span class="sxs-lookup"><span data-stu-id="ec25d-240">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="ec25d-241">Der zulässige Antwortmodus ist `fragment`.</span><span class="sxs-lookup"><span data-stu-id="ec25d-241">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="ec25d-242">`IdentityServerJwt`: Stellt eine API dar, die zusammen mit identityserver gehostet wird.</span><span class="sxs-lookup"><span data-stu-id="ec25d-242">`IdentityServerJwt`: Represents an API that is hosted alongside with IdentityServer.</span></span>
  * <span data-ttu-id="ec25d-243">Die APP ist so konfiguriert, dass Sie einen einzelnen Bereich hat, der standardmäßig den APP-Namen verwendet.</span><span class="sxs-lookup"><span data-stu-id="ec25d-243">The app is configured to have a single scope that defaults to the app name.</span></span>
* <span data-ttu-id="ec25d-244">`API`: Stellt eine API dar, die nicht mit identityserver gehostet wird.</span><span class="sxs-lookup"><span data-stu-id="ec25d-244">`API`: Represents an API that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="ec25d-245">Die APP ist so konfiguriert, dass Sie einen einzelnen Bereich hat, der standardmäßig den APP-Namen verwendet.</span><span class="sxs-lookup"><span data-stu-id="ec25d-245">The app is configured to have a single scope that defaults to the app name.</span></span>

### <a name="configuration-through-appsettings"></a><span data-ttu-id="ec25d-246">Konfiguration über appSettings</span><span class="sxs-lookup"><span data-stu-id="ec25d-246">Configuration through AppSettings</span></span>

<span data-ttu-id="ec25d-247">Konfigurieren Sie die apps über das Konfigurationssystem, indem Sie Sie der Liste `Clients` oder `Resources`hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="ec25d-247">Configure the apps through the configuration system by adding them to the list of `Clients` or `Resources`.</span></span>

<span data-ttu-id="ec25d-248">Konfigurieren Sie die- `redirect_uri` Eigenschaft `post_logout_redirect_uri` und die-Eigenschaft des Clients, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="ec25d-248">Configure each client's `redirect_uri` and `post_logout_redirect_uri` property, as shown in the following example:</span></span>

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

<span data-ttu-id="ec25d-249">Wenn Sie Ressourcen konfigurieren, können Sie die Bereiche für die Ressource konfigurieren, wie unten dargestellt:</span><span class="sxs-lookup"><span data-stu-id="ec25d-249">When configuring resources, you can configure the scopes for the resource as shown below:</span></span>

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

### <a name="configuration-through-code"></a><span data-ttu-id="ec25d-250">Konfiguration durch Code</span><span class="sxs-lookup"><span data-stu-id="ec25d-250">Configuration through code</span></span>

<span data-ttu-id="ec25d-251">Sie können die Clients und Ressourcen auch über den Code konfigurieren, indem Sie `AddApiAuthorization` eine Überladung von verwenden, die eine Aktion zum Konfigurieren von Optionen übernimmt.</span><span class="sxs-lookup"><span data-stu-id="ec25d-251">You can also configure the clients and resources through code using an overload of `AddApiAuthorization` that takes an action to configure options.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="ec25d-252">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="ec25d-252">Additional resources</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>

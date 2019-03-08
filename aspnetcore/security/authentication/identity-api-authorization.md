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
ms.sourcegitcommit: 191d21c1e37b56f0df0187e795d9a56388bbf4c7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/08/2019
ms.locfileid: "57665336"
---
# <a name="authentication-and-authorization-for-spas"></a>Authentifizierung und Autorisierung für SPAs

ASP.NET Core 3.0 oder höher bietet die Authentifizierung in einzelnen-Page-Webanwendungen (SPAs) mithilfe der Unterstützung für die API-Autorisierung. ASP.NET Core-Identität für die Authentifizierung und Speichern von Benutzern mit kombiniert [IdentityServer](https://identityserver.io/) für die Implementierung des Open ID Connect.

Ein Authentifizierungsparameter hinzugefügt wurde die **Angular** und **reagieren** -Projektvorlagen, die an den Parameter "Authentication" in ähnelt der **Webanwendung (Model-View-Controller)**  (MVC) und **Webanwendung** (Razor Pages)-Projektvorlagen. Die zulässige Parameterwerte sind **keine** und **einzelne**. Die **React.js und Redux** Projektvorlage nicht den Authentifizierungsparameter zu diesem Zeitpunkt nicht unterstützt.

## <a name="create-an-app-with-api-authorization-support"></a>Erstellen einer app mit Unterstützung für die Autorisierung-API

Benutzerauthentifizierung und-Autorisierung können mit Angular und React-SPAs verwendet werden. Öffnen Sie eine Befehlsshell, und führen Sie den folgenden Befehl:

**Angular**:

```console
dotnet new angular -o <output_directory_name> -au Individual
```

**REACT**:

```console
dotnet new react -o <output_directory_name> -au Individual
```

Der vorherige Befehl erstellt eine ASP.NET Core-app mit einem *ClientApp* Verzeichnis mit der einseitigen Anwendung.

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a>Allgemeine Beschreibung von ASP.NET Core-Komponenten der app

Den folgenden Abschnitten werden die Erweiterungen für das Projekt, bei der Unterstützung der Authentifizierung enthalten ist:

### <a name="startup-class"></a>Startup-Klasse

Die `Startup` -Klasse verfügt über den folgenden Ergänzungen:

* In der `Startup.ConfigureServices` Methode:
  * Mit der standardmäßigen UI-Identität:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * Identity Server mit einem zusätzlichen `AddApiAuthorization` Hilfsmethode dieses Setups einige standardmäßige ASP.NET Core-Konventionen über Identity Server:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Authentifizierung mit einem zusätzlichen `AddIdentityServerJwt` Hilfsmethode, die die app, um das Überprüfen von JWT-Token konfiguriert, die von Identity Server erzeugt:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* In der `Startup.Configure` Methode:
  * Die authentifizierungsmiddleware, die für die Anmeldeinformationen für den Anforderung überprüft und Festlegen des Benutzers im Kontext der Anforderung verantwortlich ist:

    ```csharp
    app.UseAuthentication();
    ```

  * Die Identity Server-Middleware, die die Open ID Connect-Endpunkte verfügbar macht:

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

Diese Hilfsmethode konfiguriert Identity Server, um unsere unterstützte Konfiguration zu verwenden. Identity Server ist ein leistungsfähiges und erweiterbares Framework für die Behandlung von Sicherheitsrisiken für die app. Zur gleichen Zeit verfügbar macht, die unnötigen Komplexität für die häufigsten Szenarien. Daher ist ein Satz von Konventionen und Konfigurationsoptionen für Sie bereitgestellt, die einen guten Ausgangspunkt betrachtet werden. Nach Ihrer Authentifizierung Änderungen erfordert, ist das volle Potenzial von Identity Server weiterhin verfügbar, um die Authentifizierung entsprechend Ihren Anforderungen anzupassen.

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

Diese Hilfsmethode wird ein Schema der Richtlinie für die app als der Standardhandler für die Authentifizierung konfiguriert. Die Richtlinie ist so konfiguriert, dass die Identität, die alle Anforderungen an alle untergeordneten Pfad im Bereich der URL des Identitätsanbieters weitergeleitet zu verarbeiten können "/ Identity". Die `JwtBearerHandler` verarbeitet alle anderen Anforderungen. Darüber hinaus diese Methode registriert eine `<<ApplicationName>>API` -API-Ressource mit Identity Server mit einem Standardbereich des `<<ApplicationName>>API` und die JWT-Trägertoken-Middleware zum Überprüfen von Token, die von Identity Server ausgestellt wird, für die app konfiguriert.

### <a name="sampledatacontroller"></a>SampleDataController

In der *Controllers\SampleDataController.cs* Datei, beachten Sie die `[Authorize]` -Attribut nicht angewendet werden, auf die Klasse, der angibt, dass der Benutzer autorisiert werden muss, basierend auf die Standardrichtlinie auf die Ressource zuzugreifen. Die Standardrichtlinie für die Autorisierung konfiguriert werden, um das Standardschema für die Authentifizierung, verwenden, die vom eingerichtet ist, dass geschieht `AddIdentityServerJwt` mit dem Richtlinien-Schema, das weiter oben erwähnt wurde, wodurch die `JwtBearerHandler` konfiguriert, indem diese Hilfsmethode den Standardhandler für Anforderungen an die app.

### <a name="applicationdbcontext"></a>ApplicationDbContext

In der *Data\ApplicationDbContext.cs* Datei, die gleichen `DbContext` werden in der Identität mit der Ausnahme, die sie erweitert `ApiAuthorizationDbContext` (eine abgeleitete Klasse von `IdentityDbContext`), die das Schema für IdentityServer zu müssen.

Um vollständige Kontrolle über das Datenbankschema zu erhalten, erben von einem der verfügbaren Identität `DbContext` Klassen aus, und konfigurieren Sie den Kontext, um das Schema für die Identität durch Aufrufen von aufzunehmen `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` auf die `OnModelCreating` Methode.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

In der *Controllers\OidcConfigurationController.cs* Datei, beachten Sie, dass den Endpunkt, der bereitgestellt wird, um die OIDC-Parameter zu verarbeiten, die der Client verwenden muss.

### <a name="appsettingsjson"></a>appsettings.json

In der *"appSettings.JSON"* Datei von das Stammverzeichnis des Projekts, es gibt eine neue `IdentityServer` Abschnitt mit die Liste der Beschreibungen konfigurierten Clients. Im folgenden Beispiel wird ein einzelner Client vorhanden. Clientname entspricht der Name der app und wird gemäß der Konvention, die OAuth zugeordnet `ClientId` Parameter. Das Profil gibt an, der app-Typ, der konfiguriert wird. Sie wird intern zum Laufwerk Konventionen verwendet, die den Konfigurationsprozess für den Server zu vereinfachen. Es gibt mehrere Profile verfügbar sind, wie unter der [Anwendungsprofile](#application-profiles) Abschnitt.

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a>appsettings.Development.json

In der *"appSettings". Development.JSON* Datei, der das Stammverzeichnis des Projekts, gibt es eine `IdentityServer` Abschnitt, den Schlüssel zum Signieren von Token beschrieben. Wenn Sie in der produktionsumgebung bereitstellen, ein Schlüssel muss bereitgestellt und zusammen mit der app bereitgestellt werden wie unter der [bereitstellen, in der Produktion](#deploy-to-production) Abschnitt.

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a>Allgemeine Beschreibung von der Angular-app

Die Authentifizierung und Autorisierung für API-Unterstützung in der Angular Vorlage befindet sich im eigenen Angular-Modul in die *ClientApp\src\api-Authorization* Verzeichnis. Das Modul besteht aus den folgenden Elementen:

* 3-Komponenten:
  * *login.component.ts*: Verarbeitet die app Anmeldung.
  * *logout.component.ts*: Der app Abmelden Flow wird verarbeitet.
  * *login-menu.component.ts*: Ein Widget, das eine der folgenden Links angezeigt:
    * Benutzerprofilverwaltung, und melden Sie sich Links, wenn der Benutzer authentifiziert ist.
    * Die Registrierung und Anmeldung über die Links, wenn der Benutzer nicht authentifiziert ist.
* Eine Route Guard `AuthorizeGuard` , die hinzugefügt werden können, um die sprachliche und erfordert, dass einen Benutzer authentifiziert werden, bevor der Zugriff auf die Route.
* Ein HTTP-Interceptor `AuthorizeInterceptor` , die das Zugriffstoken auf ausgehenden HTTP-Anforderungen, die für die API aus, wenn der Benutzer authentifiziert ist angefügt.
* Ein Dienst `AuthorizeService` , kümmert sich um die Low-Level-Details des Authentifizierungsprozesses und Informationen zu den authentifizierten Benutzer auf den Rest der app für die Nutzung verfügbar gemacht.
* Ein Angular-Modul, die Routen, die die Authentifizierung Teile der app definiert. Es stellt die Anmeldung im Menü-Komponente, den Interceptor, den Wächter und der Dienst für die Nutzung vom Rest der app.

## <a name="general-description-of-the-react-app"></a>Allgemeine Beschreibung der React-app

Die Unterstützung für die Authentifizierung und Autorisierung der API in der React-Vorlage befindet sich in der *ClientApp\src\components\api-Authorization* Verzeichnis. Es besteht aus den folgenden Elementen:

* 4-Komponenten:
  * *Login.js*: Verarbeitet die app Anmeldung.
  * *Logout.js*: Der app Abmelden Flow wird verarbeitet.
  * *LoginMenu.js*: Ein Widget, das eine der folgenden Links angezeigt:
    * Benutzerprofilverwaltung, und melden Sie sich Links, wenn der Benutzer authentifiziert ist.
    * Die Registrierung und Anmeldung über die Links, wenn der Benutzer nicht authentifiziert ist.
  * *AuthorizeRoute.js*: Eine Route-Komponente, die benötigt ein Benutzer authentifiziert werden, vor dem Rendern der Komponente angegeben, der `Component` Parameter.
* Eine exportierte `authService` Instanz der Klasse `AuthorizeService` , kümmert sich um die Low-Level-Details des Authentifizierungsprozesses und Informationen zu den authentifizierten Benutzer auf den Rest der app für die Nutzung verfügbar gemacht.

Nun, da Sie die Hauptkomponenten der Lösung gesehen haben, bringen Sie einen tieferen Einblick in die einzelnen Szenarien für die app.

## <a name="require-authorization-on-a-new-api"></a>Eine Autorisierung auf einer neuen API

Standardmäßig konfiguriert das System ist einfach der Autorisierung für die neuen APIs erforderlich ist. Zu diesem Zweck erstellen Sie einen neuen Controller, und fügen die `[Authorize]` -Attribut zur Controllerklasse oder auf eine beliebige Aktion im Controller.

## <a name="protect-a-client-side-route-angular"></a>Schützen Sie eine Route für die clientseitige (Angular)

Schützen eine Route für die clientseitige erfolgt durch Hinzufügen der Authorize-Guard zur Liste der Wächter ausgeführt wird, wenn Sie eine Route zu konfigurieren. Beispielsweise können Sie sehen die `fetch-data` Route in der Haupt-app-Angular-Modul konfiguriert:

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

Es ist wichtig zu erwähnen, dass eine Route schützen nicht den tatsächlichen Endpunkt schützen (erfordert immer noch eine `[Authorize]` angewendet werden), aber es nur verhindert, dass den Benutzer navigieren zu der angegebenen Client-Side-Route, wenn sie nicht authentifiziert ist.

## <a name="authenticate-api-requests-angular"></a>Authentifizieren der API-Anforderungen (Angular)

Authentifizieren von Anforderungen für APIs, die gemeinsam mit der app gehostet erfolgt automatisch durch die Verwendung der HTTP-Client-Interceptor, die von der app definiert.

## <a name="protect-a-client-side-route-react"></a>Schützen Sie eine Route für die clientseitige (React)

Schützen Sie eine Client-Side-Route mithilfe der `AuthorizeRoute` anstelle der einfachen Komponente `Route` Komponente. Sehen Sie sich beispielsweise wie die `fetch-data` Route konfiguriert ist, in der `App` Komponente:

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

Schützen eine Route:

* Schützt nicht den tatsächlichen Endpunkt (erfordert immer noch eine `[Authorize]` angewendet werden).
* Nur verhindert, dass den Benutzer navigieren zu der angegebenen Client-Side-Route, wenn sie nicht authentifiziert ist.

## <a name="authenticate-api-requests-react"></a>Authentifizieren der API-Anforderungen (React)

Authentifizieren von Anforderungen mit React erfolgt durch das erste Importieren der `authService` -Instanz aus der `AuthorizeService`. Das Zugriffstoken wird abgerufen, von der `authService` und auf die Anforderung angefügt ist, wie unten dargestellt. React-Komponenten, diese Arbeit erfolgt in der Regel der `componentDidMount` Lebenszyklusmethode oder als Ergebnis eine Benutzerinteraktion.

### <a name="import-the-authservice-into-your-component"></a>Importieren Sie die AuthService in Ihre Komponente

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a>Abzurufen Sie, und fügen Sie das Zugriffstoken an die Antwort

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

## <a name="deploy-to-production"></a>Für die Produktion bereitstellen

Um die app für die Produktion bereitstellen zu können, müssen die folgenden Ressourcen bereitgestellt werden:

* Eine Datenbank zum Speichern der Identity-Benutzerkonten und die Identity Server gewährt.
* Ein Production-Zertifikat zum Signieren von Token verwendet werden soll.
  * Es gibt keine speziellen Anforderungen für dieses Zertifikat ein. Sie können ein selbstsigniertes Zertifikat oder ein Zertifikat mit einer Stammzertifizierungsstelle bereitgestellt werden.
  * Sie können mithilfe von standard-Tools wie PowerShell oder OpenSSL generiert werden.
  * Es kann in den Zertifikatspeicher auf den Zielcomputern installiert wird, oder als eine *PFX* Datei mit einem sicheren Kennwort.

### <a name="example-deploy-to-azure-websites"></a>Beispiel: Bereitstellen auf Azure Websites

Dieser Abschnitt beschreibt das Bereitstellen der app auf Azure-Websites mithilfe eines Zertifikats im Zertifikatspeicher gespeichert. Um die app zum Laden eines Zertifikats aus dem Zertifikatspeicher zu ändern, muss die App Service-Plan auf mindestens den Tarif "Standard" sein, wenn Sie in einem späteren Schritt konfigurieren. In der app *"appSettings.JSON"* Datei, ändern Sie die `IdentityServer` Abschnitt aus, um die wichtigsten Details enthalten:

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

* Die Name-Eigenschaft für Zertifikat entspricht, mit dem distinguished Antragsteller des Zertifikats.
* Der Speicherort darstellt, wo Sie das Zertifikat zu laden (`CurrentUser` oder `LocalMachine`).
* Der Speichername stellt es sich um den Namen des Zertifikatspeichers an, in dem das Zertifikat gespeichert wird. In diesem Fall zeigt es in den persönlichen Speicher.

Um auf Azure Websites bereitstellen, stellen Sie die app, die die Schritte in [Bereitstellen der app in Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) erforderlichen Azure-Ressourcen erstellen und Bereitstellen der app für die Produktion.

Nach dem Ausführen der zuvor beschriebenen Anweisungen, die app in Azure bereitgestellt wird, aber noch nicht funktionsfähig. Das von der app verwendete Zertifikat muss dennoch eingerichtet werden. Suchen Sie den Fingerabdruck des Zertifikats verwendet werden, und führen Sie die Schritte [laden Ihrer Zertifikate](/azure/app-service/app-service-web-ssl-cert-load#load-your-certificates).

Während Sie diese Schritte ganz zu schweigen SSL, gibt es eine **Zertifikate mit privaten** Abschnitt im Portal, in dem Sie das bereitgestellte Zertifikat zur Verwendung mit der app hochladen können.

Nach diesem Schritt wird die app neu starten und funktionsfähig sein.

## <a name="other-configuration-options"></a>Andere Konfigurationsoptionen

Die Unterstützung für die Autorisierung für API-builds über Identity Server mit einem Satz von Konventionen, Standardwerte und Verbesserungen an die Oberfläche für SPAs zu vereinfachen. Natürlich ist das volle Potenzial von Identity Server hinter den Kulissen verfügbar, wenn die ASP.NET Core-Integrationen Ihr Szenario nicht abdecken. Die Unterstützung von ASP.NET Core konzentriert sich auf "Erstanbieter"-apps, in der alle apps erstellt und von der Organisation bereitgestellt werden. Support ist nicht als solche, für Aspekte wie die Zustimmung oder in einem Verbund angeboten. Verwenden Sie für diese Szenarien Identity Server, und befolgen Sie die Dokumentation.

### <a name="application-profiles"></a>Anwendungsprofile

Anwendungsprofile werden vordefinierte Konfigurationen für apps, die ihre Parameter weiter definieren. Zu diesem Zeitpunkt werden die folgenden Profile unterstützt:

* `IdentityServerSPA`: Stellt eine SPA gehostet werden, zusammen mit Identity Server als einzelne Einheit dar.
  * Die `redirect_uri` standardmäßig `/authentication/login-callback`.
  * Die `post_logout_redirect_uri` standardmäßig `/authentication/logout-callback`.
  * Der Satz von Bereichen umfasst die `openid`, `profile`, und alle Bereiche, die für die APIs in der app definiert.
  * Der Satz von zulässigen OIDC-Antworttypen ist `id_token token` oder jeweils einzeln (`id_token`, `token`).
  * Die zulässigen Antwortmodus ist `fragment`.
* `SPA`: Stellt eine SPA, die gehostet wird, ist nicht mit Identity Server dar.
  * Der Satz von Bereichen umfasst die `openid`, `profile`, und alle Bereiche, die für die APIs in der app definiert.
  * Der Satz von zulässigen OIDC-Antworttypen ist `id_token token` oder jeweils einzeln (`id_token`, `token`).
  * Die zulässigen Antwortmodus ist `fragment`.
* `IdentityServerJwt`: Stellt eine API, die zusammen mit gehostet wird, wird mit Identity Server dar.
  * Die app ist für einen Bereich verfügen, der standardmäßig auf den Namen der app konfiguriert.
* `API`: Stellt eine API, die gehostet wird, ist nicht mit Identity Server dar.
  * Die app ist für einen Bereich verfügen, der standardmäßig auf den Namen der app konfiguriert.

### <a name="configuration-through-appsettings"></a>Die Konfiguration über "appSettings"

Konfigurieren Sie die apps über das Konfigurationssystem, indem Sie sie in der Werteliste hinzufügen `Clients` oder `Resources`.

Konfigurieren des Clients `redirect_uri` und `post_logout_redirect_uri` -Eigenschaft, wie im folgenden Beispiel gezeigt:

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

Wenn Sie Ressourcen zu konfigurieren, können Sie die Bereiche für die Ressource konfigurieren, wie unten dargestellt:

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

### <a name="configuration-through-code"></a>Die Konfiguration über code

Sie können auch konfigurieren, die Clients und Ressourcen mithilfe von Code mithilfe einer Überladung der `AddApiAuthorization` , die immer dann eine Aktion aus, um Optionen zu konfigurieren.

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

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:spa/angular>
* <xref:spa/react>

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
# <a name="authentication-and-authorization-for-spas"></a>Authentifizierung und Autorisierung für SPAs

Unterstützung für die Authentifizierung in einseitigen Anwendungen verwenden unsere neue Unterstützung für die Autorisierung für API-werden in ASP.NET 3.0 eingeführt. Diese Unterstützung basiert auf einer Kombination von ASP.NET Core-Identität für die Authentifizierung und das Speichern von Benutzern und Identity Server für die Implementierung des Open ID Connect.

Wir haben einen neuen Authentifizierungsparameter hinzugefügt, um unsere Angular und React-Vorlagen, die die Authentifizierungsparameter in unserer Mvc und Razor-Seiten-Vorlagen mit ähnelt zulässige Werte "None" und "Einzeln".

## <a name="create-an-angular-app-with-api-authorization-support"></a>Erstellen Sie eine Angular-app mit Unterstützung für die Autorisierung-API

Um eine neue Angular-app mit Unterstützung für die Authentifizierung und Autorisierung von Benutzern zu erstellen, öffnen Sie eine Befehlsshell, und führen Sie den folgenden Befehl aus:

```console
dotnet new angular -o <output_directory_name> -au Individual
```

Der vorherige Befehl erstellt eine ASP.NET Core-app mit einem *ClientApp* Verzeichnis mit der Angular-app.

## <a name="create-a-react-app-with-api-authorization-support"></a>Erstellen Sie eine React-app mit Unterstützung für die Autorisierung-API

Um eine neue React-app mit Unterstützung für die Authentifizierung und Autorisierung von Benutzern zu erstellen, öffnen Sie eine Befehlsshell, und führen Sie den folgenden Befehl aus:

```console
dotnet new react -o <output_directory_name> -au Individual
```

Der vorherige Befehl erstellt eine ASP.NET Core-app mit einem *ClientApp* Verzeichnis mit der React-app.

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a>Allgemeine Beschreibung von ASP.NET Core-Komponenten der app

Es gibt mehrere Erweiterungen für das Projekt auf, wenn wir Unterstützung für die Authentifizierung schließen:

### <a name="startup-class"></a>Startup-Klasse

Wenn wir uns den Code in der Startup-Klasse, die unten ansehen können die folgenden Einschlüsse Danke, dass:
* In `public void ConfigureServices(IServiceCollection services)`:
  * Die Identität mit der standardmäßigen UI.
    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
      options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
      .AddDefaultUI(UIFramework.Bootstrap4)
      .AddEntityFrameworkStores<ApplicationDbContext>();
    ```
  * Identitätsserver eine zusätzliche AddApiAuthorization-Hilfsmethode dieses Setups einige Konventionen von ASP.NET über Identity Server Standard.
    ```csharp
    services.AddIdentityServer()
      .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```
  * Authentifizierung mit einer zusätzlichen AddIdentityServerJwt-Hilfsmethode, die die Anwendung zum Überprüfen von Jwt-Tokens erstellt, die von Identity Server konfiguriert. 
    ```csharp
    services.AddAuthentication()
      .AddIdentityServerJwt();
    ```
* In `public void Configure(IApplicationBuilder app)`:
  * Die authentifizierungsmiddleware, die für die Anmeldeinformationen in der eingehenden Anforderung überprüft und Festlegen des Benutzers im Kontext der Anforderung verantwortlich ist.
    ```csharp
    app.UseAuthentication();
    ```
  * Die Identity Server-Middleware, die die Open ID Connect-Endpunkte verfügbar macht.
    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization 
Diese Hilfsmethode konfiguriert die Identity-Server, um unsere unterstützte Konfiguration zu verwenden. Identitätsserver ist ein sehr leistungsfähigen und erweiterbaren Framework für die Behandlung der Anwendung die Sicherheitsrisiken, aber zur gleichen Zeit, die einen Großteil der Komplexität verfügbar macht, die wir nicht für die häufigsten Szenarien kennen müssen, also wählen wir einen Satz von Konventionen und Konfigurationsoptionen für Sie, die wir zu berücksichtigen sind ein guter Ausgangspunkt. Nach Bedarf für die Authentifizierung ist das volle Potenzial von Identity Server weiterhin zur Verfügung, damit Sie sie entsprechend Ihren Anforderungen anpassen können.

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt
Diese Hilfsmethode wird ein Schema der Richtlinie für die Anwendung als der Standardhandler für die Authentifizierung konfiguriert. Die Richtlinie ist so konfiguriert, dass die Identität, die alle Anforderungen zu verarbeiten, die zu jedem untergeordneten Pfad im Bereich der Url des Identitätsanbieters führen können "/ Identity" und die JwtBearerHandler alle anderen Anforderungen verarbeiten zu können.
Addionally, die diese Methode registriert eine `<<ApplicationName>>API` -API-Ressource mit identitätsserver mit einem Standardbereich des `<<ApplicationName>>API` und konfiguriert die JWT-Trägertoken-Middleware zum Überprüfen von Identity Server für die Anwendung ausgestellten Tokens.

### <a name="sampledatacontroller"></a>SampleDataController
Wenn Sie die Datei Controllers\SampleDataController.cs betrachten können wir verfolgen die `[Authorize]` -Attribut nicht angewendet werden, auf die Klasse, der angibt, dass der Benutzer autorisiert werden muss, basierend auf die Standardrichtlinie auf die Ressource zuzugreifen. Die Standardrichtlinie für die Autorisierung konfiguriert werden, um das Standardschema für die Authentifizierung verwenden, die vom eingerichtet ist, geschieht `AddIdentityServerJwt` mit dem Schema der Richtlinie, die wir weiter oben erwähnt, wodurch die JwtBearer-Handler konfiguriert, indem diese Hilfsmethode den Standardhandler für Anforderungen an die app.

### <a name="applicationdbcontext"></a>ApplicationDbContext
Wenn Sie die Datei im Data\ApplicationDbContext.cs betrachten sehen die gleichen "DbContext" wir in die Identität mit der Ausnahme verwenden, dass es sich um ApiAuthorizationDbContext (einen stärker abgeleiteten Klasse von IdentityDbContext) erweitert das Schema für Identity Server enthält.
Wenn Vollzugriff auf das Datenbankschema sollten wir können einfach eine der verfügbaren Identity "DbContext"-Klassen erben und konfigurieren Sie den Kontext, um das identitätsschema durch Aufrufen von aufzunehmen `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` auf die `OnModelCreating` Methode.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController
Wenn sehen wir uns an die Datei Controllers\OidcConfigurationController.cs, wie Sie sehen den Endpunkt, dass wir einsatzbesprechungen die OIDC-Parameter bereitstellen, die der Client verwenden muss.

### <a name="appsettingsjson"></a>appsettings.json
Wenn Sie die Datei "appsettings.json" im Stammverzeichnis des Projekts betrachten, wie Sie sehen einen neuen `IdentityServer` Abschnitt mit die Liste der Beschreibungen konfigurierten Clients und wir können sehen, dass es ein einzelnen Client. Der Name des Clients entspricht dem Namen der Anwendung und gemäß der Konvention an den oAuth-Client-ID-Parameter zugeordnet ist. Das Profil gibt an, welche Art von Anwendung, die wir konfigurieren, und wir es intern Laufwerk-Konventionen, die den Konfigurationsvorgang für den Server zu vereinfachen. Es gibt mehrere Profile verfügbar im folgenden Abschnitt erläutert.

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
Wenn Sie den "appSettings" betrachten. Development.JSON-Datei im Stammverzeichnis des Projekts, wie Sie sehen einen neuen `IdentityServer` Abschnitt, in dem den Schlüssel wird beschrieben, wir zum Signieren von Token verwenden. Wenn Sie in der produktionsumgebung bereitstellen muss ein Schlüssel bereitgestellt werden, zusammen mit der Anwendung bereitgestellt wird, wie nachstehend beschrieben.

```json
  "IdentityServer": {
    "Key": {
      "Type": "Development"
    }
  }
}
```

## <a name="general-description-of-the-angular-application"></a>Allgemeine Beschreibung der Angular-Anwendung
Die Unterstützung für die Authentifizierung und Autorisierung der API in der Angular-Vorlage befindet sich in eine eigene Angular-Modul. Unter ClientApp\src\api-Autorisierung, und es besteht aus den folgenden Elementen:
* 3-Komponenten:
  * Login-Komponente: Behandelt die Anmeldung für die app an.
  * Logout-Komponente: Verarbeitet die Abmeldung Flow für die app an.
  * Login-Menü-Komponente: Ein Widget, das zeigt den aktuellen authentifizierten Benutzers mit Links zum Benutzerprofil zu verwalten und Abmelden oder links zum Anmelden oder registrieren, wenn der Benutzer nicht authentifiziert ist.
* Eine Route Guard `AuthorizeGuard` , die hinzugefügt werden können, um die sprachliche und erfordert, dass einen Benutzer authentifiziert werden, bevor der Zugriff auf die Route.
* Ein http-Interceptor `AuthorizeInterceptor` , die das Zugriffstoken auf ausgehenden HTTP-Anforderungen, die für die API aus, wenn der Benutzer authentifiziert ist angefügt.
* Ein Dienst `AuthorizeService` , behandelt die Details auf niedriger Ebene des Authentifizierungsprozesses und Informationen zu den authentifizierten Benutzer auf den Rest der Anwendung für die Nutzung verfügbar gemacht.
* Ein angular-Modul, die Routen, die die Authentifizierung Teile der Anwendung definiert und verfügbar macht, die Anmeldung im Menü-Komponente, den Interceptor, den Wächter und der Dienst für die Nutzung vom Rest der Anwendung.

## <a name="general-description-of-the-react-application"></a>Allgemeine Beschreibung der React-Anwendung
Die Unterstützung für die Authentifizierung und Autorisierung der API in der React-Vorlage befindet sich unter ClientApp\src\components\api-Authorization\ und besteht aus den folgenden Elementen:
* 4-Komponenten:
  * Login-Komponente: Behandelt die Anmeldung für die app an.
  * Logout-Komponente: Verarbeitet die Abmeldung Flow für die app an.
  * Login-Menü-Komponente: Ein Widget, das zeigt den aktuellen authentifizierten Benutzers mit Links zum Benutzerprofil zu verwalten und Abmelden oder links zum Anmelden oder registrieren, wenn der Benutzer nicht authentifiziert ist.
  * AuthorizeRoute: Eine Route-Komponente, die ein Benutzer authentifiziert werden, vor dem Rendern der Komponente benötigt, die in der Component-Parameter angegeben werden.
  * Eine exportierte `authService` Instanz der Klasse `AuthorizeService` , behandelt die Details auf niedriger Ebene des Authentifizierungsprozesses und Informationen zu den authentifizierten Benutzer auf den Rest der Anwendung für die Nutzung verfügbar gemacht.

Nun, da wir die Hauptkomponenten der Lösung gesehen haben, können wir ein bestimmtes Erscheinungsbild auf einzelne Szenarien für die Anwendung ausführen:

## <a name="requiring-authorization-on-a-new-api"></a>Eine Autorisierung auf eine neue API erfordert
Das System ist aus, die es einfach, eine Autorisierung erforderlich ist, für die neuen APIs machen konfiguriert. Zu diesem Zweck erstellen Sie einen neuen Controller, und fügen die `[Authorize]` -Attribut zur Controllerklasse oder auf eine beliebige Aktion im Controller.

## <a name="protecting-a-client-side-route-angular"></a>Schützen eine Route für die clientseitige (Angular)
Schützen eine Client-Side-Route erfolgt durch Hinzufügen der Authorize-Guard zur Liste der Wächter ausgeführt wird, wenn Sie eine Route zu konfigurieren. Als Beispiel sehen Sie, wie die Route Abrufen der Daten in der Haupt-app-angular-Modul konfiguriert ist:

```ts
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

Es ist wichtig zu erwähnen, dass eine Route schützen nicht den tatsächlichen Endpunkt schützen (erfordert immer noch eine `[Authorize]` angewendet werden), aber es nur verhindert, dass den Benutzer auf die Route der angegebenen Client-Seite navigieren, wenn sie nicht authentifiziert ist.

## <a name="authenticate-api-requests-angular"></a>Authentifizieren der API-Anforderungen (Angular)

Authentifizieren von Anforderungen, für APIs, die auf Seite gehostet wird, dass die Anwendung erfolgt automatisch durch die Verwendung der HTTP-Client-Interceptor, die von der Anwendung definiert.

## <a name="protect-a-client-side-route-react"></a>Schützen Sie eine Route für die clientseitige (React)

Schützen eine Client-Side-Route erfolgt mithilfe der Komponente AuthorizeRoute anstelle der einfachen Route-Komponente. Als Beispiel sehen Sie, wie die Route Abrufen der Daten innerhalb der App-Komponente konfiguriert ist:

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

Es ist wichtig zu erwähnen, dass eine Route schützen nicht den tatsächlichen Endpunkt schützen (erfordert immer noch eine `[Authorize]` angewendet werden), aber es nur verhindert, dass den Benutzer auf die Route der angegebenen Client-Seite navigieren, wenn sie nicht authentifiziert ist.

## <a name="authenticate-api-requests-react"></a>Authentifizieren der API-Anforderungen (React)

Authentifizieren von Anforderungen mit React erfolgt durch das erste Importieren der `authService` -Instanz aus der `AuthorizeService` zum Abrufen des Zugriffstokens aus den AuthService und Anfügen an die Anforderung wie unten dargestellt. In React-Komponenten. Dies erfolgt in der Regel in der ComponentDidMount Lebenszyklus-Methode oder als Ergebnis von Benutzerinteraktion.

### <a name="import-the-authservice-into-your-component"></a>Importieren Sie die AuthService in Ihre Komponente

```js
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a>Abzurufen Sie, und fügen Sie das Zugriffstoken an die Antwort

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

## <a name="deploy-into-production"></a>In der Produktion bereitstellen

Um die Anwendung in die Produktion bereitstellen müssen wir mehrere Ressourcen bereitstellen:
* Eine Datenbank zum Speichern der Identity-Benutzerkonten und die identitätsserver gewährt.
* Ein Production-Zertifikat zum Signieren von Token verwendet werden soll.
  * Es gibt keine speziellen Anforderungen für dieses Zertifikat ein. Sie können ein selbstsigniertes Zertifikat oder ein Zertifikat mit einer Stammzertifizierungsstelle bereitgestellt werden.
  * Sie können mithilfe von standard-Tools wie Powershell oder Openssl generiert werden.
  * Sie können in den Zertifikatspeicher auf den Zielcomputern installiert wird, oder als PFX-Datei mit einem sicheren Kennwort bereitgestellt werden.

### <a name="example-deploy-into-azure-websites"></a>Beispiel: Bereitstellen in Azure-Websites

In diesem Abschnitt werden wir zum Bereitstellen der Anwendung auf Azure-Websites mithilfe eines Zertifikats im Zertifikatspeicher gespeichert. Wir müssen so ändern Sie die Anwendung aus dem Zertifikatspeicher ein Zertifikat zu laden. Zu diesem Zweck muss unsere app Service-Plan mindestens auf Tarif "standard" sein, wenn wir in einem späteren Schritt konfigurieren. In unserer Anwendung müssen wir einfach so ändern Sie im Abschnitt "Identity Server" auf "appSettings.JSON", um die wichtigsten Details enthalten:
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
* Die Name-Eigenschaft für Zertifikat entspricht, mit dem distinguished Antragsteller des Zertifikats.
* Der Speicherort darstellt, wo das Zertifikat aus ("CurrentUrser" oder "LocalMachine") geladen.
* Der Speichername stellt es sich um den Namen des Zertifikatspeichers an, in dem das Zertifikat gespeichert wird, in diesem Fall, die sie in den persönlichen Speicher verweist.

Um auf Azure Websites bereitstellen, stellen Sie die app, die die Schritte in [Bereitstellen der app in Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) erforderlichen Azure-Ressourcen erstellen und Bereitstellen der app für die Produktion.

Nachdem Sie auf diese Weise wird die Anwendung in Azure bereitgestellt wird, aber Sie ist noch nicht vollständig funktionsfähig, da wir benötigen Sie das Zertifikat von der Anwendung einrichten. Zu diesem Zweck müssen wir den Fingerabdruck des Zertifikats haben, werden wir verwenden, und führen die Schritte [laden Ihrer Zertifikate](/azure/app-service/app-service-web-ssl-cert-load#load-your-certificates).

Während Schritten SSL erwähnt, besteht ein Abschnitt "Private Zertifikate" im Portal, in dem wir unsere bereitgestellte Zertifikat für die Verwendung mit Ihrer app hochladen können.

Nach diesem Schritt wir sollten in der Lage, die Anwendung neu zu starten, und wird vollständig funktionsfähig.

## <a name="other-configuration-options"></a>Andere Konfigurationsoptionen
Unsere Unterstützung für die Autorisierung für API-builds über Identity Server mit einem Satz von Konventionen, Standardwerte und Verbesserungen an die Oberfläche für Single Page Applications zu vereinfachen. Natürlich ist das volle Potenzial von Identity Server verfügbar ist, hinter den Kulissen, wenn die Integrationen, die wir anbieten Ihr Szenario nicht abdecken. Unser Support ist auf "Erstanbieter"-Anwendungen, sogenannte, in denen alle Anwendungen erstellt und bereitgestellt, die von der Organisation ausgerichtet. Daher bieten wir keine Unterstützung für Aufgaben wie die Zustimmung oder in einem Verbund. Für diese Szenarien ist unsere Empfehlung zum Verwenden von Identity Server ein, und führen ihre Dokumentation.

### <a name="application-profiles"></a>Anwendungsprofile
Anwendungsprofile werden vordefinierte Konfigurationen für Anwendungen, die ihre Parameter weiter definieren. Zurzeit unterstützen wir zwei Profile:
* IdentityServerSPA: Stellt eine einseitige Anwendung gehostet wird, zusammen mit der Identity-Server als einzelne Einheit dar.
  * Der umleitungs-URI ist standardmäßig `/authentication/login-callback`.
  * Standardmäßig den Post_logout_redirect_uri `/authentication/logout-callback`.
  * Der Satz von Bereichen umfasst die `openid`, `profile`, und alle Bereiche, die für die APIs in der Anwendung definiert.
  * Der Satz von zulässigen OIDC-Antworttypen ist `id_token token` oder jeweils einzeln (`id_token`, `token`).
  * Die zulässigen Antwortmodus ist `fragment`.
* SPA: Stellt eine einseitige Anwendung, die nicht mit Identity Server gehostet wird.
  * Der Satz von Bereichen umfasst die `openid`, `profile`, und alle Bereiche, die für die APIs in der Anwendung definiert.
  * Der Satz von zulässigen OIDC-Antworttypen ist `id_token token` oder jeweils einzeln (`id_token`, `token`).
  * Die zulässigen Antwortmodus ist `fragment`.
* IdentityServerJwt: Stellt eine API, die zusammen mit gehostet wird, wird mit Identity Server dar.
  * Die Anwendung ist konfiguriert, um einen Bereich verfügen, der standardmäßig auf den Namen der Anwendung.
* API: Stellt eine API, die nicht gehostet wird, wird mit Identity Server dar.
  * Die Anwendung ist konfiguriert, um einen Bereich verfügen, der standardmäßig auf den Namen der Anwendung.

### <a name="configuration-through-appsettings"></a>Die Konfiguration über "appSettings"
Wir können die Anwendungen über unser Konfigurationssystem bzw. der Liste der Clients oder Ressourcen hinzufügen konfigurieren. 

Konfiguration von Clients konfigurieren wir die `redirect_uri` und `post_logout_redirect_uri` wie unten dargestellt:
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

Beim Konfigurieren der Ressourcen können wir die Bereiche für die Ressource konfigurieren, wie unten dargestellt:
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

### <a name="configuration-through-code"></a>Die Konfiguration über code
Wir können auch konfigurieren, die Clients und Ressourcen mithilfe von Code mithilfe einer Überladung der AddApiAuthorization, die eine Aktion, um Optionen zu konfigurieren.
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

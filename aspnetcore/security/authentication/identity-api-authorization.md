---
title: Einführung in die Authentifizierung für Single-Page-apps auf ASP.net Core
author: javiercn
description: Verwenden Sie die Identität mit einer einseitigen APP, die in einer ASP.net Core-App gehostet wird.
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 11/08/2019
uid: security/authentication/identity/spa
ms.openlocfilehash: 623f739b17c0bed3ce929f562c9581ab26ecf5bc
ms.sourcegitcommit: 0b0e485a8a6dfcc65a7a58b365622b3839f4d624
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/01/2020
ms.locfileid: "76928560"
---
# <a name="authentication-and-authorization-for-spas"></a>Authentifizierung und Autorisierung für Spas

ASP.net Core 3,0 oder höher bietet die Authentifizierung in Single-Page-Apps (Spas) mithilfe der Unterstützung für die API-Autorisierung. ASP.net Core Identität für das Authentifizieren und Speichern von Benutzern wird mit [identityserver](https://identityserver.io/) für die Implementierung von Open ID Connect kombiniert.

Der **Angular** -und der-Projektvorlagen, die dem-Authentifizierungs Parameter in **den Projektvorlagen** **Webanwendung (Model-View-Controller)** (MVC) und **Webanwendung** (Razor Pages) ähneln, wurde ein Authentifizierungs Parameter hinzugefügt. Die zulässigen Parameterwerte sind **None** und **Individual**. Die **Projektvorlage** "" "" "" "" "" "" "" "" "" "" ""

## <a name="create-an-app-with-api-authorization-support"></a>Erstellen einer APP mit Unterstützung für die API-Autorisierung

Benutzerauthentifizierung und-Autorisierung können mit Angular-und reagingspas verwendet werden. Öffnen Sie eine Befehlsshell, und führen Sie den folgenden Befehl:

**Angular**:

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

**Reagieren**:

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

Der vorherige Befehl erstellt eine ASP.net Core-App mit einem *ClientApp* -Verzeichnis, das die Spa enthält.

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a>Allgemeine Beschreibung der ASP.net Core Komponenten der APP

In den folgenden Abschnitten werden Ergänzungen zum-Projekt beschrieben, wenn die Authentifizierungs Unterstützung enthalten ist:

### <a name="startup-class"></a>Startup-Klasse

Die `Startup`-Klasse verfügt über folgende Ergänzungen:

* In der `Startup.ConfigureServices`-Methode:
  * Identität mit der Standardbenutzer Oberfläche:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * Identityserver mit einer zusätzlichen `AddApiAuthorization`-Hilfsmethode, mit der einige standardmäßige ASP.net Core Konventionen auf identityserver eingerichtet werden:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Authentifizierung mit einer zusätzlichen `AddIdentityServerJwt`-Hilfsmethode, die die APP so konfiguriert, dass die von identityserver erstellten JWT-Token überprüft werden:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* In der `Startup.Configure`-Methode:
  * Die Authentifizierungs Middleware, die für die Validierung der Anforderungs Anmelde Informationen und das Festlegen des Benutzers im Anforderungs Kontext zuständig ist:

    ```csharp
    app.UseAuthentication();
    ```

  * Die identityserver-Middleware, die die Open ID Connect-Endpunkte verfügbar macht:

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a>Addapiauthorization

Diese Hilfsmethode konfiguriert identityserver für die Verwendung der unterstützten Konfiguration. Identityserver ist ein leistungsfähiges und erweiterbares Framework für die Behandlung von App-Sicherheitsbedenken. Gleichzeitig stellt dies unnötige Komplexität für die gängigsten Szenarien bereit. Folglich werden Ihnen eine Reihe von Konventionen und Konfigurationsoptionen zur Verfügung gestellt, die als guter Ausgangspunkt angesehen werden. Nachdem sich Ihre Authentifizierung geändert hat, ist die volle Leistungsfähigkeit von identityserver weiterhin verfügbar, um die Authentifizierung an Ihre Bedürfnisse anzupassen.

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

Diese Hilfsmethode konfiguriert ein Richtlinien Schema für die APP als Standard Authentifizierungs Handler. Die Richtlinie ist so konfiguriert, dass die Identität alle Anforderungen verarbeitet, die an einen untergeordneten Pfad im Identitäts-URL-Bereich "/Identity" weitergeleitet werden. Die `JwtBearerHandler` behandelt alle anderen Anforderungen. Darüber hinaus registriert diese Methode eine `<<ApplicationName>>API` API-Ressource bei identityserver mit einem Standardbereich `<<ApplicationName>>API` und konfiguriert die JWT-bearertoken-Middleware, um von identityserver für die APP ausgegebene Token zu überprüfen.

### <a name="weatherforecastcontroller"></a>Weatherforecastcontroller

Beachten Sie in der Datei " *controllers\weatherforecastcontroller.cs* " das `[Authorize]` Attribut, das auf die Klasse angewendet wird, die angibt, dass der Benutzer auf der Basis der Standard Richtlinie für den Zugriff auf die Ressource autorisiert werden muss. Die Standard Autorisierungs Richtlinie wird so konfiguriert, dass das Standard Authentifizierungsschema verwendet wird, das durch `AddIdentityServerJwt` auf das oben erwähnte Richtlinien Schema festgelegt wird. Dadurch wird der von dieser Hilfsmethode konfigurierte `JwtBearerHandler` der Standard Handler für Anforderungen an die app.

### <a name="applicationdbcontext"></a>ApplicationDbContext

Beachten Sie in der Datei *data\applicationdbcontext.cs* , dass die gleiche `DbContext` in der Identität verwendet wird, mit der Ausnahme, dass Sie `ApiAuthorizationDbContext` (eine stärker abgeleitete Klasse von `IdentityDbContext`) erweitert, um das Schema für identityserver einzuschließen.

Um die vollständige Kontrolle über das Datenbankschema zu erhalten, erben Sie von einer der verfügbaren Identitäts `DbContext` Klassen, und konfigurieren Sie den Kontext so, dass er das Identitäts Schema einschließt, indem Sie `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` für die `OnModelCreating` Methode aufrufen

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

Beachten Sie in der Datei " *controllers\oidcconfigurationcontroller.cs* " den Endpunkt, der bereitgestellt wird, um die oidc-Parameter bereitzustellen, die vom Client verwendet werden müssen.

### <a name="appsettingsjson"></a>appsettings.json

In der Datei *appSettings. JSON* des Projekt Stamms befindet sich ein neuer `IdentityServer` Abschnitt, in dem die Liste der konfigurierten Clients beschrieben wird. Im folgenden Beispiel gibt es einen einzelnen Client. Der Client Name entspricht dem APP-Namen und wird dem OAuth-`ClientId` Parameter gemäß der Konvention zugeordnet. Das Profil gibt den App-Typ an, der konfiguriert wird. Sie wird intern verwendet, um Konventionen zu fördern, die den Konfigurationsprozess für den Server vereinfachen. Es stehen mehrere Profile zur Verfügung, wie im Abschnitt [Anwendungsprofile](#application-profiles) erläutert.

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

In *appSettings. Development. JSON* -Datei des Projekt Stamms. es gibt einen `IdentityServer` Abschnitt, in dem der Schlüssel zum Signieren von Token beschrieben wird. Beim Bereitstellen in der Produktion muss neben der APP ein Schlüssel bereitgestellt und bereitgestellt werden, wie im Abschnitt bereitstellen [in der Produktion](#deploy-to-production) erläutert.

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a>Allgemeine Beschreibung der Angular-App

Die Unterstützung von Authentifizierung und API-Autorisierung in der Angular-Vorlage befindet sich in einem eigenen Angular-Modul im Verzeichnis *clientapp\src\api-Authorization* . Das Modul besteht aus den folgenden Elementen:

* 3 Komponenten:
  * *Login. Component. TS*: verarbeitet den Anmelde Fluss der app.
  * *Abmelde. Component. TS*: verarbeitet den Abmelde Fluss der app.
  * *Login-Menu. Component. TS*: ein Widget, das eine der folgenden Gruppen von Links anzeigt:
    * Benutzerprofil Verwaltung und Abmelde Links, wenn der Benutzer authentifiziert ist.
    * Links zur Registrierung und Anmeldung, wenn der Benutzer nicht authentifiziert ist.
* Ein Weiterleitungs Wächter `AuthorizeGuard`, das Routen hinzugefügt werden kann und erfordert, dass ein Benutzer vor dem Besuch der Route authentifiziert wird.
* Ein HTTP-Interceptor `AuthorizeInterceptor`, der das Zugriffs Token an ausgehende HTTP-Anforderungen anbindet, die auf die API abzielen, wenn der Benutzer authentifiziert wird.
* Ein Dienst `AuthorizeService`, der die Details des Authentifizierungsprozesses auf niedrigerer Ebene behandelt und Informationen über den authentifizierten Benutzer für den Rest der App zur Nutzung verfügbar macht.
* Ein Angular-Modul, das Routen definiert, die den Authentifizierungs Teilen der APP zugeordnet sind. Er macht die Anmelde Menü Komponente, den Interceptor, den Wächter und den Dienst für die Nutzung durch den Rest der app verfügbar.

## <a name="general-description-of-the-react-app"></a>Allgemeine Beschreibung der App "reagieren"

Die Unterstützung für die Authentifizierung und API-Autorisierung in der "reagieren"-Vorlage befindet sich im Verzeichnis " *clientapp\src\components\api-Authorization* ". Sie besteht aus den folgenden Elementen:

* 4 Komponenten:
  * *Login. js*: verarbeitet den Anmelde Fluss der app.
  * *Abmelde. js*: verarbeitet den Abmelde Fluss der app.
  * *Loginmenu. js*: ein Widget, das eine der folgenden Gruppen von Links anzeigt:
    * Benutzerprofil Verwaltung und Abmelde Links, wenn der Benutzer authentifiziert ist.
    * Links zur Registrierung und Anmeldung, wenn der Benutzer nicht authentifiziert ist.
  * *Autorizeroute. js*: eine Routen Komponente, für die ein Benutzer vor dem Rendern der im `Component`-Parameter festgelegten Komponente authentifiziert werden muss.
* Eine exportierte `authService` Instanz der-Klasse `AuthorizeService`, die die Details des Authentifizierungsprozesses auf niedrigerer Ebene behandelt und Informationen über den authentifizierten Benutzer für den Rest der App zur Nutzung verfügbar macht.

Nachdem Sie die Hauptkomponenten der Lösung kennengelernt haben, können Sie sich einen tieferen Einblick in die einzelnen Szenarios für die APP machen.

## <a name="require-authorization-on-a-new-api"></a>Autorisierung für eine neue API erforderlich

Standardmäßig ist das System so konfiguriert, dass die Autorisierung für neue APIs problemlos erforderlich ist. Erstellen Sie hierzu einen neuen Controller, und fügen Sie das `[Authorize]`-Attribut zur Controller Klasse oder zu einer beliebigen Aktion im Controller hinzu.

## <a name="customize-the-api-authentication-handler"></a>Anpassen des API-Authentifizierungs Handlers

Konfigurieren Sie zum Anpassen der Konfiguration des JWT-Handlers der API die <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> Instanz:

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();

services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        ...
    });
```

Der JWT-Handler der API löst Ereignisse aus, die die Steuerung des Authentifizierungsprozesses mithilfe von `JwtBearerEvents`ermöglichen. Um Unterstützung für die API-Autorisierung zu bieten, registriert `AddIdentityServerJwt` eigene Ereignishandler.

Um die Behandlung eines Ereignisses anzupassen, binden Sie den vorhandenen Ereignishandler nach Bedarf mit zusätzlicher Logik ein. Beispiel:

```csharp
services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        var onTokenValidated = options.Events.OnTokenValidated;       
        
        options.Events.OnTokenValidated = async context =>
        {
            await onTokenValidated(context);
            ...
        }
    });
```

Im vorangehenden Code wird der `OnTokenValidated` Ereignishandler durch eine benutzerdefinierte Implementierung ersetzt. Diese Implementierung:

1. Ruft die ursprüngliche, von der API-Autorisierungs Unterstützung bereitgestellte Implementierung auf.
1. Führen Sie eine eigene benutzerdefinierte Logik aus.

## <a name="protect-a-client-side-route-angular"></a>Schützen einer Client seitigen Route (Angular)

Der Schutz einer Client seitigen Route erfolgt durch Hinzufügen von Autorisierungs Wächter zur Liste der Wächter, die beim Konfigurieren einer Route ausgeführt werden. Beispielsweise können Sie sehen, wie die `fetch-data` Route innerhalb des Haupt-App-Angular-Moduls konfiguriert ist:

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

Beachten Sie, dass der Schutz einer Route nicht den tatsächlichen Endpunkt schützt (für den immer noch ein `[Authorize]` Attribut erforderlich ist), sondern dass der Benutzer nur die Navigation zur angegebenen Client seitigen Route verhindert, wenn er nicht authentifiziert ist.

## <a name="authenticate-api-requests-angular"></a>Authentifizieren von API-Anforderungen (Angular)

Die Authentifizierung von Anforderungen an APIs, die zusammen mit der APP gehostet werden, erfolgt automatisch durch die Verwendung des von der APP definierten http-Client-Interceptors.

## <a name="protect-a-client-side-route-react"></a>Schützen einer Client seitigen Route (reagieren)

Schützen Sie eine Client seitige Route, indem Sie die `AuthorizeRoute` Komponente anstelle der einfachen `Route` Komponente verwenden. Beachten Sie z. b., wie die `fetch-data` Route innerhalb der `App` Komponente konfiguriert ist:

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

Schützen einer Route:

* Schützt nicht den eigentlichen Endpunkt (für den immer noch ein `[Authorize]` Attribut erforderlich ist).
* Verhindert, dass der Benutzer zur angegebenen Client seitigen Route navigiert, wenn er nicht authentifiziert ist.

## <a name="authenticate-api-requests-react"></a>Authentifizieren von API-Anforderungen (reagieren)

Das Authentifizieren von Anforderungen mit reagieren erfolgt durch das erste Importieren der `authService` Instanz aus dem `AuthorizeService`. Das Zugriffs Token wird aus dem `authService` abgerufen und wie unten dargestellt an die Anforderung angefügt. In den Komponenten reagieren erfolgt diese Aufgabe in der Regel in der `componentDidMount` Lifecycle-Methode oder als Ergebnis einer Benutzerinteraktion.

### <a name="import-the-authservice-into-your-component"></a>Importieren Sie den authService in Ihre Komponente.

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a>Abrufen und Anfügen des Zugriffs Tokens an die Antwort

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

## <a name="deploy-to-production"></a>In Produktionsumgebungen bereitstellen

Um die app in der Produktionsumgebung bereitzustellen, müssen die folgenden Ressourcen bereitgestellt werden:

* Eine Datenbank zum Speichern der Identitäts Benutzerkonten und identityserver-Zuweisungen.
* Ein Produktions Zertifikat, das zum Signieren von Token verwendet werden soll.
  * Es gibt keine speziellen Anforderungen für dieses Zertifikat. Dabei kann es sich um ein selbst signiertes Zertifikat oder um ein Zertifikat handeln, das über eine Zertifizierungsstelle bereitgestellt wird.
  * Sie kann über Standard Tools wie PowerShell oder OpenSSL generiert werden.
  * Sie kann im Zertifikat Speicher auf den Ziel Computern installiert oder als *PFX* -Datei mit einem sicheren Kennwort bereitgestellt werden.

### <a name="example-deploy-to-azure-websites"></a>Beispiel: Bereitstellen auf Azure-Websites

In diesem Abschnitt wird beschrieben, wie Sie die app in Azure Websites mithilfe eines Zertifikats bereitstellen, das im Zertifikat Speicher gespeichert ist Um die APP so zu ändern, dass ein Zertifikat aus dem Zertifikat Speicher geladen wird, muss sich der APP Service Plan mindestens auf dem Standard-Tarif befinden, wenn Sie in einem späteren Schritt konfigurieren. Ändern Sie in der Datei *appSettings. JSON* der APP den `IdentityServer` Abschnitt, um die Schlüssel Details einzuschließen:

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

* Der Speicher Name steht für den Namen des Zertifikat Speicher, in dem das Zertifikat gespeichert ist. In diesem Fall verweist Sie auf den persönlichen Benutzerspeicher.
* Der Speicherort stellt den Speicherort dar, von dem das Zertifikat geladen werden soll (`CurrentUser` oder `LocalMachine`).
* Die Name-Eigenschaft für das Zertifikat entspricht dem Distinguished Subject für das Zertifikat.

Stellen Sie die APP bereit, indem Sie die Schritte unter Bereitstellen [der app in Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) bereitstellen, um die erforderlichen Azure-Ressourcen zu erstellen und die app in der Produktionsumgebung bereitzustellen.

Nachdem Sie die obigen Anweisungen ausgeführt haben, wird die app in Azure bereitgestellt, ist aber noch nicht funktionsfähig. Das von der APP verwendete Zertifikat muss noch eingerichtet werden. Suchen Sie den Fingerabdruck des zu verwendenden Zertifikats, und befolgen Sie die Schritte unter [Laden von Zertifikaten](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).

Obwohl diese Schritte SSL erwähnen, gibt es einen Abschnitt mit **privaten Zertifikaten** im Portal, in dem Sie das bereitgestellte Zertifikat für die Verwendung mit der APP hochladen können.

Starten Sie die APP nach diesem Schritt neu, damit Sie funktionsfähig ist.

## <a name="other-configuration-options"></a>Andere Konfigurationsoptionen

Die Unterstützung für die API-Autorisierung basiert auf identityserver mit einem Satz von Konventionen, Standardwerten und Verbesserungen, um die Bedienung für Spas zu vereinfachen. Natürlich ist die volle Leistungsfähigkeit von identityserver im Hintergrund verfügbar, wenn die ASP.net Core Integrationen Ihr Szenario nicht abdecken. Die ASP.net Core-Unterstützung konzentriert sich auf "erst Anbieter-Apps", bei denen alle apps von unserer Organisation erstellt und bereitgestellt werden. Daher wird die Unterstützung für Dinge wie die Zustimmung oder den Verbund nicht angeboten. Verwenden Sie für diese Szenarios identityserver, und befolgen Sie die zugehörigen Dokumentationen.

### <a name="application-profiles"></a>Anwendungsprofile

Anwendungsprofile sind vordefinierte Konfigurationen für apps, die ihre Parameter weiter definieren. Zu diesem Zeitpunkt werden die folgenden Profile unterstützt:

* `IdentityServerSPA`: stellt eine von identityserver gehostete Spa als einzelne Einheit dar.
  * Der `redirect_uri` standardmäßig `/authentication/login-callback`.
  * Der `post_logout_redirect_uri` standardmäßig `/authentication/logout-callback`.
  * Der Satz von Bereichen umfasst die `openid`, `profile`und alle Bereiche, die für die APIs in der APP definiert sind.
  * Der Satz zulässiger oidc-Antworttypen ist `id_token token` oder einzeln (`id_token`, `token`).
  * Der zulässige Antwortmodus ist `fragment`.
* `SPA`: stellt eine Spa dar, die nicht mit identityserver gehostet wird.
  * Der Satz von Bereichen umfasst die `openid`, `profile`und alle Bereiche, die für die APIs in der APP definiert sind.
  * Der Satz zulässiger oidc-Antworttypen ist `id_token token` oder einzeln (`id_token`, `token`).
  * Der zulässige Antwortmodus ist `fragment`.
* `IdentityServerJwt`: stellt eine API dar, die zusammen mit identityserver gehostet wird.
  * Die APP ist so konfiguriert, dass Sie einen einzelnen Bereich hat, der standardmäßig den APP-Namen verwendet.
* `API`: stellt eine API dar, die nicht mit identityserver gehostet wird.
  * Die APP ist so konfiguriert, dass Sie einen einzelnen Bereich hat, der standardmäßig den APP-Namen verwendet.

### <a name="configuration-through-appsettings"></a>Konfiguration über appSettings

Konfigurieren Sie die apps über das Konfigurationssystem, indem Sie Sie der Liste der `Clients` oder `Resources`hinzufügen.

Konfigurieren Sie die `redirect_uri`-und `post_logout_redirect_uri` Eigenschaft jedes Clients, wie im folgenden Beispiel gezeigt:

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

Wenn Sie Ressourcen konfigurieren, können Sie die Bereiche für die Ressource konfigurieren, wie unten dargestellt:

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

### <a name="configuration-through-code"></a>Konfiguration durch Code

Sie können die Clients und Ressourcen auch über den Code konfigurieren, indem Sie eine Überladung von `AddApiAuthorization` verwenden, die eine Aktion zum Konfigurieren von Optionen übernimmt.

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

## <a name="additional-resources"></a>Weitere Ressourcen

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>

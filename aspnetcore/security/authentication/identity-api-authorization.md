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
# <a name="authentication-and-authorization-for-spas"></a>Authentifizierung und Autorisierung für Spas

ASP.net Core 3,0 oder höher bietet die Authentifizierung in Single-Page-Apps (Spas) mithilfe der Unterstützung für die API-Autorisierung. ASP.net Core Identität für das Authentifizieren und Speichern von Benutzern wird mit [identityserver](https://identityserver.io/) für die Implementierung von Open ID Connect kombiniert.

Der **Angular** -und der-Projektvorlagen, die dem Authentifizierungs Parameter in der **Webanwendung (Model-View-Controller)** (MVC) und der **Webanwendung** (Razor Pages) ähneln, wurde ein Authentifizierungs Parameter hinzugefügt. Projektvorlagen. Die zulässigen Parameterwerte sind **None** und **Individual**. Die Projektvorlage "" "" "" "" "" "" "" "" "" "" ""

## <a name="create-an-app-with-api-authorization-support"></a>Erstellen einer APP mit Unterstützung für die API-Autorisierung

Benutzerauthentifizierung und-Autorisierung können mit Angular-und reagingspas verwendet werden. Öffnen Sie eine Befehlsshell, und führen Sie den folgenden Befehl:

**Angular**:

```console
dotnet new angular -o <output_directory_name> -au Individual
```

**Reagieren**:

```console
dotnet new react -o <output_directory_name> -au Individual
```

Der vorherige Befehl erstellt eine ASP.net Core-App mit einem *ClientApp* -Verzeichnis, das die Spa enthält.

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a>Allgemeine Beschreibung der ASP.net Core Komponenten der APP

In den folgenden Abschnitten werden Ergänzungen zum-Projekt beschrieben, wenn die Authentifizierungs Unterstützung enthalten ist:

### <a name="startup-class"></a>Startup-Klasse

Die `Startup` -Klasse verfügt über die folgenden Ergänzungen:

* In der `Startup.ConfigureServices` -Methode:
  * Identität mit der Standardbenutzer Oberfläche:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * Identityserver mit einer zusätzlichen `AddApiAuthorization` Hilfsmethode, mit der einige Standard ASP.net Core Konventionen auf identityserver fest verankert werden:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Authentifizierung mit einer zusätzlichen `AddIdentityServerJwt` Hilfsmethode, die die APP für die Validierung der von identityserver erstellten JWT-Token konfiguriert:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* In der `Startup.Configure` -Methode:
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

Diese Hilfsmethode konfiguriert ein Richtlinien Schema für die APP als Standard Authentifizierungs Handler. Die Richtlinie ist so konfiguriert, dass die Identität alle Anforderungen verarbeitet, die an einen untergeordneten Pfad im Identitäts-URL-Bereich "/Identity" weitergeleitet werden. Der `JwtBearerHandler` verarbeitet alle anderen Anforderungen. Darüber hinaus registriert diese Methode eine `<<ApplicationName>>API` API-Ressource bei identityserver mit einem Standardbereich `<<ApplicationName>>API` von und konfiguriert die JWT-bearertoken-Middleware, um von identityserver für die APP ausgegebene Token zu überprüfen.

### <a name="sampledatacontroller"></a>Sampledatacontroller

Beachten Sie in der Datei " *controllers\sampledatacontroller.cs* " das `[Authorize]` -Attribut, das auf die-Klasse angewendet wird, die angibt, dass der Benutzer auf der Basis der Standard Richtlinie für den Zugriff auf die Ressource autorisiert werden muss. Die Standard Autorisierungs Richtlinie wird so konfiguriert, dass das standardmäßige Authentifizierungsschema verwendet wird, das von `AddIdentityServerJwt` dem oben erwähnten Richtlinien Schema festgelegt wird. Dadurch wird `JwtBearerHandler` die von dieser Hilfsmethode konfigurierte Standard Handler für verwendet. Anforderungen an die app.

### <a name="applicationdbcontext"></a>ApplicationDbContext

Beachten Sie in der Datei *data\applicationdbcontext.cs* , dass `DbContext` in der Identität dasselbe verwendet wird, mit der Ausnahme `ApiAuthorizationDbContext` , dass Sie erweitert wird ( `IdentityDbContext`eine stärker abgeleitete Klasse von), um das Schema für identityserver einzuschließen.

Um die vollständige Kontrolle über das Datenbankschema zu erhalten, erben Sie von `DbContext` einer der verfügbaren Identitäts Klassen und konfigurieren den Kontext so, `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` dass das `OnModelCreating` Identitäts Schema durch Aufrufen von für die-Methode eingeschlossen wird.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

Beachten Sie in der Datei " *controllers\oidcconfigurationcontroller.cs* " den Endpunkt, der bereitgestellt wird, um die oidc-Parameter bereitzustellen, die vom Client verwendet werden müssen.

### <a name="appsettingsjson"></a>appsettings.json

In der *appSettings. JSON* -Datei des Projekt Stamms gibt es einen neuen `IdentityServer` Abschnitt, in dem die Liste der konfigurierten Clients beschrieben wird. Im folgenden Beispiel gibt es einen einzelnen Client. Der Client Name entspricht dem APP-Namen und wird dem OAuth `ClientId` -Parameter gemäß der Konvention zugeordnet. Das Profil gibt den App-Typ an, der konfiguriert wird. Sie wird intern verwendet, um Konventionen zu fördern, die den Konfigurationsprozess für den Server vereinfachen. Es stehen mehrere Profile zur Verfügung, wie im Abschnitt [Anwendungsprofile](#application-profiles) erläutert.

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

In *appSettings. Development. JSON* -Datei des Projekt Stamms. es gibt `IdentityServer` einen Abschnitt, in dem der Schlüssel zum Signieren von Token beschrieben wird. Beim Bereitstellen in der Produktion muss neben der APP ein Schlüssel bereitgestellt und bereitgestellt werden, wie im Abschnitt bereitstellen [in der Produktion](#deploy-to-production) erläutert.

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
  * *login.component.ts*: Behandelt den Anmelde Fluss der app.
  * *logout.component.ts*: Verarbeitet den Abmelde Fluss der app.
  * *login-menu.component.ts*: Ein Widget, das eine der folgenden Gruppen von Links anzeigt:
    * Benutzerprofil Verwaltung und Abmelde Links, wenn der Benutzer authentifiziert ist.
    * Links zur Registrierung und Anmeldung, wenn der Benutzer nicht authentifiziert ist.
* Ein Weiterleitungs `AuthorizeGuard` Wächter, der Routen hinzugefügt werden kann und erfordert, dass ein Benutzer vor dem Besuch der Route authentifiziert wird.
* Ein HTTP-Interceptor `AuthorizeInterceptor` , der das Zugriffs Token an ausgehende HTTP-Anforderungen anbindet, die auf die API abzielen, wenn der Benutzer authentifiziert wird.
* Ein Dienst `AuthorizeService` , der die Details des Authentifizierungsprozesses auf niedrigerer Ebene behandelt und Informationen über den authentifizierten Benutzer für den Rest der App zur Nutzung verfügbar macht.
* Ein Angular-Modul, das Routen definiert, die den Authentifizierungs Teilen der APP zugeordnet sind. Er macht die Anmelde Menü Komponente, den Interceptor, den Wächter und den Dienst für die Nutzung durch den Rest der app verfügbar.

## <a name="general-description-of-the-react-app"></a>Allgemeine Beschreibung der App "reagieren"

Die Unterstützung für die Authentifizierung und API-Autorisierung in der "reagieren"-Vorlage befindet sich im Verzeichnis " *clientapp\src\components\api-Authorization* ". Sie besteht aus den folgenden Elementen:

* 4 Komponenten:
  * *Login.js*: Behandelt den Anmelde Fluss der app.
  * *Logout. js*: Verarbeitet den Abmelde Fluss der app.
  * *LoginMenu.js*: Ein Widget, das eine der folgenden Gruppen von Links anzeigt:
    * Benutzerprofil Verwaltung und Abmelde Links, wenn der Benutzer authentifiziert ist.
    * Links zur Registrierung und Anmeldung, wenn der Benutzer nicht authentifiziert ist.
  * *Autorizeroute. js*: Eine Routen Komponente, bei der ein Benutzer vor dem Rendern der im `Component` -Parameter angegeben Komponente authentifiziert werden muss.
* Eine exportierte `authService` Instanz der `AuthorizeService` -Klasse, die die Details des Authentifizierungsprozesses auf niedrigerer Ebene behandelt und Informationen über den authentifizierten Benutzer für den Rest der App zur Nutzung verfügbar macht.

Nachdem Sie die Hauptkomponenten der Lösung kennengelernt haben, können Sie sich einen tieferen Einblick in die einzelnen Szenarios für die APP machen.

## <a name="require-authorization-on-a-new-api"></a>Autorisierung für eine neue API erforderlich

Standardmäßig ist das System so konfiguriert, dass die Autorisierung für neue APIs problemlos erforderlich ist. Erstellen Sie hierzu einen neuen Controller, und fügen Sie das `[Authorize]` -Attribut zur Controller Klasse oder zu einer beliebigen Aktion im Controller hinzu.

## <a name="protect-a-client-side-route-angular"></a>Schützen einer Client seitigen Route (Angular)

Der Schutz einer Client seitigen Route erfolgt durch Hinzufügen von Autorisierungs Wächter zur Liste der Wächter, die beim Konfigurieren einer Route ausgeführt werden. Beispielsweise können Sie sehen, wie die `fetch-data` Route innerhalb des Haupt-App-Angular-Moduls konfiguriert ist:

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

Beachten Sie, dass der Schutz einer Route nicht den eigentlichen Endpunkt schützt (für den immer noch ein `[Authorize]` angewendetes Attribut erforderlich ist), sondern dass der Benutzer nur die Navigation zur angegebenen Client seitigen Route verhindert, wenn er nicht authentifiziert ist.

## <a name="authenticate-api-requests-angular"></a>Authentifizieren von API-Anforderungen (Angular)

Die Authentifizierung von Anforderungen an APIs, die zusammen mit der APP gehostet werden, erfolgt automatisch durch die Verwendung des von der APP definierten http-Client-Interceptors.

## <a name="protect-a-client-side-route-react"></a>Schützen einer Client seitigen Route (reagieren)

Schützen Sie eine Client seitige Route mithilfe der `AuthorizeRoute` Komponente anstelle der einfachen `Route` Komponente. Beachten Sie z. b. `fetch-data` , wie die Route innerhalb `App` der-Komponente konfiguriert ist:

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

Schützen einer Route:

* Schützt nicht den eigentlichen Endpunkt (für den immer noch `[Authorize]` ein angewendetes Attribut erforderlich ist).
* Verhindert, dass der Benutzer zur angegebenen Client seitigen Route navigiert, wenn er nicht authentifiziert ist.

## <a name="authenticate-api-requests-react"></a>Authentifizieren von API-Anforderungen (reagieren)

Das Authentifizieren von Anforderungen mit reagieren erfolgt durch das erste `authService` Importieren der- `AuthorizeService`Instanz aus. Das Zugriffs Token wird aus dem `authService` abgerufen und wie unten dargestellt an die Anforderung angefügt. In reagiere Komponenten wird diese Aufgabe in der Regel in `componentDidMount` der Lebenszyklus Methode oder als Ergebnis einer Benutzerinteraktion ausgeführt.

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

In diesem Abschnitt wird beschrieben, wie Sie die app in Azure Websites mithilfe eines Zertifikats bereitstellen, das im Zertifikat Speicher gespeichert ist Um die APP so zu ändern, dass ein Zertifikat aus dem Zertifikat Speicher geladen wird, muss sich der APP Service Plan mindestens auf dem Standard-Tarif befinden, wenn Sie in einem späteren Schritt konfigurieren. Ändern`IdentityServer` Sie in der Datei *appSettings. JSON* der APP den Abschnitt, um die Schlüssel Details einzuschließen:

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

* Die Name-Eigenschaft für das Zertifikat entspricht dem Distinguished Subject für das Zertifikat.
* Der Speicherort stellt dar, wo das Zertifikat geladen werden`CurrentUser` soll `LocalMachine`(oder).
* Der Speicher Name steht für den Namen des Zertifikat Speicher, in dem das Zertifikat gespeichert ist. In diesem Fall verweist Sie auf den persönlichen Benutzerspeicher.

Stellen Sie die APP bereit, indem Sie die Schritte unter Bereitstellen [der app in Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) bereitstellen, um die erforderlichen Azure-Ressourcen zu erstellen und die app in der Produktionsumgebung bereitzustellen.

Nachdem Sie die obigen Anweisungen ausgeführt haben, wird die app in Azure bereitgestellt, ist aber noch nicht funktionsfähig. Das von der APP verwendete Zertifikat muss noch eingerichtet werden. Suchen Sie den Fingerabdruck des zu verwendenden Zertifikats, und befolgen Sie die Schritte unter [Laden von Zertifikaten](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).

Obwohl diese Schritte SSL erwähnen, gibt es einen Abschnitt mit **privaten Zertifikaten** im Portal, in dem Sie das bereitgestellte Zertifikat für die Verwendung mit der APP hochladen können.

Starten Sie die APP nach diesem Schritt neu, damit Sie funktionsfähig ist.

## <a name="other-configuration-options"></a>Andere Konfigurationsoptionen

Die Unterstützung für die API-Autorisierung basiert auf identityserver mit einem Satz von Konventionen, Standardwerten und Verbesserungen, um die Bedienung für Spas zu vereinfachen. Natürlich ist die volle Leistungsfähigkeit von identityserver im Hintergrund verfügbar, wenn die ASP.net Core Integrationen Ihr Szenario nicht abdecken. Die ASP.net Core-Unterstützung konzentriert sich auf "erst Anbieter-Apps", bei denen alle apps von unserer Organisation erstellt und bereitgestellt werden. Daher wird die Unterstützung für Dinge wie die Zustimmung oder den Verbund nicht angeboten. Verwenden Sie für diese Szenarios identityserver, und befolgen Sie die zugehörigen Dokumentationen.

### <a name="application-profiles"></a>Anwendungsprofile

Anwendungsprofile sind vordefinierte Konfigurationen für apps, die ihre Parameter weiter definieren. Zu diesem Zeitpunkt werden die folgenden Profile unterstützt:

* `IdentityServerSPA`: Stellt eine von identityserver gehostete Spa als einzelne Einheit dar.
  * `redirect_uri` Der`/authentication/login-callback`Standardwert ist.
  * `post_logout_redirect_uri` Der`/authentication/logout-callback`Standardwert ist.
  * Der Satz von Bereichen umfasst `openid`, und alle Bereiche, die für die APIs in der APP definiert sind. `profile`
  * Der Satz zulässiger oidc-Antworttypen `id_token token` ist oder jeweils einzeln (`id_token`, `token`).
  * Der zulässige Antwortmodus ist `fragment`.
* `SPA`: Stellt eine Spa dar, die nicht mit identityserver gehostet wird.
  * Der Satz von Bereichen umfasst `openid`, und alle Bereiche, die für die APIs in der APP definiert sind. `profile`
  * Der Satz zulässiger oidc-Antworttypen `id_token token` ist oder jeweils einzeln (`id_token`, `token`).
  * Der zulässige Antwortmodus ist `fragment`.
* `IdentityServerJwt`: Stellt eine API dar, die zusammen mit identityserver gehostet wird.
  * Die APP ist so konfiguriert, dass Sie einen einzelnen Bereich hat, der standardmäßig den APP-Namen verwendet.
* `API`: Stellt eine API dar, die nicht mit identityserver gehostet wird.
  * Die APP ist so konfiguriert, dass Sie einen einzelnen Bereich hat, der standardmäßig den APP-Namen verwendet.

### <a name="configuration-through-appsettings"></a>Konfiguration über appSettings

Konfigurieren Sie die apps über das Konfigurationssystem, indem Sie Sie der Liste `Clients` oder `Resources`hinzufügen.

Konfigurieren Sie die- `redirect_uri` Eigenschaft `post_logout_redirect_uri` und die-Eigenschaft des Clients, wie im folgenden Beispiel gezeigt:

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

Sie können die Clients und Ressourcen auch über den Code konfigurieren, indem Sie `AddApiAuthorization` eine Überladung von verwenden, die eine Aktion zum Konfigurieren von Optionen übernimmt.

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
* <xref:security/authentication/scaffold-identity>

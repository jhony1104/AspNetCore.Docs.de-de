---
title: Sichern einer gehosteten ASP.NET Core Blazor WebAssembly-App mit Azure Active Directory
author: guardrex
description: ''
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 9332eddd3d428e8a25910d387f95b870926d5ae5
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103291"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory"></a>Sichern einer gehosteten ASP.NET Core Blazor WebAssembly-App mit Azure Active Directory

Von [Javier Calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)

In diesem Artikel wird beschrieben, wie Sie eine gehostete [Blazor WebAssembly-App](xref:blazor/hosting-models#blazor-webassembly) erstellen, die [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) für die Authentifizierung verwendet.

## <a name="register-apps-in-aad-and-create-solution"></a>Registrieren von Apps in AAD und Erstellen einer Lösung

### <a name="create-a-tenant"></a>Erstellen eines Mandanten

Befolgen Sie die Anweisungen unter [Schnellstart: Einrichten eines Mandanten](/azure/active-directory/develop/quickstart-create-new-tenant), um einen Mandanten in AAD zu erstellen.

### <a name="register-a-server-api-app"></a>Registrieren einer Server-API-App

Befolgen Sie die Anweisungen unter [Schnellstart: Registrieren einer Anwendung bei Microsoft Identity Platform](/azure/active-directory/develop/quickstart-register-app) und in den anschließenden Azure AAD-Themen, um eine AAD-App für die *Server-API-App* zu registrieren, und tun Sie danach Folgendes:

1. Klicken Sie unter **Azure Active Directory** > **App-Registrierungen** auf **Neue Registrierung**.
1. Geben Sie einen **Namen** für die App an (z. B. **Blazor Server AAD**).
1. Wählen Sie einen **Unterstützten Kontotyp** aus. Hier können Sie die Option **Nur Konten in diesem Organisationsverzeichnis** (einzelner Mandant) auswählen.
1. Die *Server-API-App* erfordert in diesem Szenario keinen **Umleitungs-URI**. Belassen Sie daher die Dropdownauswahl bei **Web**, und geben Sie keinen Umleitungs-URI ein.
1. Deaktivieren Sie das Kontrollkästchen bei **Berechtigungen** > **Administratoreinwilligung für openid- und offline_access-Berechtigungen erteilen**.
1. Wählen Sie **Registrieren**.

Notieren Sie sich folgende Informationen:

* Anwendungs-ID (Client-ID) der *Server-API-App* (z. B. `11111111-1111-1111-1111-111111111111`)
* Verzeichnis-ID (Mandanten-ID), z. B. `222222222-2222-2222-2222-222222222222`
* Domäne des AAD-Mandanten (z. B. `contoso.onmicrosoft.com`): Die Domäne ist als **Herausgeberdomäne** auf dem Blatt **Branding** des Azure-Portals für die registrierte App verfügbar.

Entfernen Sie unter **API-Berechtigungen** die Berechtigung **Microsoft Graph** > **User.Read**, da die App keine Anmeldung oder Zugriff auf Benutzerprofile benötigt.

Gehen Sie unter **Eine API verfügbar machen** wie folgt vor:

1. Wählen Sie **Bereich hinzufügen**.
1. Wählen Sie **Speichern und fortfahren** aus.
1. Geben Sie einen **Bereichsnamen** an (z. B. `API.Access`).
1. Geben Sie einen **Anzeigenamen der Administratorzustimmung** an (z. B. `Access API`).
1. Geben Sie eine **Beschreibung der Administratorzustimmung** an (z. B. `Allows the app to access server app API endpoints.`).
1. Vergewissern Sie sich, dass **Zustand** auf **Aktiviert** gesetzt ist.
1. Wählen Sie **Bereich hinzufügen** aus.

Notieren Sie sich folgende Informationen:

* App-ID-URI (z. B. `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111` oder der von Ihnen angegebene benutzerdefinierte Wert)
* Standardbereich (z. B. `API.Access`)

### <a name="register-a-client-app"></a>Registrieren einer Client-App

Befolgen Sie die Anweisungen unter [Schnellstart: Registrieren einer Anwendung bei Microsoft Identity Platform](/azure/active-directory/develop/quickstart-register-app) und in den anschließenden Azure AAD-Themen, um eine AAD-App für die *Client-App* zu registrieren, und tun Sie danach Folgendes:

1. Klicken Sie unter **Azure Active Directory** > **App-Registrierungen** auf **Neue Registrierung**.
1. Geben Sie einen **Namen** für die App an (z. B. **Blazor Client AAD**).
1. Wählen Sie einen **Unterstützten Kontotyp** aus. Hier können Sie die Option **Nur Konten in diesem Organisationsverzeichnis** (einzelner Mandant) auswählen.
1. Belassen Sie die Dropdownauswahl für **Umleitungs-URI** auf **Web**, und geben Sie den folgenden Umleitungs-URI an: `https://localhost:{PORT}/authentication/login-callback`. Der Standardport für eine App, die auf Kestrel ausgeführt wird, ist 5001. Wenn die App auf einem anderen Kestrel-Port ausgeführt wird, verwenden Sie den Port der App. Für IIS Express finden Sie den zufällig generierten Port für die App in den Eigenschaften der Server-App im Panel **Debuggen**. Da die App an dieser Stelle noch nicht existiert und der IIS Express-Port unbekannt ist, kehren Sie nach dem Erstellen der App zu diesem Schritt zurück, und aktualisieren Sie den Umleitungs-URI. Im Abschnitt zum [Erstellen der App](#create-the-app) erscheint ein Hinweis, um IIS Express-Benutzer an die Aktualisierung des Umleitungs-URIs zu erinnern.
1. Deaktivieren Sie das Kontrollkästchen bei **Berechtigungen** > **Administratoreinwilligung für openid- und offline_access-Berechtigungen erteilen**.
1. Wählen Sie **Registrieren**.

Notieren Sie sich die Anwendungs-ID (Client-ID) der *Client-App* (z. B. `33333333-3333-3333-3333-333333333333`).

Gehen Sie unter **Authentifizierung** > **Plattformkonfigurationen** > **Web** wie folgt vor:

1. Vergewissern Sie sich, dass der **Umleitungs-URI** von `https://localhost:{PORT}/authentication/login-callback` vorhanden ist.
1. Aktivieren Sie für **Implizite Gewährung** die Kontrollkästchen **Zugriffstoken** und **ID-Token**.
1. Die verbleibenden Standardwerte für die App müssen für dieses Szenario nicht angepasst werden.
1. Klicken Sie auf die Schaltfläche **Speichern**.

Gehen Sie unter **API-Berechtigungen** wie folgt vor:

1. Sorgen Sie dafür, dass die App über die Berechtigung **Microsoft Graph** > **User.Read** verfügt.
1. Klicken Sie auf **Berechtigung hinzufügen** und dann auf **Meine APIs**.
1. Wählen Sie die *Server-API-App* aus der Spalte **Name** aus (z. B. **Blazor Server AAD**).
1. Öffnen Sie die **API**-Liste.
1. Ermöglichen Sie den Zugriff auf die API (z. B. `API.Access`).
1. Wählen Sie **Berechtigungen hinzufügen** aus.
1. Klicken Sie auf die Schaltfläche **Administratorzustimmung für {Mandantenname} erteilen**. Klicken Sie auf **Ja**, um zu bestätigen.

### <a name="create-the-app"></a>Erstellen der App

Ersetzen Sie die Platzhalter im folgenden Befehl durch die zuvor notierten Informationen, und führen Sie den Befehl in einer Befehlsshell aus:

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

Zum Angeben eines Ausgabespeicherorts (wodurch ein Projektordner erstellt wird, falls noch nicht vorhanden) schließen Sie die Ausgabeoption mit einem Pfad (z. B. `-o BlazorSample`) in den Befehl ein. Der Name des Ordners wird auch Teil des Projektnamens.

> [!NOTE]
> Übergeben Sie den App-ID-URI an die Option `app-id-uri`. Beachten Sie jedoch, dass eine Änderung der Konfiguration in der Client-App erforderlich sein könnte, die im Abschnitt [Zugriffstokenbereiche](#access-token-scopes) beschrieben ist.

> [!NOTE]
> Im Azure-Portal ist der **Authentifizierung** > **Plattformkonfigurationen** > **Web** > **Umleitungs-URI** der *Client-App* für Apps für Port 5001 konfiguriert, die auf dem Kestrel-Server mit Standardeinstellungen ausgeführt werden.
>
> Wenn die *Client-App* auf einem zufälligen IIS Express-Port ausgeführt wird, finden Sie den Port der App in den Eigenschaften der *Server-App* im Panel **Debuggen**.
>
> Wenn der Port nicht zuvor mit dem bekannten Port der *Client-App* konfiguriert wurde, kehren Sie zur *Client-App-Registrierung* im Azure-Portal zurück, und aktualisieren Sie den Umleitungs-URI mit dem korrekten Port.

## <a name="server-app-configuration"></a>Server-App-Konfiguration

*Dieser Abschnitt bezieht sich auf die **Server**-App der Lösung.*

### <a name="authentication-package"></a>Authentifizierungspaket

Die Unterstützung für die Authentifizierung und Autorisierung von Aufrufen von ASP.NET Core-Web-APIs wird vom Paket [Microsoft.AspNetCore.Authentication.AzureAD.UI](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI/) bereitgestellt:

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="3.1.4" />
```

### <a name="authentication-service-support"></a>Unterstützung für Authentifizierungsdienste

Die `AddAuthentication`-Methode richtet Authentifizierungsdienste innerhalb der App ein und konfiguriert den JWT-Bearerhandler als Standardauthentifizierungsmethode. Die <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A>-Methode richtet die spezifischen Parameter im JWT-Bearerhandler ein, die zum Überprüfen der von Azure Active Directory ausgegebenen Token erforderlich sind:

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> und <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> stellen Folgendes sicher:

* Die App versucht, Token auf eingehende Anforderungen zu analysieren und zu überprüfen.
* Jede Anforderung, die versucht, ohne die richtigen Anmeldeinformationen auf eine geschützte Ressource zuzugreifen, schlägt fehl.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a>User.Identity.Name

Standardmäßig füllt die Server-App-API `User.Identity.Name` mit dem Wert aus dem `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`-Anspruchstyp (z. B. `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`) auf.

Konfigurieren Sie [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) von <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`, um die App so zu konfigurieren, dass sie den Wert aus dem `name`-Anspruchstyp empfängt:

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a>App-Einstellungen

Die Datei *appsettings.json* enthält die Optionen zum Konfigurieren des JWT-Bearerhandlers, der zum Überprüfen von Zugriffstoken verwendet wird.

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
  }
}
```

Beispiel:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
  }
}
```

### <a name="weatherforecast-controller"></a>WeatherForecast-Controller

Der WeatherForecast-Controller (*Controllers/WeatherForecastController.cs*) macht eine geschützte API mit auf den Controller angewendetem [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)-Attribut verfügbar. Es ist **wichtig**, Folgendes zu verstehen:

* Das [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)-Attribut in diesem API-Controller ist die einzige Möglichkeit, diese API vor nicht autorisiertem Zugriff zu schützen.
* Das in der Blazor WebAssembly-App verwendete [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)-Attribut dient nur als Hinweis für die App, dass der Benutzer autorisiert sein sollte, damit die App richtig funktioniert.

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a>Client-App-Konfiguration

*Dieser Abschnitt bezieht sich auf die **Client**-App der Lösung.*

### <a name="authentication-package"></a>Authentifizierungspaket

Wenn eine App zum Verwenden von Geschäfts-, Schul- oder Unikonten (`SingleOrg`) erstellt wird, erhält die App automatisch einen Paketverweis für die [Authentifizierungsbibliothek von Microsoft](/azure/active-directory/develop/msal-overview) ([Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)). Das Paket stellt einige Primitive bereit, die der App beim Authentifizieren von Benutzern und beim Abrufen von Token zum Aufrufen geschützter APIs helfen.

Wenn Sie einer App eine Authentifizierung hinzufügen, fügen Sie das Paket manuell der Projektdatei der App hinzu:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

Das [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)-Paket fügt der App das Paket [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) transitiv hinzu.

### <a name="authentication-service-support"></a>Unterstützung für Authentifizierungsdienste

Es wird Unterstützung für <xref:System.Net.Http.HttpClient>-Instanzen hinzugefügt, die bei Anforderungen an das Serverprojekt Zugriffstoken einschließen.

*Program.cs*:

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

Der Platzhalter `{APP ASSEMBLY}` ist der Assemblyname der App (z. B. `BlazorSample.ServerAPI`).

Die Unterstützung für die Authentifizierung von Benutzern wird im Dienstcontainer mit der vom Paket [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) bereitgestellten <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>-Erweiterungsmethode registriert. Diese Methode richtet die Dienste ein, die erforderlich sind, damit die App mit dem Identitätsanbieter interagiert.

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

Die <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>-Methode akzeptiert einen Rückruf zum Konfigurieren der für die Authentifizierung der App erforderlichen Parameter. Die zum Konfigurieren der App erforderlichen Werte können von der AAD-Konfiguration im Azure-Portal beim Registrieren der App abgerufen werden.

Die Konfiguration wird durch die Datei *wwwroot/appsettings.json* bereitgestellt.

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

Beispiel:

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a>Zugriffstokenbereiche

Zu den Standard-Zugriffstokenbereichen zählen die Zugriffstokenbereiche, auf die Folgendes zutrifft:

* standardmäßig in der Anmeldeanforderung enthalten
* zum Bereitstellen eines Zugriffstokens direkt nach der Authentifizierung verwendet

Alle Bereiche müssen gemäß den Azure Active Directory-Regeln zur selben App gehören. Zusätzliche Bereiche können nach Bedarf für zusätzliche API-Apps hinzugefügt werden:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

Weitere Informationen finden Sie in den folgenden Abschnitten des Artikels zu *zusätzlichen Szenarios*:

* [Anfordern zusätzlicher Zugriffstoken](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [Anfügen von Token an ausgehende Anforderungen](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a>Imports-Datei

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Indexseite

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a>App-Komponente

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>RedirectToLogin-Komponente

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>LoginDisplay-Komponente

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a>Authentication-Komponente

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>FetchData-Komponente

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>Ausführen der App

Führen Sie die App aus dem Serverprojekt aus. Wenn Sie Visual Studio verwenden, haben Sie folgende beiden Möglichkeiten:

* Wählen Sie in der Dropdownliste **Startprojekte** in der Symbolleiste die *Server-API-App* aus, und klicken Sie auf **Ausführen**.
* Wählen Sie das Serverprojekt im **Projektmappen-Explorer** aus, und klicken Sie in der Symbolleiste auf **Ausführen**, oder starten Sie die App über das Menü **Debuggen**.

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:blazor/security/webassembly/additional-scenarios>
* [Nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen in einer App mit einem sicheren Standardclient](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [Dokumentation zu Microsoft Identity Platform](/azure/active-directory/develop/)

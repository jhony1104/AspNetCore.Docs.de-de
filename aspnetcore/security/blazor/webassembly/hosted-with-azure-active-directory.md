---
title: Sichern einer ASP.net Core Blazor Webassembly-gehosteten App mit Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 6ff95f0c5c925cbafef2b997a6cb23aeb15ff1aa
ms.sourcegitcommit: 1250c90c8d87c2513532be5683640b65bfdf9ddb
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83153961"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory"></a>Sichern einer ASP.net Core Blazor Webassembly-gehosteten App mit Azure Active Directory

Von [Javier calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

In diesem Artikel wird beschrieben, wie Sie eine [ Blazor Webassembly-gehostete App](xref:blazor/hosting-models#blazor-webassembly) erstellen, die [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) für die Authentifizierung verwendet.

## <a name="register-apps-in-aad-and-create-solution"></a>Registrieren von apps in Aad und Erstellen einer Lösung

### <a name="create-a-tenant"></a>Erstellen eines Mandanten

Befolgen Sie die Anweisungen unter [Schnellstart: Einrichten eines](/azure/active-directory/develop/quickstart-create-new-tenant) Mandanten zum Erstellen eines Mandanten in Aad.

### <a name="register-a-server-api-app"></a>Registrieren einer Server-API-APP

Befolgen Sie die Anleitungen unter [Schnellstart: Registrieren einer Anwendung mit der Microsoft Identity Platform](/azure/active-directory/develop/quickstart-register-app) und nachfolgende Azure Aad-Themen zum Registrieren einer Aad-App für die *Server-API-APP* im **Azure Active Directory**  >  **App-Registrierungen** Bereich der Azure-Portal:

1. Wählen Sie **Neue Registrierung** aus.
1. Geben Sie einen **Namen** für die APP an (z. b. ** Blazor Server Aad**).
1. Wählen Sie einen **unterstützten Kontotyp**aus. Für diese Vorgehens Art können Sie **nur Konten in diesem Organisations Verzeichnis** auswählen (einzelner Mandant).
1. Für die *Server-API-APP* ist in diesem Szenario kein **Umleitungs-URI** erforderlich. lassen Sie die Dropdown-Datei also auf **Web** festgelegt und geben Sie keinen Umleitungs-URI
1. Deaktivieren Sie **das**  >  Kontrollkästchen**Administrator Berechtigungen für OpenID und offline_access Berechtigungen gewähren** .
1. Wählen Sie **Registrieren**.

Entfernen Sie unter **API-Berechtigungen**die Berechtigung **Microsoft Graph**  >  **User. Read** , da die APP keinen Anmelde-oder uerprofilzugriff erfordert.

In machen Sie **eine API**verfügbar:

1. Wählen Sie **Bereich hinzufügen**.
1. Wählen Sie **Speichern und fortfahren** aus.
1. Geben Sie einen **Bereichs Namen** an (z `API.Access` . b.).
1. Geben Sie einen **anzeigen Amen für die Administrator Zustimmung** an (z. b `Access API` .).
1. Geben Sie eine Beschreibung für die **Administrator Zustimmung** an (z `Allows the app to access server app API endpoints.` . b.).
1. Vergewissern Sie sich, dass der **Status** auf **aktiviert**festgelegt ist.
1. Wählen Sie **Bereich hinzufügen** aus.

Notieren Sie sich die folgenden Informationen:

* *Server-API-APP* Anwendungs-ID (Client-ID) (z. b. `11111111-1111-1111-1111-111111111111` )
* APP-ID-URI (z. b., `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111` oder der von Ihnen angegebene benutzerdefinierte Wert)
* Verzeichnis-ID (Mandanten-ID) (z. b. `222222222-2222-2222-2222-222222222222` )
* Aad-Mandanten Domäne (z. b. `contoso.onmicrosoft.com` )
* Standardbereich (z. b. `API.Access` )

### <a name="register-a-client-app"></a>Registrieren einer Client-App

Befolgen Sie die Anleitungen unter [Schnellstart: Registrieren einer Anwendung mit der Microsoft Identity Platform](/azure/active-directory/develop/quickstart-register-app) und nachfolgende Azure Aad-Themen zum Registrieren einer Aad-App für die *Client-App* im **Azure Active Directory**  >  **App-Registrierungen** Bereich der Azure-Portal:

1. Wählen Sie **Neue Registrierung** aus.
1. Geben Sie einen **Namen** für die APP an (z. b. ** Blazor Client Aad**).
1. Wählen Sie einen **unterstützten Kontotyp**aus. Für diese Vorgehens Art können Sie **nur Konten in diesem Organisations Verzeichnis** auswählen (einzelner Mandant).
1. Belassen Sie den **Umleitungs-URI** -Dropdown auf **Web**, und geben Sie einen Umleitungs-URI von an `https://localhost:5001/authentication/login-callback` .
1. Deaktivieren Sie **das**  >  Kontrollkästchen**Administrator Berechtigungen für OpenID und offline_access Berechtigungen gewähren** .
1. Wählen Sie **Registrieren**.

Im Web der **Authentifizierungs**  >  **Platt Form Konfigurationen**  >  **Web**:

1. Bestätigen Sie, dass der **Umleitungs-URI** von `https://localhost:5001/authentication/login-callback` vorhanden ist
1. Aktivieren Sie für **implizite Gewährung**die Kontrollkästchen für **Zugriffs Token** und **ID-Token**.
1. Die verbleibenden Standardwerte für die APP sind für diese Art von akzeptabel.
1. Wählen Sie die Schaltfläche **Speichern** aus.

In **API-Berechtigungen**:

1. Vergewissern Sie sich, dass die APP über **Microsoft Graph**  >  **User. Read** -Berechtigung verfügt.
1. Wählen Sie **Berechtigung hinzufügen** und dann **meine APIs**aus.
1. Wählen Sie die *Server-API-APP* aus der Spalte **Name** (z. b. ** Blazor Server Aad**) aus.
1. Öffnen Sie die **API** -Liste.
1. Aktivieren Sie den Zugriff auf die API (z `API.Access` . b.).
1. Wählen Sie **Berechtigungen hinzufügen** aus.
1. Wählen Sie die Schaltfläche **Administrator Inhalt für {TENANT Name} erteilen aus** . Klicken Sie zum Bestätigen auf **Ja**.

Notieren Sie sich die Anwendungs-ID der *Client-App* (Client-ID) (z `33333333-3333-3333-3333-333333333333` . b.).

### <a name="create-the-app"></a>Erstellen der App

Ersetzen Sie die Platzhalter im folgenden Befehl durch die zuvor aufgezeichneten Informationen, und führen Sie den Befehl in einer Befehlsshell aus:

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

Um den Ausgabe Speicherort anzugeben, der einen Projektordner erstellt, wenn er nicht vorhanden ist, schließen Sie die Output-Option in den Befehl mit einem Pfad (z `-o BlazorSample` . b.) ein. Der Name des Ordners wird auch Teil des Projekt namens.

> [!NOTE]
> Übergeben Sie den APP-ID-URI an die- `app-id-uri` Option, aber beachten Sie, dass in der Client-App möglicherweise eine Konfigurationsänderung erforderlich ist, die im Abschnitt [Zugriffs Token-Bereiche](#access-token-scopes) beschrieben wird.

## <a name="server-app-configuration"></a>Server-App-Konfiguration

*Dieser Abschnitt bezieht sich auf die **Server** -App der Lösung.*

### <a name="authentication-package"></a>Authentifizierungs Paket

Die Unterstützung für das Authentifizieren und autorialisieren von Aufrufen von ASP.net Core Web-APIs wird von bereitgestellt `Microsoft.AspNetCore.Authentication.AzureAD.UI` :

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="{VERSION}" />
```

### <a name="authentication-service-support"></a>Unterstützung für Authentifizierungsdienst

Die `AddAuthentication` -Methode richtet Authentifizierungsdienste innerhalb der APP ein und konfiguriert den JWT-bearerhandler als Standard Authentifizierungsmethode. Die- `AddAzureADBearer` Methode richtet die spezifischen Parameter im JWT-bearerhandler ein, der zum Überprüfen der vom Azure Active Directory ausgegebenen Token erforderlich ist:

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

`UseAuthentication`und `UseAuthorization` Stellen Sie Folgendes sicher:

* Die APP versucht, Token bei eingehenden Anforderungen zu analysieren und zu überprüfen.
* Jede Anforderung, die versucht, auf eine geschützte Ressource ohne korrekte Anmelde Informationen zuzugreifen, schlägt fehl.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a>Benutzer. Identity .. Benennen

Standardmäßig füllt die Server-App-API `User.Identity.Name` mit dem Wert aus dem `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` Anspruchstyp (z. b `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com` .).

Um die APP so zu konfigurieren, dass Sie den Wert aus dem `name` Anspruchstyp empfängt, konfigurieren Sie [tokenvalidationparameters. nameclaimtype](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) von <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices` :

```csharp
services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a>App-Einstellungen

Die Datei *appSettings. JSON* enthält die Optionen zum Konfigurieren des JWT-bearerhandlers, der zum Überprüfen von Zugriffs Token verwendet wird.

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

### <a name="weatherforecast-controller"></a>Weatherforecast-Controller

Der weatherforecast-Controller (*Controllers/weatherforecastcontroller. cs*) macht eine geschützte API verfügbar, wobei das- `[Authorize]` Attribut auf den Controller angewendet wird. Es ist **wichtig** , Folgendes zu verstehen:

* Das `[Authorize]` Attribut in diesem API-Controller ist die einzige Möglichkeit, diese API vor nicht autorisiertem Zugriff zu schützen.
* Das `[Authorize]` in der Blazor Webassembly-App verwendete Attribut dient nur als Hinweis für die APP, dass der Benutzer autorisiert werden muss, damit die APP ordnungsgemäß funktioniert.

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

*Dieser Abschnitt bezieht sich auf die **Client** -App der Lösung.*

### <a name="authentication-package"></a>Authentifizierungs Paket

Wenn eine APP für die Verwendung von Geschäfts-, Schul-oder unikonten () erstellt wird `SingleOrg` , erhält die APP automatisch einen Paket Verweis für die [Microsoft-Authentifizierungs Bibliothek](/azure/active-directory/develop/msal-overview) ( `Microsoft.Authentication.WebAssembly.Msal` ). Das Paket enthält eine Reihe von primitiven, die der APP helfen, Benutzer zu authentifizieren und Token abzurufen, um geschützte APIs aufzurufen.

Wenn Sie eine Authentifizierung zu einer APP hinzufügen, fügen Sie das Paket manuell zur Projektdatei der APP hinzu:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

Ersetzen Sie `{VERSION}` im vorherigen Paket Verweis durch die Version des Pakets, das `Microsoft.AspNetCore.Blazor.Templates` im Artikel angezeigt wird <xref:blazor/get-started> .

Das `Microsoft.Authentication.WebAssembly.Msal` Paket fügt das Paket transitiv der `Microsoft.AspNetCore.Components.WebAssembly.Authentication` app hinzu.

### <a name="authentication-service-support"></a>Unterstützung für Authentifizierungsdienst

Es wird Unterstützung für- `HttpClient` Instanzen hinzugefügt, die Zugriffs Token enthalten, wenn Anforderungen an das Server Projekt gestellt werden.

*Program.cs*:

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

Die Unterstützung für das Authentifizieren von Benutzern wird im Dienst Container mit der `AddMsalAuthentication` vom Paket bereitgestellten Erweiterungsmethode registriert `Microsoft.Authentication.WebAssembly.Msal` . Diese Methode richtet alle Dienste ein, die für die Interaktion der APP mit dem Identity Anbieter (IP) erforderlich sind.

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

Die- `AddMsalAuthentication` Methode akzeptiert einen Rückruf, um die Parameter zu konfigurieren, die zum Authentifizieren einer APP erforderlich sind. Die für die Konfiguration der APP erforderlichen Werte können aus der Aad-Konfiguration des Azure-Portals abgerufen werden, wenn Sie die APP registrieren.

Die Konfiguration wird von der Datei " *wwwroot/appSettings. JSON* " bereitgestellt:

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

### <a name="access-token-scopes"></a>Zugriffs Token-Bereiche

Die standardzugriffstokenbereiche stellen die Liste der zugriffstokenbereiche dar, die lauten:

* Standardmäßig in der Anmelde Anforderung enthalten.
* Wird verwendet, um unmittelbar nach der Authentifizierung ein Zugriffs Token bereitzustellen.

Alle Bereiche müssen pro Azure Active Directory Regeln zur gleichen App gehören. Für zusätzliche API-Apps können nach Bedarf weitere Bereiche hinzugefügt werden:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> Wenn die Azure-Portal einen Bereichs-URI bereitstellt und **die APP eine nicht behandelte Ausnahme** auslöst, wenn Sie eine 401-nicht *autorisierte* Antwort von der API empfängt, verwenden Sie einen Bereichs-URI, der das Schema und den Host nicht enthält. Der Azure-Portal kann z. b. eines der folgenden Bereich-URI-Formate bereitstellen:
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> Geben Sie den Bereichs-URI ohne das Schema und den Host an:
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

Weitere Informationen finden Sie in den folgenden Abschnitten des Artikels *weitere Szenarien* :

* [Anfordern zusätzlicher Zugriffstoken](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [Anfügen von Token an ausgehende Anforderungen](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a>Import Datei

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Indexseite

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a>App-Komponente

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>Redirecttologin-Komponente

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Logindisplay-Komponente

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a>Authentifizierungs Komponente

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>Fetchdata-Komponente

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>Ausführen der App

Führen Sie die APP aus dem Server Projekt aus. Wenn Sie Visual Studio verwenden, wählen Sie das Server Projekt in **Projektmappen-Explorer** aus, und klicken Sie auf der Symbolleiste auf die Schaltfläche **Ausführen** , oder starten Sie die APP im Menü **Debuggen**

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:security/blazor/webassembly/additional-scenarios>
* [Nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen in einer APP mit einem sicheren Standard Client](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [Dokumentation zur Microsoft Identity Platform](/azure/active-directory/develop/)

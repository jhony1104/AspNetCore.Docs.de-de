---
title: Sichern einer ASP.net Core Blazor Webassembly-gehosteten App mit Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: b451df023bdea3e76725d5d1301f3c3f44ea5d99
ms.sourcegitcommit: 30fcf69556b6b6ec54a3879e280d5f61f018b48f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/07/2020
ms.locfileid: "82876204"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a>Sichern einer ASP.net Core Blazor Webassembly-gehosteten App mit Azure Active Directory B2C

Von [Javier calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

In diesem Artikel wird beschrieben, wie Blazor Sie eine eigenständige Webassembly-app erstellen, die [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) für die Authentifizierung verwendet.

## <a name="register-apps-in-aad-b2c-and-create-solution"></a>Registrieren von apps in Aad B2C und Erstellen einer Lösung

### <a name="create-a-tenant"></a>Erstellen eines Mandanten

Befolgen Sie die Anweisungen im [Tutorial: Erstellen eines Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) Mandanten, um einen Aad B2C-Mandanten zu erstellen, und notieren Sie die folgenden Informationen:

* Aad B2C Instanz (z. b `https://contoso.b2clogin.com/`., einschließlich des nachgestellten Schrägstrichs)
* Aad B2C Mandanten Domäne (z. b `contoso.onmicrosoft.com`.)

### <a name="register-a-server-api-app"></a>Registrieren einer Server-API-APP

Befolgen Sie die Anweisungen im [Tutorial: Registrieren einer Anwendung in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) , um eine Aad-App für die *Server-API-APP* im **Azure Active Directory** > **App-Registrierungen** Bereich der Azure-Portal zu registrieren:

1. Wählen Sie **Neue Registrierung** aus.
1. Geben Sie einen **Namen** für die APP an (z. b. ** Blazor Server Aad B2C**).
1. Wählen Sie für **unterstützte Konto Typen** **in einem beliebigen Organisations Verzeichnis oder einem beliebigen Identitäts Anbieter Konten aus. Zum Authentifizieren von Benutzern mit Azure AD B2C.** (mehr Instanzen fähig).
1. Für die *Server-API-APP* ist in diesem Szenario kein **Umleitungs-URI** erforderlich. lassen Sie die Dropdown-Datei also auf **Web** festgelegt und geben Sie keinen Umleitungs-URI
1. Vergewissern Sie sich **, dass die** > Berechtigungen**admin keine Zustimmung für OpenID und offline_access Berechtigungen gewähren** aktiviert ist.
1. Wählen Sie **Registrieren**.

In machen Sie **eine API**verfügbar:

1. Wählen Sie **Bereich hinzufügen**.
1. Wählen Sie **Speichern und fortfahren** aus.
1. Geben Sie einen **Bereichs Namen** an (z `API.Access`. b.).
1. Geben Sie einen **anzeigen Amen für die Administrator Zustimmung** an `Access API`(z. b.).
1. Geben Sie eine Beschreibung für die **Administrator Zustimmung** an `Allows the app to access server app API endpoints.`(z. b.).
1. Vergewissern Sie sich, dass der **Status** auf **aktiviert**festgelegt ist.
1. Wählen Sie **Bereich hinzufügen** aus.

Notieren Sie sich die folgenden Informationen:

* *Server-API-APP* Anwendungs-ID (Client-ID) (z `11111111-1111-1111-1111-111111111111`. b.)
* APP-ID-URI (z `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111`. b., oder der von Ihnen angegebene benutzerdefinierte Wert)
* Verzeichnis-ID (Mandanten-ID) (z `222222222-2222-2222-2222-222222222222`. b.)
* *Server-API-APP* APP-ID-URI (z `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`. b. wird der Azure-Portal den Wert möglicherweise als Standardwert für die Client-ID)
* Standardbereich (z. b `API.Access`.)

### <a name="register-a-client-app"></a>Registrieren einer Client-App

Befolgen Sie die Anweisungen [im Tutorial: Registrieren einer Anwendung in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) erneut, um eine Aad-App für die *Client-App* in der **Azure Active Directory** > **App-Registrierungen** Bereich der Azure-Portal zu registrieren:

1. Wählen Sie **Neue Registrierung** aus.
1. Geben Sie einen **Namen** für die APP an (z. b. ** Blazor Client Aad B2C**).
1. Wählen Sie für **unterstützte Konto Typen** **in einem beliebigen Organisations Verzeichnis oder einem beliebigen Identitäts Anbieter Konten aus. Zum Authentifizieren von Benutzern mit Azure AD B2C.** (mehr Instanzen fähig).
1. Belassen Sie den **Umleitungs-URI** -Dropdown auf **Web**, und geben Sie `https://localhost:5001/authentication/login-callback`einen Umleitungs-URI von an.
1. Vergewissern Sie sich **, dass die** > Berechtigungen**admin keine Zustimmung für OpenID und offline_access Berechtigungen gewähren** aktiviert ist.
1. Wählen Sie **Registrieren**.

Im**Web**der **Authentifizierungs** > **Platt Form Konfigurationen** > :

1. Bestätigen Sie, dass der `https://localhost:5001/authentication/login-callback` **Umleitungs-URI** von vorhanden ist
1. Aktivieren Sie für **implizite Gewährung**die Kontrollkästchen für **Zugriffs Token** und **ID-Token**.
1. Die verbleibenden Standardwerte für die APP sind für diese Art von akzeptabel.
1. Wählen Sie die Schaltfläche **Speichern** aus.

In **API-Berechtigungen**:

1. Vergewissern Sie sich, dass die APP über **Microsoft Graph** > **User. Read** -Berechtigung verfügt.
1. Wählen Sie **Berechtigung hinzufügen** und dann **meine APIs**aus.
1. Wählen Sie die *Server-API-APP* aus der Spalte **Name** (z. b. ** Blazor Server Aad B2C**) aus.
1. Öffnen Sie die **API** -Liste.
1. Aktivieren Sie den Zugriff auf die API (z `API.Access`. b.).
1. Wählen Sie **Berechtigungen hinzufügen** aus.
1. Wählen Sie die Schaltfläche **Administrator Inhalt für {TENANT Name} erteilen aus** . Klicken Sie zum Bestätigen auf **Ja**.

In der **Start** > **Azure AD B2C** > **benutzerflows**:

[Erstellen eines Benutzerflows für Registrierung und Anmeldung](/azure/active-directory-b2c/tutorial-create-user-flows)

Wählen Sie mindestens das Benutzer Attribut **Anwendungs Anspruchs** > **Anzeige Name** aus, um das `context.User.Identity.Name` in der `LoginDisplay` Komponente (*Shared/logindisplay. Razor*) aufzufüllen.

Notieren Sie sich die folgenden Informationen:

* Notieren Sie *Client app* sich `33333333-3333-3333-3333-333333333333`die Anwendungs-ID der Client-app (Client-ID) (z. b.).
* Zeichnen Sie den für die APP erstellten Registrierungs-und Anmeldedaten Flussnamen auf (z. b. `B2C_1_signupsignin`).

### <a name="create-the-app"></a>Erstellen der App

Ersetzen Sie die Platzhalter im folgenden Befehl durch die zuvor aufgezeichneten Informationen, und führen Sie den Befehl in einer Befehlsshell aus:

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

Um den Ausgabe Speicherort anzugeben, der einen Projektordner erstellt, wenn er nicht vorhanden ist, schließen Sie die Output-Option in den Befehl mit einem `-o BlazorSample`Pfad (z. b.) ein. Der Name des Ordners wird auch Teil des Projekt namens.

> [!NOTE]
> Übergeben Sie den APP-ID- `app-id-uri` URI an die-Option, aber beachten Sie, dass in der Client-App möglicherweise eine Konfigurationsänderung erforderlich ist, die im Abschnitt [Zugriffs Token-Bereiche](#access-token-scopes) beschrieben wird.

## <a name="server-app-configuration"></a>Server-App-Konfiguration

*Dieser Abschnitt bezieht sich auf die **Server** -App der Lösung.*

### <a name="authentication-package"></a>Authentifizierungs Paket

Die Unterstützung für das Authentifizieren und autorialisieren von Aufrufen von ASP.net Core Web-APIs `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`wird von bereitgestellt:

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
    Version="{VERSION}" />
```

### <a name="authentication-service-support"></a>Unterstützung für Authentifizierungsdienst

Die `AddAuthentication` -Methode richtet Authentifizierungsdienste innerhalb der APP ein und konfiguriert den JWT-bearerhandler als Standard Authentifizierungsmethode. Die `AddAzureADB2CBearer` -Methode richtet die spezifischen Parameter im JWT-bearerhandler ein, der zum Überprüfen der vom Azure Active Directory B2C ausgegebenen Token erforderlich ist:

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

`UseAuthentication`und `UseAuthorization` stellen Sie Folgendes sicher:

* Die APP versucht, Token bei eingehenden Anforderungen zu analysieren und zu überprüfen.
* Jede Anforderung, die versucht, auf eine geschützte Ressource ohne korrekte Anmelde Informationen zuzugreifen, schlägt fehl.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a>Bedienungs. Identity. Benennen

Standardmäßig wird `User.Identity.Name` nicht aufgefüllt.

Um die APP so zu konfigurieren, dass Sie den `name` Wert aus dem Anspruchstyp empfängt, konfigurieren Sie [tokenvalidationparameters. nameclaimtype](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) von <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:

```csharp
services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a>App-Einstellungen

Die Datei *appSettings. JSON* enthält die Optionen zum Konfigurieren des JWT-bearerhandlers, der zum Überprüfen von Zugriffs Token verwendet wird.

```json
{
  "AzureAdB2C": {
    "Instance": "https://{ORGANIZATION}.b2clogin.com/",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "Domain": "{DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

Beispiel:

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com/",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "Domain": "contoso.onmicrosoft.com",
    "SignUpSignInPolicyId": "B2C_1_signupsignin1",
  }
}
```

### <a name="weatherforecast-controller"></a>Weatherforecast-Controller

Der weatherforecast-Controller (*Controllers/weatherforecastcontroller. cs*) macht eine geschützte API verfügbar `[Authorize]` , wobei das-Attribut auf den Controller angewendet wird. Es ist **wichtig** , Folgendes zu verstehen:

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

Wenn eine APP für die Verwendung eines einzelnen B2C-Kontos (`IndividualB2C`) erstellt wird, erhält die APP automatisch einen Paket Verweis für die Microsoft-`Microsoft.Authentication.WebAssembly.Msal` [Authentifizierungs Bibliothek](/azure/active-directory/develop/msal-overview) (). Das Paket enthält eine Reihe von primitiven, die der APP helfen, Benutzer zu authentifizieren und Token abzurufen, um geschützte APIs aufzurufen.

Wenn Sie eine Authentifizierung zu einer APP hinzufügen, fügen Sie das Paket manuell zur Projektdatei der APP hinzu:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

Ersetzen `{VERSION}` Sie im vorherigen Paket Verweis durch die Version des `Microsoft.AspNetCore.Blazor.Templates` Pakets, das im <xref:blazor/get-started> Artikel angezeigt wird.

Das `Microsoft.Authentication.WebAssembly.Msal` Paket fügt das `Microsoft.AspNetCore.Components.WebAssembly.Authentication` Paket transitiv der APP hinzu.

### <a name="authentication-service-support"></a>Unterstützung für Authentifizierungsdienst

Es wird `HttpClient` Unterstützung für-Instanzen hinzugefügt, die Zugriffs Token enthalten, wenn Anforderungen an das Server Projekt gestellt werden.

*Program.cs*:

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

Die Unterstützung für das Authentifizieren von Benutzern wird im Dienst Container `AddMsalAuthentication` mit der vom `Microsoft.Authentication.WebAssembly.Msal` Paket bereitgestellten Erweiterungsmethode registriert. Diese Methode richtet alle Dienste ein, die für die Interaktion der APP mit dem Identity Anbieter (IP) erforderlich sind.

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

Die `AddMsalAuthentication` -Methode akzeptiert einen Rückruf, um die Parameter zu konfigurieren, die zum Authentifizieren einer APP erforderlich sind. Die für die Konfiguration der APP erforderlichen Werte können aus der Aad-Konfiguration des Azure-Portals abgerufen werden, wenn Sie die APP registrieren.

Die Konfiguration wird von der Datei " *wwwroot/appSettings. JSON* " bereitgestellt:

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

Beispiel:

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
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

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-ad-b2c>
* [Tutorial zum Erstellen eines Azure Active Directory B2C-Mandanten](/azure/active-directory-b2c/tutorial-create-tenant)
* [Dokumentation zur Microsoft Identity Platform](/azure/active-directory/develop/)

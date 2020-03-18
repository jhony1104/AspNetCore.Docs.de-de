---
title: Sichern eines ASP.net Core Blazor gehosteten Webassembly-App mit Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 2ddbc9791ec9b31d55c9c6017d9d6d5be5c8dec8
ms.sourcegitcommit: 5bdc54162d7dea8d9fa54ac3055678db23586af1
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/17/2020
ms.locfileid: "79434485"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory"></a>Sichern eines ASP.net Core Blazor gehosteten Webassembly-App mit Azure Active Directory

Von [Javier calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]



In diesem Artikel wird beschrieben, wie Sie eine [Blazor Webassembly-gehostete App](xref:blazor/hosting-models#blazor-webassembly) erstellen, die [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) für die Authentifizierung verwendet.

## <a name="register-apps-in-aad-b2c-and-create-solution"></a>Registrieren von apps in Aad B2C und Erstellen einer Lösung

### <a name="create-a-tenant"></a>Erstellen eines Mandanten

Befolgen Sie die Anweisungen unter [Schnellstart: Einrichten eines](/azure/active-directory/develop/quickstart-create-new-tenant) Mandanten zum Erstellen eines Mandanten in Aad.

### <a name="register-a-server-api-app"></a>Registrieren einer Server-API-APP

Befolgen Sie die Anleitungen unter [Schnellstart: Registrieren einer Anwendung mit der Microsoft Identity Platform](/azure/active-directory/develop/quickstart-register-app) und nachfolgende Azure Aad-Themen zum Registrieren einer Aad-App für die *Server-API-APP* im Bereich **Azure Active Directory** > **App-Registrierungen** des Azure-Portal:

1. Wählen Sie **Neue Registrierung** aus.
1. Geben Sie einen **Namen** für die APP an (z. b. **Blazor Server Aad**).
1. Wählen Sie einen **unterstützten Kontotyp**aus. Für diese Vorgehens Art können Sie **nur Konten in diesem Organisations Verzeichnis** auswählen (einzelner Mandant).
1. Für die *Server-API-APP* ist in diesem Szenario kein **Umleitungs-URI** erforderlich. lassen Sie die Dropdown-Datei also auf **Web** festgelegt und geben Sie keinen Umleitungs-URI
1. Deaktivieren Sie das Kontrollkästchen **Berechtigungen** > **erteilen Sie Administrator Berechtigungen für OpenID und offline_access Berechtigungen** .
1. Wählen Sie **Registrieren**.

Entfernen Sie unter **API-Berechtigungen**die **Microsoft Graph** > **User. Read** -Berechtigung, da die APP keinen Anmelde-oder uerprofilzugriff erfordert.

In machen Sie **eine API**verfügbar:

1. Wählen Sie **Bereich hinzufügen**.
1. Wählen Sie **Speichern und fortfahren** aus.
1. Geben Sie einen **Bereichs Namen** an (z. b. `API.Access`).
1. Geben Sie einen **anzeigen Amen für die Administrator Zustimmung** an (z. b. `Access API`).
1. Geben Sie eine Beschreibung für die **Administrator Zustimmung** an (z. b. `Allows the app to access server app API endpoints.`).
1. Vergewissern Sie sich, dass der **Status** auf **aktiviert**festgelegt ist.
1. Wählen Sie **Bereich hinzufügen** aus.

Notieren Sie sich die folgenden Informationen:

* *Server-API-APP* Anwendungs-ID (Client-ID) (z. b. `11111111-1111-1111-1111-111111111111`)
* Verzeichnis-ID (Mandanten-ID) (z. b. `222222222-2222-2222-2222-222222222222`)
* Aad-Mandanten Domäne (z. b. `contoso.onmicrosoft.com`)
* Standardbereich (z. b. `API.Access`)

### <a name="register-a-client-app"></a>Registrieren einer Client-App

Befolgen Sie die Anleitungen unter [Schnellstart: Registrieren einer Anwendung mit der Microsoft Identity Platform](/azure/active-directory/develop/quickstart-register-app) und nachfolgende Azure Aad-Themen zum Registrieren einer Aad-App für die *Client-App* in der **Azure Active Directory** > **App-Registrierungen** Bereich der Azure-Portal:

1. Wählen Sie **Neue Registrierung** aus.
1. Geben Sie einen **Namen** für die APP an (z. b. **Blazor Aad-Client**).
1. Wählen Sie einen **unterstützten Kontotyp**aus. Für diese Vorgehens Art können Sie **nur Konten in diesem Organisations Verzeichnis** auswählen (einzelner Mandant).
1. Lassen Sie die Dropdown-Einstellung für **Umleitungs-URI** auf **Web**und einen Umleitungs-URI `https://localhost:5001/authentication/login-callback`.
1. Deaktivieren Sie das Kontrollkästchen **Berechtigungen** > **erteilen Sie Administrator Berechtigungen für OpenID und offline_access Berechtigungen** .
1. Wählen Sie **Registrieren**.

In der **Authentifizierung** > **Platt Form Konfigurationen** > **Web**:

1. Vergewissern Sie sich, dass der **Umleitungs-URI** `https://localhost:5001/authentication/login-callback` vorhanden ist.
1. Aktivieren Sie für **implizite Gewährung**die Kontrollkästchen für **Zugriffs Token** und **ID-Token**.
1. Die verbleibenden Standardwerte für die APP sind für diese Art von akzeptabel.
1. Wählen Sie die Schaltfläche **Speichern** aus.

In **API-Berechtigungen**:

1. Vergewissern Sie sich, dass die APP über **Microsoft Graph** > **User. Read** -Berechtigung verfügt.
1. Wählen Sie **Berechtigung hinzufügen** und dann **meine APIs**aus.
1. Wählen Sie die *Server-API-APP* aus der Spalte **Name** (z. b. **Blazor Server Aad**) aus.
1. Öffnen Sie die **API** -Liste.
1. Aktivieren Sie den Zugriff auf die API (z. b. `API.Access`).
1. Wählen Sie **Berechtigungen hinzufügen** aus.
1. Wählen Sie die Schaltfläche **Administrator Inhalt für {TENANT Name} erteilen aus** . Klicken Sie auf **Ja**, um zu bestätigen.

Notieren Sie die Anwendungs-ID der *Client* Anwendung (Client-ID) (z. b. `33333333-3333-3333-3333-333333333333`).

### <a name="create-the-app"></a>Erstellen der App

Ersetzen Sie die Platzhalter im folgenden Befehl durch die zuvor aufgezeichneten Informationen, und führen Sie den Befehl in einer Befehlsshell aus:

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP CLIENT ID}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

Um den Ausgabe Speicherort anzugeben, der einen Projektordner erstellt, wenn er nicht vorhanden ist, fügen Sie die Output-Option im Befehl mit einem Pfad (z. b. `-o BlazorSample`) ein. Der Name des Ordners wird auch Teil des Projekt namens.

> [!NOTE]
> Eine wichtige Konfigurationsänderung am Standard zugriffstokenbereich finden Sie im Abschnitt [Unterstützung des Authentifizierungs Diensts](#Authentication service support) . Der von der Vorlage Blazor Webassembly bereitgestellte Wert muss nach dem Erstellen der *Client-App* aus der Vorlage manuell geändert werden.

## <a name="server-app-configuration"></a>Server-App-Konfiguration

*Dieser Abschnitt bezieht sich auf die **Server** -App der Lösung.*

### <a name="authentication-package"></a>Authentifizierungs Paket

Die Unterstützung für die Authentifizierung und Autorisierung von Aufrufen von ASP.net Core Web-APIs wird vom `Microsoft.AspNetCore.Authentication.AzureAD.UI`bereitgestellt:

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a>Unterstützung für Authentifizierungsdienst

Die `AddAuthentication`-Methode richtet Authentifizierungsdienste innerhalb der APP ein und konfiguriert den JWT-bearerhandler als Standard Authentifizierungsmethode. Die `AddAzureADBearer`-Methode richtet die spezifischen Parameter im JWT-bearerhandler ein, der zum Überprüfen der vom Azure Active Directory ausgegebenen Token erforderlich ist:

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

`UseAuthentication` und `UseAuthorization` Folgendes sicherstellen:

* Die APP versucht, Token bei eingehenden Anforderungen zu analysieren und zu überprüfen.
* Jede Anforderung, die versucht, auf eine geschützte Ressource ohne korrekte Anmelde Informationen zuzugreifen, schlägt fehl.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="app-settings"></a>App-Einstellungen

Die Datei *appSettings. JSON* enthält die Optionen zum Konfigurieren des JWT-bearerhandlers, der zum Überprüfen von Zugriffs Token verwendet wird.

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{API CLIENT ID}",
  }
}
```

### <a name="weatherforecast-controller"></a>Weatherforecast-Controller

Der weatherforecast-Controller (*Controllers/weatherforecastcontroller. cs*) macht eine geschützte API verfügbar, wobei das `[Authorize]`-Attribut auf den Controller angewendet wird. Es ist **wichtig** , Folgendes zu verstehen:

* Das `[Authorize]`-Attribut in diesem API-Controller ist die einzige Möglichkeit, diese API vor nicht autorisiertem Zugriff zu schützen.
* Das `[Authorize]`-Attribut, das in der Blazor Webassembly-App verwendet wird, dient nur als Hinweis für die APP, dass der Benutzer autorisiert werden muss, damit die APP ordnungsgemäß funktioniert.

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

Wenn eine APP für die Verwendung von Geschäfts-, Schul-oder unikonten (`SingleOrg`) erstellt wird, erhält die APP automatisch einen Paket Verweis für die [Microsoft-Authentifizierungs Bibliothek](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`). Das Paket enthält eine Reihe von primitiven, die der APP helfen, Benutzer zu authentifizieren und Token abzurufen, um geschützte APIs aufzurufen.

Wenn Sie eine Authentifizierung zu einer APP hinzufügen, fügen Sie das Paket manuell zur Projektdatei der APP hinzu:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

Ersetzen Sie `{VERSION}` im vorangehenden Paket Verweis durch die Version des `Microsoft.AspNetCore.Blazor.Templates` Pakets, das im <xref:blazor/get-started> Artikel angezeigt wird.

Das `Microsoft.Authentication.WebAssembly.Msal` Paket fügt das `Microsoft.AspNetCore.Components.WebAssembly.Authentication` Paket transitiv der APP hinzu.

### <a name="authentication-service-support"></a>Unterstützung für Authentifizierungsdienst

Die Unterstützung für das Authentifizieren von Benutzern wird im Dienst Container mit der `AddMsalAuthentication`-Erweiterungsmethode registriert, die vom `Microsoft.Authentication.WebAssembly.Msal`-Paket bereitgestellt wird. Diese Methode richtet alle Dienste ein, die erforderlich sind, damit die APP mit dem Identitäts Anbieter (IP) interagieren muss.

*Program.cs*:

Wenn die *Client-App* generiert wird, hat der standardzugriffstokenbereich das Format `api://{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}`. **Entfernen Sie den `api://` Teil des Bereichs Werts.** Dieses Problem wird in einer zukünftigen Vorschauversion behandelt.

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}");
});
```

> [!NOTE]
> Der standardzugriffstokenbereich muss im Format `{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}` sein (z. b. `11111111-1111-1111-1111-111111111111/API.Access`). Wenn für die Bereichseinstellung ein Schema oder ein Schema und ein Host bereitgestellt wird (wie im Azure-Portal gezeigt), löst die *Client-App* eine nicht behandelte Ausnahme aus, wenn Sie eine 401-nicht *autorisierte* Antwort von der Server- *API-APP*empfängt.

Die `AddMsalAuthentication`-Methode akzeptiert einen Rückruf, um die Parameter zu konfigurieren, die zum Authentifizieren einer APP erforderlich sind. Die für die Konfiguration der APP erforderlichen Werte können aus der Aad-Konfiguration des Azure-Portals abgerufen werden, wenn Sie die APP registrieren.

Die standardzugriffstokenbereiche stellen die Liste der zugriffstokenbereiche dar, die lauten:

* Standardmäßig in der Anmelde Anforderung enthalten.
* Wird verwendet, um unmittelbar nach der Authentifizierung ein Zugriffs Token bereitzustellen.

Alle Bereiche müssen pro Azure Active Directory Regeln zur gleichen App gehören. Für zusätzliche API-Apps können nach Bedarf weitere Bereiche hinzugefügt werden:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{SERVER API APP CLIENT ID}/{SCOPE}");
});
```

### <a name="index-page"></a>Indexseite

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

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

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:security/authentication/azure-active-directory/index>

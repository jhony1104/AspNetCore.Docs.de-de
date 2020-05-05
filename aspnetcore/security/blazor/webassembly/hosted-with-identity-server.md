---
title: Sichern einer ASP.net Core Blazor Webassembly-gehosteten Identity App mit dem Server
author: guardrex
description: So erstellen Sie eine Blazor neue gehostete App mit Authentifizierung aus Visual Studio, die ein [identityserver](https://identityserver.io/) -Backend verwendet
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
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: bf2298618e922df412e0742177afd390c4116388
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768121"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a>Sichern einer ASP.net Core Blazor Webassembly-gehosteten Identity App mit dem Server

Von [Javier calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

So erstellen Sie eine Blazor neue gehostete app in Visual Studio, die [identityserver](https://identityserver.io/) verwendet, um Benutzer und API-Aufrufe zu authentifizieren:

1. Verwenden Sie Visual Studio, um eine neue ** Blazor Webassembly** -APP zu erstellen. Weitere Informationen finden Sie unter <xref:blazor/get-started>.
1. Wählen Sie im Dialogfeld **neue Blazor app erstellen** im Abschnitt **Authentifizierung** die Option **ändern** aus.
1. Wählen Sie **einzelne Benutzerkonten** und dann **OK**aus.
1. Aktivieren Sie das Kontrollkästchen **ASP.net Core gehostet** im Abschnitt **erweitert** .
1. Wählen Sie die Schaltfläche **Erstellen**.

Führen Sie den folgenden Befehl aus, um die app in einer Befehlsshell zu erstellen:

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

Um den Ausgabe Speicherort anzugeben, der einen Projektordner erstellt, wenn er nicht vorhanden ist, schließen Sie die Output-Option in den Befehl mit einem `-o BlazorSample`Pfad (z. b.) ein. Der Name des Ordners wird auch Teil des Projekt namens.

## <a name="server-app-configuration"></a>Server-App-Konfiguration

In den folgenden Abschnitten werden Ergänzungen zum-Projekt beschrieben, wenn die Authentifizierungs Unterstützung enthalten ist.

### <a name="startup-class"></a>Startklasse

Die `Startup` -Klasse verfügt über die folgenden Ergänzungen:

* In `Startup.ConfigureServices`:

  * Identity:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * Identityserver mit einer zusätzlichen <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> Hilfsmethode, mit der einige Standard ASP.net Core Konventionen auf identityserver eingerichtet werden:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Authentifizierung mit einer zusätzlichen <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> Hilfsmethode, die die APP für die Validierung der von identityserver erstellten JWT-Token konfiguriert:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* In `Startup.Configure`:

  * Die Authentifizierungs Middleware, die für die Validierung der Anforderungs Anmelde Informationen und das Festlegen des Benutzers im Anforderungs Kontext zuständig ist:

    ```csharp
    app.UseAuthentication();
    ```

  * Die identityserver-Middleware, die die Endpunkte der Open ID Connect (oidc) verfügbar macht:

    ```csharp
    app.UseIdentityServer();
    ```

  * Authentifizierungs-und Autorisierungs Middleware:

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a>Addapiauthorization

Die <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> -Hilfsmethode konfiguriert [identityserver](https://identityserver.io/) für ASP.net Core Szenarien. Identityserver ist ein leistungsfähiges und erweiterbares Framework für die Behandlung von App-Sicherheitsbedenken. Identityserver macht unnötige Komplexität für die häufigsten Szenarien verfügbar. Folglich wird ein Satz von Konventionen und Konfigurationsoptionen bereitgestellt, die einen guten Ausgangspunkt in Erwägung gezogen. Nachdem sich Ihre Authentifizierung geändert hat, ist die volle Leistungsfähigkeit von identityserver weiterhin verfügbar, um die Authentifizierung an die Anforderungen einer APP anzupassen.

### <a name="addidentityserverjwt"></a>Addidentityserverjwt

Die <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> -Hilfsmethode konfiguriert ein Richtlinien Schema für die APP als Standard Authentifizierungs Handler. Die Richtlinie ist so konfiguriert Identity , dass Sie alle Anforderungen verarbeiten kann, die Identity an einen untergeordneten `/Identity`Pfad im URL-Bereich weitergeleitet werden. Der <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> verarbeitet alle anderen Anforderungen. Außerdem gilt diese Methode:

* Registriert eine `{APPLICATION NAME}API` API-Ressource bei identityserver mit einem Standardbereich `{APPLICATION NAME}API`von.
* Konfiguriert die JWT-bearertoken-Middleware zum Überprüfen von Token, die von identityserver für die APP ausgegeben werden.

### <a name="weatherforecastcontroller"></a>Weatherforecastcontroller

In `WeatherForecastController` (*Controllers/weatherforecastcontroller. cs*) wird das [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) -Attribut auf die-Klasse angewendet. Das-Attribut gibt an, dass der Benutzer auf der Basis der Standard Richtlinie autorisiert werden muss, um auf die Ressource zuzugreifen. Die standardmäßige Autorisierungs Richtlinie ist so konfiguriert, dass das Standard Authentifizierungsschema verwendet wird, <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> das durch das zuvor erwähnte Richtlinien Schema festgelegt wird. Die-Hilfsmethode konfiguriert <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> als Standard Handler für Anforderungen an die app.

### <a name="applicationdbcontext"></a>ApplicationDbContext

In der `ApplicationDbContext` (*Data/applicationdbcontext. cs*) wird die gleiche <xref:Microsoft.EntityFrameworkCore.DbContext> in Identity mit der Ausnahme verwendet, die Sie erweitert <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> , um das Schema für identityserver einzuschließen. <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> wird von <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> abgeleitet.

Um die vollständige Kontrolle über das Datenbankschema zu erhalten, erben Sie Identity <xref:Microsoft.EntityFrameworkCore.DbContext> von einer der verfügbaren Klassen und konfigurieren Identity den Kontext so `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` , dass `OnModelCreating` das Schema durch Aufrufen von in der-Methode eingeschlossen wird.

### <a name="oidcconfigurationcontroller"></a>Oidcconfigurationcontroller

In der `OidcConfigurationController` (*Controllers/oidcconfigurationcontroller. cs*) wird der Client Endpunkt bereitgestellt, um oidc-Parameter bereitzustellen.

### <a name="app-settings-files"></a>App-Einstellungsdateien

In der APP-Einstellungsdatei (*appSettings. JSON*) im Projektstamm Abschnitt wird `IdentityServer` in diesem Abschnitt die Liste der konfigurierten Clients beschrieben. Im folgenden Beispiel gibt es einen einzelnen Client. Der Client Name entspricht dem APP-Namen und wird dem OAuth `ClientId` -Parameter gemäß der Konvention zugeordnet. Das Profil gibt den App-Typ an, der konfiguriert wird. Das Profil wird intern verwendet, um Konventionen zu steuern, die den Konfigurationsprozess für den Server vereinfachen. <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

## <a name="client-app-configuration"></a>Client-App-Konfiguration

### <a name="authentication-package"></a>Authentifizierungs Paket

Wenn eine APP für die Verwendung einzelner Benutzerkonten (`Individual`) erstellt wird, erhält die APP automatisch einen Paket Verweis für `Microsoft.AspNetCore.Components.WebAssembly.Authentication` das Paket in der Projektdatei der app. Das Paket enthält eine Reihe von primitiven, die der APP helfen, Benutzer zu authentifizieren und Token abzurufen, um geschützte APIs aufzurufen.

Wenn Sie eine Authentifizierung zu einer APP hinzufügen, fügen Sie das Paket manuell zur Projektdatei der APP hinzu:

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

Ersetzen `{VERSION}` Sie im vorherigen Paket Verweis durch die Version des `Microsoft.AspNetCore.Blazor.Templates` Pakets, das im <xref:blazor/get-started> Artikel angezeigt wird.

### <a name="api-authorization-support"></a>API-Autorisierungs Unterstützung

Die Unterstützung für das Authentifizieren von Benutzern wird durch die im `Microsoft.AspNetCore.Components.WebAssembly.Authentication` Paket bereitgestellte Erweiterungsmethode an den Dienst Container angeschlossen. Diese Methode richtet alle Dienste ein, die erforderlich sind, damit die APP mit dem vorhandenen Autorisierungssystem interagiert.

```csharp
builder.Services.AddApiAuthorization();
```

Standardmäßig wird die Konfiguration für die APP gemäß der Konvention von `_configuration/{client-id}`geladen. Gemäß der Konvention wird die Client-ID auf den Assemblynamen der APP festgelegt. Diese URL kann geändert werden, um auf einen separaten Endpunkt zu verweisen, indem Sie die-Überladung mit-Optionen aufrufen.

### <a name="imports-file"></a>Import Datei

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Indexseite

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a>App-Komponente

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>Redirecttologin-Komponente

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Logindisplay-Komponente

Die `LoginDisplay` Komponente (*Shared/logindisplay. Razor*) wird in der `MainLayout` Komponente (*Shared/MainLayout. Razor*) gerendert und verwaltet die folgenden Verhaltensweisen:

* Für authentifizierte Benutzer:
  * Zeigt den aktuellen Benutzernamen an.
  * Bietet einen Link zur Benutzerprofil Seite in ASP.net Core Identity.
  * Bietet eine Schaltfläche, um sich von der APP abzumelden.
* Für anonyme Benutzer:
  * Bietet die Option zum Registrieren von.
  * Bietet die Option zum Anmelden.

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

### <a name="authentication-component"></a>Authentifizierungs Komponente

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>Fetchdata-Komponente

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>Ausführen der App

Führen Sie die APP aus dem Server Projekt aus. Wenn Sie Visual Studio verwenden, wählen Sie das Server Projekt in **Projektmappen-Explorer** aus, und klicken Sie auf der Symbolleiste auf die Schaltfläche **Ausführen** , oder starten Sie die APP im Menü **Debuggen**

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:security/blazor/webassembly/additional-scenarios>

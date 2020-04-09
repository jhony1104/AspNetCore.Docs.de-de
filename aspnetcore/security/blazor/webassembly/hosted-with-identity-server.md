---
title: Sichern einer Blazor ASP.NET gehosteten Core WebAssembly-App mit Identity Server
author: guardrex
description: So erstellen Blazor Sie eine neue gehostete App mit Authentifizierung aus Visual Studio, die ein [IdentityServer-Backend](https://identityserver.io/) verwendet
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: 832109530c4aac372fd75aa1a1d2edbe3768f55f
ms.sourcegitcommit: 1d8f1396ccc66a0c3fcb5e5f36ea29b50db6d92a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501280"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a>Sichern einer Blazor ASP.NET gehosteten Core WebAssembly-App mit Identity Server

Von [Javier Calvarro Nelson](https://github.com/javiercn) und Luke [Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

So erstellen Blazor Sie eine neue gehostete App in Visual Studio, die [IdentityServer](https://identityserver.io/) zum Authentifizieren von Benutzern und API-Aufrufen verwendet:

1. Verwenden Sie Visual Studio, um eine neue ** Blazor WebAssembly-App** zu erstellen. Weitere Informationen finden Sie unter <xref:blazor/get-started>.
1. Wählen Sie im Dialogfeld **Erstellen einer neuen Blazor App** im Abschnitt **Authentifizierung** die Option **Ändern** aus.
1. Wählen Sie **Einzelne Benutzerkonten** gefolgt von **OK**aus.
1. Aktivieren Sie das Kontrollkästchen **ASP.NET gehosteten Core** im Abschnitt **Erweitert.**
1. Wählen Sie die Schaltfläche **Erstellen**.

Um die App in einer Befehlsshell zu erstellen, führen Sie den folgenden Befehl aus:

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

Um den Ausgabespeicherort anzugeben, der einen Projektordner erstellt, falls dieser nicht vorhanden ist, fügen `-o BlazorSample`Sie die Ausgabeoption in den Befehl mit einem Pfad ein (z. B. ). Der Ordnername wird auch Teil des Projektnamens.

## <a name="server-app-configuration"></a>Server-App-Konfiguration

In den folgenden Abschnitten werden Ergänzungen zum Projekt beschrieben, wenn authentifizierungsunterstützung enthalten ist.

### <a name="startup-class"></a>Startklasse

Die `Startup` Klasse hat die folgenden Ergänzungen:

* In `Startup.ConfigureServices`:

  * Identität mit der Standardbenutzeroberfläche:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer mit <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> einer zusätzlichen Hilfsmethode, die einige Standard-ASP.NET Core-Konventionen über IdentityServer einrichtet:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Authentifizierung mit <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> einer zusätzlichen Hilfsmethode, die die App so konfiguriert, dass JWT-Token überprüft werden, die von IdentityServer erstellt wurden:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* In `Startup.Configure`:

  * Die Authentifizierungsmiddleware, die für die Überprüfung der Anforderungsanmeldeinformationen und das Festlegen des Benutzers für den Anforderungskontext zuständig ist:

    ```csharp
    app.UseAuthentication();
    ```

  * Die IdentityServer-Middleware, die die OIDC-Endpunkte (Open ID Connect) verfügbar macht:

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

Die <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> Hilfsmethode konfiguriert [IdentityServer](https://identityserver.io/) für ASP.NET Core-Szenarien. IdentityServer ist ein leistungsstarkes und erweiterbares Framework für die Behandlung von Sicherheitsbedenken von Apps. IdentityServer macht unnötige Komplexität für die häufigsten Szenarien verfügbar. Folglich wird eine Reihe von Konventionen und Konfigurationsoptionen bereitgestellt, die wir als einen guten Ausgangspunkt betrachten. Sobald sich Ihre Authentifizierungsanforderungen ändern, ist IdentityServer weiterhin mit voller Leistungsfähigkeit verfügbar, um die Authentifizierung an die Anforderungen einer App anzupassen.

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

Die <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> Hilfsmethode konfiguriert ein Richtlinienschema für die App als Standardauthentifizierungshandler. Die Richtlinie ist so konfiguriert, dass Identity alle Anforderungen verarbeiten kann, `/Identity`die an einen beliebigen Unterpfad im Identitäts-URL-Bereich weitergeleitet werden. Der <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> behandelt alle anderen Anforderungen. Darüber hinaus ist diese Methode:

* Registriert eine `{APPLICATION NAME}API` API-Ressource bei IdentityServer `{APPLICATION NAME}API`mit dem Standardbereich von .
* Konfiguriert die JWT Bearer Token Middleware, um Token zu überprüfen, die von IdentityServer für die App ausgegeben wurden.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

In `WeatherForecastController` der (*Controllers/WeatherForecastController.cs*) wird das [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) Attribut auf die Klasse angewendet. Das Attribut gibt an, dass der Benutzer basierend auf der Standardrichtlinie autorisiert werden muss, um auf die Ressource zuzugreifen. Die Standardautorisierungsrichtlinie ist für die Verwendung des Standardauthentifizierungsschemas konfiguriert, das für <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> das zuvor erwähnte Richtlinienschema eingerichtet wird. Die Hilfsmethode konfiguriert <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> sich als Standardhandler für Anforderungen an die App.

### <a name="applicationdbcontext"></a>ApplicationDbContext

In `ApplicationDbContext` der (*Data/ApplicationDbContext.cs*) wird dasselbe <xref:Microsoft.EntityFrameworkCore.DbContext> in Identity <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> verwendet, mit der Ausnahme, dass sie sich auf das Schema für IdentityServer erstreckt. <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> wird von <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> abgeleitet.

Um die vollständige Kontrolle über das Datenbankschema zu erhalten, erben Sie von einer der verfügbaren Identitätsklassen, <xref:Microsoft.EntityFrameworkCore.DbContext> und konfigurieren Sie den Kontext so, dass das Identitätsschema durch Aufrufen der `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` `OnModelCreating` Methode eingeschlossen wird.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

Im `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*) wird der Clientendpunkt bereitgestellt, um OIDC-Parameter zu bedienen.

### <a name="app-settings-files"></a>App-Einstellungsdateien

In der App-Einstellungsdatei (*appsettings.json* `IdentityServer` ) am Projektstamm wird im Abschnitt die Liste der konfigurierten Clients beschrieben. Im folgenden Beispiel gibt es einen einzelnen Client. Der Clientname entspricht dem App-Namen und wird durch `ClientId` Konvention dem OAuth-Parameter zugeordnet. Das Profil gibt den konfigurierten App-Typ an. Das Profil wird intern verwendet, um Konventionen zu unterstützen, die den Konfigurationsprozess für den Server vereinfachen. <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "BlazorApplicationWithAuthentication.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

In der App-Einstellungsdatei für die Entwicklungsumgebung *(Appsettings. Development.json*) im Projektstamm `IdentityServer` beschreibt der Abschnitt den Schlüssel, der zum Signieren von Token verwendet wird. <!-- When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section. -->

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="client-app-configuration"></a>Client-App-Konfiguration

### <a name="authentication-package"></a>Authentifizierungspaket

Wenn eine App erstellt wird,`Individual`um einzelne Benutzerkonten ( zu `Microsoft.AspNetCore.Components.WebAssembly.Authentication` verwenden), erhält die App automatisch eine Paketreferenz für das Paket in der Projektdatei der App. Das Paket stellt eine Reihe von Primitiven bereit, die der App helfen, Benutzer zu authentifizieren und Token zum Aufrufen geschützter APIs zu erhalten.

Wenn Sie einer App eine Authentifizierung hinzufügen, fügen Sie das Paket manuell zur Projektdatei der App hinzu:

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

Ersetzen `{VERSION}` Sie im vorherigen Paketverweis `Microsoft.AspNetCore.Blazor.Templates` durch die <xref:blazor/get-started> im Artikel gezeigte Version des Pakets.

### <a name="api-authorization-support"></a>UNTERSTÜTZUNG für API-Autorisierungen

Die Unterstützung für die Authentifizierung von Benutzern wird durch die `Microsoft.AspNetCore.Components.WebAssembly.Authentication` im Paket bereitgestellte Erweiterungsmethode an den Dienstcontainer angeschlossen. Diese Methode richtet alle Dienste ein, die für die App erforderlich sind, um mit dem vorhandenen Autorisierungssystem zu interagieren.

```csharp
builder.Services.AddApiAuthorization();
```

Standardmäßig wird die Konfiguration für die App `_configuration/{client-id}`nach Konvention aus geladen. Konventionell wird die Client-ID auf den Assemblynamen der App festgelegt. Diese URL kann geändert werden, um auf einen separaten Endpunkt zu verweisen, indem die Überladung mit Optionen aufgerufen wird.

### <a name="imports-file"></a>Imports-Datei

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Indexseite

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a>App-Komponente

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>RedirectToLogin-Komponente

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>LoginDisplay-Komponente

Die `LoginDisplay` Komponente (*Shared/LoginDisplay.razor*) `MainLayout` wird in der Komponente (*Shared/MainLayout.razor*) gerendert und verwaltet die folgenden Verhaltensweisen:

* Für authentifizierte Benutzer:
  * Zeigt den aktuellen Benutzernamen an.
  * Bietet einen Link zur Benutzerprofilseite in ASP.NET Core Identity.
  * Bietet eine Schaltfläche zum Abmelden aus der App.
* Für anonyme Benutzer:
  * Bietet die Möglichkeit, sich zu registrieren.
  * Bietet die Möglichkeit, sich anzumelden.

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

### <a name="authentication-component"></a>Authentifizierungskomponente

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>FetchData-Komponente

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>Ausführen der App

Führen Sie die App aus dem Serverprojekt aus. Wenn Sie Visual Studio verwenden, wählen Sie das Serverprojekt im **Projektmappen-Explorer** aus, und wählen Sie die Schaltfläche **Ausführen** in der Symbolleiste aus, oder starten Sie die App im **Menü Debug.**

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Zusätzliche Zugriffstoken anfordern](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)

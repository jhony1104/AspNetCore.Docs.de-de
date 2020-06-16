---
title: Sichern einer ASP.net Core Blazor Webassembly-gehosteten App mit dem Identity Server
author: guardrex
description: So erstellen Sie eine neue Blazor gehostete App mit Authentifizierung aus Visual Studio, die ein [identityserver](https://identityserver.io/) -Backend verwendet
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
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: c85843c04688beefe7ea87d9e8b281d14ab85bc5
ms.sourcegitcommit: b0062f29cba2e5c21b95cf89eaf435ba830d11a3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/15/2020
ms.locfileid: "84776513"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a>Sichern einer ASP.net Core Blazor Webassembly-gehosteten App mit dem Identity Server

Von [Javier calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)

In diesem Artikel wird erläutert, wie Sie eine neue Blazor gehostete app erstellen, die [identityserver](https://identityserver.io/) verwendet, um Benutzer und API-Aufrufe zu authentifizieren.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

In Visual Studio:

1. Erstellen Sie eine neue ** Blazor Webassembly** -app. Weitere Informationen finden Sie unter <xref:blazor/get-started>.
1. Wählen Sie im Dialogfeld **neue Blazor app erstellen** im Abschnitt **Authentifizierung** die Option **ändern** aus.
1. Wählen Sie **einzelne Benutzerkonten** und dann **OK**aus.
1. Aktivieren Sie das Kontrollkästchen **ASP.net Core gehostet** im Abschnitt **erweitert** .
1. Wählen Sie die Schaltfläche **Erstellen**.

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli/)

Führen Sie den folgenden Befehl aus, um die app in einer Befehlsshell zu erstellen:

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

Um den Ausgabe Speicherort anzugeben, der einen Projektordner erstellt, wenn er nicht vorhanden ist, schließen Sie die Output-Option in den Befehl mit einem Pfad (z `-o BlazorSample` . b.) ein. Der Name des Ordners wird auch Teil des Projekt namens.

---

## <a name="server-app-configuration"></a>Server-App-Konfiguration

In den folgenden Abschnitten werden Ergänzungen zum-Projekt beschrieben, wenn die Authentifizierungs Unterstützung enthalten ist.

### <a name="startup-class"></a>Startklasse

Die- `Startup` Klasse verfügt über die folgenden Ergänzungen.

* In `Startup.ConfigureServices`:

  * ASP.net Core Identity :

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * Identityserver mit einer zusätzlichen <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> Hilfsmethode, die Standard-ASP.net Core Konventionen zusätzlich zu identityserver festlegt:

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

  * Die identityserver-Middleware macht die Endpunkte der Open ID Connect (oidc) verfügbar:

    ```csharp
    app.UseIdentityServer();
    ```

  * Die Authentifizierungs Middleware ist verantwortlich für das Überprüfen von Anforderungs Anmelde Informationen und das Festlegen des Benutzers für den Anforderungs Kontext:

    ```csharp
    app.UseAuthentication();
    ```

  * Autorisierungs Middleware ermöglicht Autorisierungs Funktionen:

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a>Addapiauthorization

Die- <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> Hilfsmethode konfiguriert [identityserver](https://identityserver.io/) für ASP.net Core Szenarien. Identityserver ist ein leistungsfähiges und erweiterbares Framework für die Behandlung von App-Sicherheitsbedenken. Identityserver macht unnötige Komplexität für die häufigsten Szenarien verfügbar. Folglich wird ein Satz von Konventionen und Konfigurationsoptionen bereitgestellt, die einen guten Ausgangspunkt in Erwägung gezogen. Nachdem sich Ihre Authentifizierung geändert hat, steht die volle Leistungsfähigkeit von identityserver zur Verfügung, um die Authentifizierung an die Anforderungen einer APP anzupassen.

### <a name="addidentityserverjwt"></a>Addidentityserverjwt

Die- <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> Hilfsmethode konfiguriert ein Richtlinien Schema für die APP als Standard Authentifizierungs Handler. Die Richtlinie ist so konfiguriert, dass Identity Sie alle Anforderungen verarbeiten kann, die an einen untergeordneten Pfad im URL-Bereich weitergeleitet werden Identity `/Identity` . Der <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> verarbeitet alle anderen Anforderungen. Außerdem gilt diese Methode:

* Registriert eine `{APPLICATION NAME}API` API-Ressource bei identityserver mit einem Standardbereich von `{APPLICATION NAME}API` .
* Konfiguriert die JWT-bearertoken-Middleware zum Überprüfen von Token, die von identityserver für die APP ausgegeben werden.

### <a name="weatherforecastcontroller"></a>Weatherforecastcontroller

In `WeatherForecastController` (*Controllers/weatherforecastcontroller. cs*) wird das- [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) Attribut auf die-Klasse angewendet. Das-Attribut gibt an, dass der Benutzer auf der Basis der Standard Richtlinie autorisiert werden muss, um auf die Ressource zuzugreifen. Die standardmäßige Autorisierungs Richtlinie ist so konfiguriert, dass das Standard Authentifizierungsschema verwendet wird, das von festgelegt wird <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> . Die-Hilfsmethode konfiguriert <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> als Standard Handler für Anforderungen an die app.

### <a name="applicationdbcontext"></a>ApplicationDbContext

Erweitert in `ApplicationDbContext` (*Data/applicationdbcontext. cs*), <xref:Microsoft.EntityFrameworkCore.DbContext> <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> um das Schema für identityserver einzuschließen. <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> wird von <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> abgeleitet.

Um die vollständige Kontrolle über das Datenbankschema zu erhalten, erben Sie von einer der verfügbaren Identity <xref:Microsoft.EntityFrameworkCore.DbContext> Klassen und konfigurieren den Kontext so, dass das Identity Schema durch Aufrufen von `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in der- <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> Methode eingeschlossen wird.

### <a name="oidcconfigurationcontroller"></a>Oidcconfigurationcontroller

In der `OidcConfigurationController` (*Controllers/oidcconfigurationcontroller. cs*) wird der Client Endpunkt bereitgestellt, um oidc-Parameter bereitzustellen.

### <a name="app-settings-files"></a>App-Einstellungsdateien

In der APP-Einstellungsdatei (*appsettings.jsauf*) im Projektstamm Abschnitt wird in diesem `IdentityServer` Abschnitt die Liste der konfigurierten Clients beschrieben. Im folgenden Beispiel gibt es einen einzelnen Client. Der Client Name entspricht dem APP-Namen und wird dem OAuth-Parameter gemäß der Konvention zugeordnet `ClientId` . Das Profil gibt den App-Typ an, der konfiguriert wird. Das Profil wird intern verwendet, um Konventionen zu steuern, die den Konfigurationsprozess für den Server vereinfachen. <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

Der Platzhalter `{APP ASSEMBLY}` ist der AssemblyName der APP (z `BlazorSample.Client` . b.).

## <a name="client-app-configuration"></a>Client-App-Konfiguration

### <a name="authentication-package"></a>Authentifizierungs Paket

Wenn eine APP für die Verwendung einzelner Benutzerkonten () erstellt wird `Individual` , erhält die APP automatisch einen Paket Verweis für das [Microsoft. aspnetcore. Components. Webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) -Paket in der Projektdatei der app. Das Paket enthält eine Reihe von primitiven, die der APP helfen, Benutzer zu authentifizieren und Token abzurufen, um geschützte APIs aufzurufen.

Wenn Sie eine Authentifizierung zu einer APP hinzufügen, fügen Sie das Paket manuell zur Projektdatei der APP hinzu:

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

### <a name="api-authorization-support"></a>API-Autorisierungs Unterstützung

Die Unterstützung für die Authentifizierung von Benutzern wird durch die im [Microsoft. aspnetcore. Components. Webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) -Paket bereitgestellte Erweiterungsmethode an den Dienst Container angeschlossen. Diese Methode richtet die Dienste ein, die von der APP für die Interaktion mit dem vorhandenen Autorisierungssystem benötigt werden.

```csharp
builder.Services.AddApiAuthorization();
```

Standardmäßig wird die Konfiguration für die APP gemäß der Konvention von geladen `_configuration/{client-id}` . Gemäß der Konvention wird die Client-ID auf den Assemblynamen der APP festgelegt. Diese URL kann geändert werden, um auf einen separaten Endpunkt zu verweisen, indem Sie die-Überladung mit-Optionen aufrufen.

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
  * Bietet einen Link zur Benutzerprofil Seite in ASP.net Core Identity .
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

Führen Sie die APP aus dem Server Projekt aus. Wenn Sie Visual Studio verwenden, können Sie Folgendes tun:

* Legen Sie die Dropdown Liste **Start Projekte** auf der Symbolleiste auf die *Server-API-APP* fest, und wählen Sie die Schaltfläche **Ausführen** .
* Wählen Sie das Server Projekt in **Projektmappen-Explorer** aus, und klicken Sie auf der Symbolleiste auf die Schaltfläche **Ausführen** , oder starten Sie die APP im Menü **Debuggen**

## <a name="name-and-role-claim-with-api-authorization"></a>Name und Rollen Anspruch mit API-Autorisierung

### <a name="custom-user-factory"></a>Benutzerdefinierte benutzerfactory

Erstellen Sie in der Client-App eine benutzerdefinierte benutzerfactory. IdentityDer Server sendet mehrere Rollen als JSON-Array in einem einzelnen `role` Anspruch. Eine einzelne Rolle wird als Zeichen folgen Wert im Anspruch gesendet. Die Factory erstellt einen einzelnen `role` Anspruch für jede der Rollen des Benutzers.

*CustomUserFactory.cs*:

```csharp
using System.Linq;
using System.Security.Claims;
using System.Text.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    public CustomUserFactory(IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var user = await base.CreateUserAsync(account, options);

        if (user.Identity.IsAuthenticated)
        {
            var identity = (ClaimsIdentity)user.Identity;
            var roleClaims = identity.FindAll(identity.RoleClaimType);

            if (roleClaims != null && roleClaims.Any())
            {
                foreach (var existingClaim in roleClaims)
                {
                    identity.RemoveClaim(existingClaim);
                }

                var rolesElem = account.AdditionalProperties[identity.RoleClaimType];

                if (rolesElem is JsonElement roles)
                {
                    if (roles.ValueKind == JsonValueKind.Array)
                    {
                        foreach (var role in roles.EnumerateArray())
                        {
                            identity.AddClaim(new Claim(options.RoleClaim, role.GetString()));
                        }
                    }
                    else
                    {
                        identity.AddClaim(new Claim(options.RoleClaim, roles.GetString()));
                    }
                }
            }
        }

        return user;
    }
}
```

Registrieren Sie in der Client-App die Factory in `Program.Main` (*Program.cs*):

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

Wenden Sie in der Server-APP <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> für den Generator an Identity , der Rollen bezogene Dienste hinzufügt:

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-identity-server"></a>IdentityServer konfigurieren

Verwenden Sie **einen** der folgenden Ansätze:

* [API-Autorisierungs Optionen](#api-authorization-options)
* [Profil Dienst](#profile-service)

#### <a name="api-authorization-options"></a>API-Autorisierungs Optionen

In der Server-App:

* Konfigurieren Identity Sie den Server so, dass die `name` `role` Ansprüche und in das ID-Token und das Zugriffs Token eingefügt werden.
* Hiermit wird die Standard Zuordnung für Rollen im JWT-Tokenhandler verhindert.

```csharp
using System.IdentityModel.Tokens.Jwt;
using System.Linq;

...

services.AddIdentityServer()
    .AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options => {
        options.IdentityResources["openid"].UserClaims.Add("name");
        options.ApiResources.Single().UserClaims.Add("name");
        options.IdentityResources["openid"].UserClaims.Add("role");
        options.ApiResources.Single().UserClaims.Add("role");
    });

JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Remove("role");
```

#### <a name="profile-service"></a>Profil Dienst

Erstellen Sie in der Server-App eine- `ProfileService` Implementierung.

*ProfileService.cs*:

```csharp
using IdentityModel;
using IdentityServer4.Models;
using IdentityServer4.Services;
using System.Threading.Tasks;

public class ProfileService : IProfileService
{
    public ProfileService()
    {
    }

    public Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        return Task.CompletedTask;
    }

    public Task IsActiveAsync(IsActiveContext context)
    {
        return Task.CompletedTask;
    }
}
```

Registrieren Sie in der Server-App den Profil Dienst in `Startup.ConfigureServices` :

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a>Verwenden von Autorisierungs Mechanismen

In der Client-App sind die Komponenten Autorisierungs Ansätze an dieser Stelle funktionsfähig. Jeder der Autorisierungs Mechanismen in-Komponenten kann eine Rolle verwenden, um den Benutzer zu autorisieren:

* [Autorität View-Komponente](xref:security/blazor/index#authorizeview-component) (Beispiel: `<AuthorizeView Roles="admin">` )
* [ `[Authorize]` Attribute-Direktive](xref:security/blazor/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (Beispiel: `@attribute [Authorize(Roles = "admin")]` )
* [Prozedurale Logik](xref:security/blazor/index#procedural-logic) (Beispiel: `if (user.IsInRole("admin")) { ... }` )

  Mehrere Rollen Tests werden unterstützt:

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

`User.Identity.Name`wird in der Client-App mit dem Benutzernamen des Benutzers aufgefüllt, bei dem es sich in der Regel um Ihre Anmelde-e-Mail-Adresse handelt.

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Bereitstellung für Azure App Service](xref:security/authentication/identity/spa#deploy-to-production)
* [Importieren eines Zertifikats aus Key Vault (Azure-Dokumentation)](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:security/blazor/webassembly/additional-scenarios>
* [Nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen in einer APP mit einem sicheren Standard Client](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)

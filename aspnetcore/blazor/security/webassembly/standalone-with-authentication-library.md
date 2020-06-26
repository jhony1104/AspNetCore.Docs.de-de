---
title: Sichern einer eigenständigen ASP.NET Core Blazor WebAssembly-App mit der Authentifizierungsbibliothek
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
uid: blazor/security/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 5a05543c77f1ebaebadc27236aa8f7634e84f1fd
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243412"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-the-authentication-library"></a>Sichern einer eigenständigen ASP.NET Core Blazor WebAssembly-App mit der Authentifizierungsbibliothek

Von [Javier Calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)

*Befolgen Sie die Anweisungen in diesem Artikel nicht für Azure Active Directory (AAD) und Azure Active Directory B2C (AAD B2C). Informationen zu AAD und AAD B2C finden Sie in den entsprechenden Artikeln in diesem Inhaltsverzeichnisknoten.*

Führen Sie den folgenden Befehl in einer Befehlsshell aus, um eine eigenständige Blazor WebAssembly-App zu erstellen, die die Bibliothek [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) verwendet:

```dotnetcli
dotnet new blazorwasm -au Individual
```

Zum Angeben eines Ausgabespeicherorts (wodurch ein Projektordner erstellt wird, falls noch nicht vorhanden) schließen Sie die Ausgabeoption mit einem Pfad (z. B. `-o BlazorSample`) in den Befehl ein. Der Name des Ordners wird auch Teil des Projektnamens.

[Erstellen Sie eine Blazor WebAssembly-App](xref:blazor/get-started) in Visual Studio. Legen Sie die **Authentifizierung** auf **Einzelne Benutzerkonten** mit der Option **In-App-Speicherung von Benutzerkonten** fest.

## <a name="authentication-package"></a>Authentifizierungspaket

Wenn eine App erstellt wird, die einzelne Benutzerkonten verwendet, erhält die App automatisch einen Paketverweis für das [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/)-Paket in der Projektdatei der App. Das Paket stellt einige Primitive bereit, die der App beim Authentifizieren von Benutzern und beim Abrufen von Token zum Aufrufen geschützter APIs helfen.

Wenn Sie einer App eine Authentifizierung hinzufügen, fügen Sie das Paket manuell der Projektdatei der App hinzu:

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

## <a name="authentication-service-support"></a>Unterstützung für Authentifizierungsdienste

Die Unterstützung für die Authentifizierung von Benutzern wird im Dienstcontainer mit der vom Paket [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) bereitgestellten <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>-Erweiterungsmethode registriert. Diese Methode richtet die Dienste ein, die erforderlich sind, damit die App mit dem Identitätsanbieter interagiert.

`Program.cs`:

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

Die Konfiguration wird durch die Datei `wwwroot/appsettings.json` bereitgestellt:

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

Für die Authentifizierungsunterstützung für eigenständige Apps wird OpenID Connect (OIDC) verwendet. Die <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>-Methode akzeptiert einen Rückruf zum Konfigurieren der für die Authentifizierung einer App mit OIDC erforderlichen Parameter. Die für das Konfigurieren der App erforderlichen Werte können über die OIDC-kompatible IP-Adresse abgerufen werden. Rufen Sie die Werte ab, wenn Sie die App registrieren. Dies erfolgt in der Regel im entsprechenden Onlineportal.

## <a name="access-token-scopes"></a>Zugriffstokenbereiche

Die Blazor WebAssembly-Vorlage konfiguriert die App nicht automatisch so, dass diese ein Zugriffstoken für eine sichere API anfordert. Zum Bereitstellen eines Zugriffstokens als Teil des Anmeldeflows fügen Sie den Bereich den Standardtokenbereichen von <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions> hinzu:

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

Weitere Informationen finden Sie in den folgenden Abschnitten des Artikels zu *zusätzlichen Szenarios*:

* [Anfordern zusätzlicher Zugriffstoken](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [Anfügen von Token an ausgehende Anforderungen](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a>Imports-Datei

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>Indexseite

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a>App-Komponente

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>RedirectToLogin-Komponente

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>LoginDisplay-Komponente

Die `LoginDisplay`-Komponente (`Shared/LoginDisplay.razor`) wird in der `MainLayout`-Komponente (`Shared/MainLayout.razor`) gerendert, und sie verwaltet die folgenden Verhaltensweisen:

* Für authentifizierte Benutzer:
  * Zeigt den aktuellen Benutzernamen an
  * Bietet eine Schaltfläche zum Abmelden von der App
* Bietet die Option zur Anmeldung für anonyme Benutzer

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
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

## <a name="authentication-component"></a>Authentication-Komponente

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:blazor/security/webassembly/additional-scenarios>
* [Nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen in einer App mit einem sicheren Standardclient](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)

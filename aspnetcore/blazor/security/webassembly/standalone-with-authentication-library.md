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
ms.openlocfilehash: ba6b3a333a021184ad8a42d6292915e908cc6eb7
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103280"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="877d8-102">Sichern einer eigenständigen ASP.NET Core Blazor WebAssembly-App mit der Authentifizierungsbibliothek</span><span class="sxs-lookup"><span data-stu-id="877d8-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="877d8-103">Von [Javier Calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="877d8-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="877d8-104">*Befolgen Sie die Anweisungen in diesem Artikel nicht für Azure Active Directory (AAD) und Azure Active Directory B2C (AAD B2C). Informationen zu AAD und AAD B2C finden Sie in den entsprechenden Artikeln in diesem Inhaltsverzeichnisknoten.*</span><span class="sxs-lookup"><span data-stu-id="877d8-104">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="877d8-105">Führen Sie den folgenden Befehl in einer Befehlsshell aus, um eine eigenständige Blazor WebAssembly-App zu erstellen, die die Bibliothek [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) verwendet:</span><span class="sxs-lookup"><span data-stu-id="877d8-105">To create a Blazor WebAssembly standalone app that uses [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="877d8-106">Zum Angeben eines Ausgabespeicherorts (wodurch ein Projektordner erstellt wird, falls noch nicht vorhanden) schließen Sie die Ausgabeoption mit einem Pfad (z. B. `-o BlazorSample`) in den Befehl ein.</span><span class="sxs-lookup"><span data-stu-id="877d8-106">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="877d8-107">Der Name des Ordners wird auch Teil des Projektnamens.</span><span class="sxs-lookup"><span data-stu-id="877d8-107">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="877d8-108">[Erstellen Sie eine Blazor WebAssembly-App](xref:blazor/get-started) in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="877d8-108">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="877d8-109">Legen Sie die **Authentifizierung** auf **Einzelne Benutzerkonten** mit der Option **In-App-Speicherung von Benutzerkonten** fest.</span><span class="sxs-lookup"><span data-stu-id="877d8-109">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="877d8-110">Authentifizierungspaket</span><span class="sxs-lookup"><span data-stu-id="877d8-110">Authentication package</span></span>

<span data-ttu-id="877d8-111">Wenn eine App erstellt wird, die einzelne Benutzerkonten verwendet, erhält die App automatisch einen Paketverweis für das [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/)-Paket in der Projektdatei der App.</span><span class="sxs-lookup"><span data-stu-id="877d8-111">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package in the app's project file.</span></span> <span data-ttu-id="877d8-112">Das Paket stellt einige Primitive bereit, die der App beim Authentifizieren von Benutzern und beim Abrufen von Token zum Aufrufen geschützter APIs helfen.</span><span class="sxs-lookup"><span data-stu-id="877d8-112">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="877d8-113">Wenn Sie einer App eine Authentifizierung hinzufügen, fügen Sie das Paket manuell der Projektdatei der App hinzu:</span><span class="sxs-lookup"><span data-stu-id="877d8-113">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

## <a name="authentication-service-support"></a><span data-ttu-id="877d8-114">Unterstützung für Authentifizierungsdienste</span><span class="sxs-lookup"><span data-stu-id="877d8-114">Authentication service support</span></span>

<span data-ttu-id="877d8-115">Die Unterstützung für die Authentifizierung von Benutzern wird im Dienstcontainer mit der vom Paket [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) bereitgestellten <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>-Erweiterungsmethode registriert.</span><span class="sxs-lookup"><span data-stu-id="877d8-115">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> extension method provided by the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span> <span data-ttu-id="877d8-116">Diese Methode richtet die Dienste ein, die erforderlich sind, damit die App mit dem Identitätsanbieter interagiert.</span><span class="sxs-lookup"><span data-stu-id="877d8-116">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="877d8-117">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="877d8-117">*Program.cs*:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="877d8-118">Die Konfiguration wird durch die Datei *wwwroot/appsettings.json* bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="877d8-118">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

<span data-ttu-id="877d8-119">Für die Authentifizierungsunterstützung für eigenständige Apps wird OpenID Connect (OIDC) verwendet.</span><span class="sxs-lookup"><span data-stu-id="877d8-119">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="877d8-120">Die <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>-Methode akzeptiert einen Rückruf zum Konfigurieren der für die Authentifizierung einer App mit OIDC erforderlichen Parameter.</span><span class="sxs-lookup"><span data-stu-id="877d8-120">The <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="877d8-121">Die für das Konfigurieren der App erforderlichen Werte können über die OIDC-kompatible IP-Adresse abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="877d8-121">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="877d8-122">Rufen Sie die Werte ab, wenn Sie die App registrieren. Dies erfolgt in der Regel im entsprechenden Onlineportal.</span><span class="sxs-lookup"><span data-stu-id="877d8-122">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="877d8-123">Zugriffstokenbereiche</span><span class="sxs-lookup"><span data-stu-id="877d8-123">Access token scopes</span></span>

<span data-ttu-id="877d8-124">Die Blazor WebAssembly-Vorlage konfiguriert die App nicht automatisch so, dass diese ein Zugriffstoken für eine sichere API anfordert.</span><span class="sxs-lookup"><span data-stu-id="877d8-124">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="877d8-125">Zum Bereitstellen eines Zugriffstokens als Teil des Anmeldeflows fügen Sie den Bereich den Standardtokenbereichen von <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions> hinzu:</span><span class="sxs-lookup"><span data-stu-id="877d8-125">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="877d8-126">Weitere Informationen finden Sie in den folgenden Abschnitten des Artikels zu *zusätzlichen Szenarios*:</span><span class="sxs-lookup"><span data-stu-id="877d8-126">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="877d8-127">Anfordern zusätzlicher Zugriffstoken</span><span class="sxs-lookup"><span data-stu-id="877d8-127">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="877d8-128">Anfügen von Token an ausgehende Anforderungen</span><span class="sxs-lookup"><span data-stu-id="877d8-128">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="877d8-129">Imports-Datei</span><span class="sxs-lookup"><span data-stu-id="877d8-129">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="877d8-130">Indexseite</span><span class="sxs-lookup"><span data-stu-id="877d8-130">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="877d8-131">App-Komponente</span><span class="sxs-lookup"><span data-stu-id="877d8-131">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="877d8-132">RedirectToLogin-Komponente</span><span class="sxs-lookup"><span data-stu-id="877d8-132">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="877d8-133">LoginDisplay-Komponente</span><span class="sxs-lookup"><span data-stu-id="877d8-133">LoginDisplay component</span></span>

<span data-ttu-id="877d8-134">Die `LoginDisplay`-Komponente (*Shared/LoginDisplay.razor*) wird in der `MainLayout`-Komponente (*Shared/MainLayout.razor*) gerendert, und sie verwaltet die folgenden Verhaltensweisen:</span><span class="sxs-lookup"><span data-stu-id="877d8-134">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="877d8-135">Für authentifizierte Benutzer:</span><span class="sxs-lookup"><span data-stu-id="877d8-135">For authenticated users:</span></span>
  * <span data-ttu-id="877d8-136">Zeigt den aktuellen Benutzernamen an</span><span class="sxs-lookup"><span data-stu-id="877d8-136">Displays the current username.</span></span>
  * <span data-ttu-id="877d8-137">Bietet eine Schaltfläche zum Abmelden von der App</span><span class="sxs-lookup"><span data-stu-id="877d8-137">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="877d8-138">Bietet die Option zur Anmeldung für anonyme Benutzer</span><span class="sxs-lookup"><span data-stu-id="877d8-138">For anonymous users, offers the option to log in.</span></span>

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

## <a name="authentication-component"></a><span data-ttu-id="877d8-139">Authentication-Komponente</span><span class="sxs-lookup"><span data-stu-id="877d8-139">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="877d8-140">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="877d8-140">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="877d8-141">Nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen in einer App mit einem sicheren Standardclient</span><span class="sxs-lookup"><span data-stu-id="877d8-141">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)

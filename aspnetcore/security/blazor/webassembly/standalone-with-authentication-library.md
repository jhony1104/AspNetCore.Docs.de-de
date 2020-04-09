---
title: Sichern einer Blazor eigenständigen Core WebAssembly-App mit der Authentifizierungsbibliothek ASP.NET Core WebAssembly
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 893fff10df37e1c2be549604f4cb83cd20049108
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977040"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="d8cd6-102">Sichern einer Blazor eigenständigen Core WebAssembly-App mit der Authentifizierungsbibliothek ASP.NET Core WebAssembly</span><span class="sxs-lookup"><span data-stu-id="d8cd6-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="d8cd6-103">Von [Javier Calvarro Nelson](https://github.com/javiercn) und Luke [Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d8cd6-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="d8cd6-104">*Befolgen Sie für Azure Active Directory (AAD) und Azure Active Directory B2C (AAD B2C) nicht die Anweisungen in diesem Thema. Siehe die Themen AAD und AAD B2C in diesem Inhaltsverzeichnisknoten.*</span><span class="sxs-lookup"><span data-stu-id="d8cd6-104">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="d8cd6-105">Um eine Blazor eigenständige WebAssembly-App `Microsoft.AspNetCore.Components.WebAssembly.Authentication` zu erstellen, die Bibliothek verwendet, führen Sie den folgenden Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="d8cd6-105">To create a Blazor WebAssembly standalone app that uses `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="d8cd6-106">Um den Ausgabespeicherort anzugeben, der einen Projektordner erstellt, falls dieser nicht vorhanden ist, fügen `-o BlazorSample`Sie die Ausgabeoption in den Befehl mit einem Pfad ein (z. B. ).</span><span class="sxs-lookup"><span data-stu-id="d8cd6-106">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="d8cd6-107">Der Ordnername wird auch Teil des Projektnamens.</span><span class="sxs-lookup"><span data-stu-id="d8cd6-107">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="d8cd6-108">Erstellen Sie in Visual Studio [eine Blazor WebAssembly-App](xref:blazor/get-started).</span><span class="sxs-lookup"><span data-stu-id="d8cd6-108">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="d8cd6-109">Legen Sie **die Authentifizierung** auf **einzelne Benutzerkonten** mit der **Option Benutzerkonten speichern in der App** fest.</span><span class="sxs-lookup"><span data-stu-id="d8cd6-109">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="d8cd6-110">Authentifizierungspaket</span><span class="sxs-lookup"><span data-stu-id="d8cd6-110">Authentication package</span></span>

<span data-ttu-id="d8cd6-111">Wenn eine App für die Verwendung einzelner Benutzerkonten erstellt wird, erhält die App automatisch eine Paketreferenz für das `Microsoft.AspNetCore.Components.WebAssembly.Authentication` Paket in der Projektdatei der App.</span><span class="sxs-lookup"><span data-stu-id="d8cd6-111">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="d8cd6-112">Das Paket stellt eine Reihe von Primitiven bereit, die der App helfen, Benutzer zu authentifizieren und Token zum Aufrufen geschützter APIs zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="d8cd6-112">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="d8cd6-113">Wenn Sie einer App eine Authentifizierung hinzufügen, fügen Sie das Paket manuell zur Projektdatei der App hinzu:</span><span class="sxs-lookup"><span data-stu-id="d8cd6-113">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="d8cd6-114">Ersetzen `{VERSION}` Sie im vorherigen Paketverweis `Microsoft.AspNetCore.Blazor.Templates` durch die <xref:blazor/get-started> im Artikel gezeigte Version des Pakets.</span><span class="sxs-lookup"><span data-stu-id="d8cd6-114">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="d8cd6-115">Unterstützung des Authentifizierungsdienstes</span><span class="sxs-lookup"><span data-stu-id="d8cd6-115">Authentication service support</span></span>

<span data-ttu-id="d8cd6-116">Unterstützung für die Authentifizierung von Benutzern wird `AddOidcAuthentication` im Dienstcontainer `Microsoft.AspNetCore.Components.WebAssembly.Authentication` mit der vom Paket bereitgestellten Erweiterungsmethode registriert.</span><span class="sxs-lookup"><span data-stu-id="d8cd6-116">Support for authenticating users is registered in the service container with the `AddOidcAuthentication` extension method provided by the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="d8cd6-117">Diese Methode richtet alle Dienste ein, die für die Interaktion der App mit dem Identitätsanbieter (IDENTITY Provider, IP) erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="d8cd6-117">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="d8cd6-118">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="d8cd6-118">*Program.cs*:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    options.ProviderOptions.Authority = "{AUTHORITY}";
    options.ProviderOptions.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="d8cd6-119">Die Authentifizierungsunterstützung für eigenständige Apps wird mit Open ID Connect (OIDC) angeboten.</span><span class="sxs-lookup"><span data-stu-id="d8cd6-119">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="d8cd6-120">Die `AddOidcAuthentication` Methode akzeptiert einen Rückruf, um die Parameter zu konfigurieren, die erforderlich sind, um eine App mit OIDC zu authentifizieren.</span><span class="sxs-lookup"><span data-stu-id="d8cd6-120">The `AddOidcAuthentication` method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="d8cd6-121">Die für die Konfiguration der App erforderlichen Werte können über die OIDC-kompatible IP abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="d8cd6-121">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="d8cd6-122">Rufen Sie die Werte ab, wenn Sie die App registrieren, was in der Regel in ihrem Onlineportal auftritt.</span><span class="sxs-lookup"><span data-stu-id="d8cd6-122">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="d8cd6-123">Zugriffstokenbereiche</span><span class="sxs-lookup"><span data-stu-id="d8cd6-123">Access token scopes</span></span>

<span data-ttu-id="d8cd6-124">Die Blazor WebAssembly-Vorlage konfiguriert die App nicht automatisch, um ein Zugriffstoken für eine sichere API anzufordern.</span><span class="sxs-lookup"><span data-stu-id="d8cd6-124">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="d8cd6-125">Um ein Token als Teil des Anmeldeflusses zu bereitstellen, fügen Sie `OidcProviderOptions`den Bereich den Standardtokenbereichen der folgenden Werte hinzu:</span><span class="sxs-lookup"><span data-stu-id="d8cd6-125">To provision a token as part of the sign-in flow, add the scope to the default token scopes of the `OidcProviderOptions`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="d8cd6-126">Wenn das Azure-Portal einen Bereichs-URI bereitstellt und **die App eine nicht behandelte Ausnahme auslöst,** wenn sie eine *401 nicht autorisierte* Antwort von der API empfängt, versuchen Sie, einen Bereichs-URI zu verwenden, der das Schema und den Host nicht enthält.</span><span class="sxs-lookup"><span data-stu-id="d8cd6-126">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="d8cd6-127">Das Azure-Portal kann z. B. eines der folgenden Bereichs-URI-Formate bereitstellen:</span><span class="sxs-lookup"><span data-stu-id="d8cd6-127">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="d8cd6-128">Geben Sie den Bereichs-URI ohne Schema und Host:</span><span class="sxs-lookup"><span data-stu-id="d8cd6-128">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="d8cd6-129">Weitere Informationen finden Sie unter <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="d8cd6-129">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

## <a name="imports-file"></a><span data-ttu-id="d8cd6-130">Imports-Datei</span><span class="sxs-lookup"><span data-stu-id="d8cd6-130">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="d8cd6-131">Indexseite</span><span class="sxs-lookup"><span data-stu-id="d8cd6-131">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="d8cd6-132">App-Komponente</span><span class="sxs-lookup"><span data-stu-id="d8cd6-132">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="d8cd6-133">RedirectToLogin-Komponente</span><span class="sxs-lookup"><span data-stu-id="d8cd6-133">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="d8cd6-134">LoginDisplay-Komponente</span><span class="sxs-lookup"><span data-stu-id="d8cd6-134">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="d8cd6-135">Authentifizierungskomponente</span><span class="sxs-lookup"><span data-stu-id="d8cd6-135">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="d8cd6-136">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="d8cd6-136">Additional resources</span></span>

* [<span data-ttu-id="d8cd6-137">Zusätzliche Zugriffstoken anfordern</span><span class="sxs-lookup"><span data-stu-id="d8cd6-137">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)

---
title: Sichern einer ASP.net Core Blazor eigenständigen Webassembly-App mit der Authentifizierungs Bibliothek
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 043e4548ad6f40fdf1e6c27cd51946c7bf59a66e
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110946"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="60311-102">Sichern einer ASP.net Core Blazor eigenständigen Webassembly-App mit der Authentifizierungs Bibliothek</span><span class="sxs-lookup"><span data-stu-id="60311-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="60311-103">Von [Javier calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="60311-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="60311-104">Die Anleitung in diesem Artikel gilt für ASP.net Core 3,2 Preview 4.</span><span class="sxs-lookup"><span data-stu-id="60311-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="60311-105">Dieses Thema wird aktualisiert, um Preview 5 am Freitag, dem 24. April, zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="60311-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="60311-106">*Befolgen Sie die Anweisungen in diesem Thema für Azure Active Directory (AAD) und Azure Active Directory B2C (AAD B2C). Weitere Informationen finden Sie in den Themen zu Aad und Aad B2C in diesem Knoten Inhaltsverzeichnis.*</span><span class="sxs-lookup"><span data-stu-id="60311-106">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="60311-107">Führen Sie den Blazor folgenden Befehl in einer Befehlsshell aus, um eine eigenständige Webassembly-APP zu erstellen, die die Bibliothek verwendet `Microsoft.AspNetCore.Components.WebAssembly.Authentication` :</span><span class="sxs-lookup"><span data-stu-id="60311-107">To create a Blazor WebAssembly standalone app that uses `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="60311-108">Um den Ausgabe Speicherort anzugeben, der einen Projektordner erstellt, wenn er nicht vorhanden ist, schließen Sie die Output-Option in den Befehl mit einem `-o BlazorSample`Pfad (z. b.) ein.</span><span class="sxs-lookup"><span data-stu-id="60311-108">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="60311-109">Der Name des Ordners wird auch Teil des Projekt namens.</span><span class="sxs-lookup"><span data-stu-id="60311-109">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="60311-110">Erstellen Sie in Visual Studio [eine Blazor Webassembly-App](xref:blazor/get-started).</span><span class="sxs-lookup"><span data-stu-id="60311-110">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="60311-111">Legen Sie die **Authentifizierung** auf **einzelne Benutzerkonten** fest, indem Sie die Option **Benutzerkonten in-APP speichern** auswählen.</span><span class="sxs-lookup"><span data-stu-id="60311-111">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="60311-112">Authentifizierungs Paket</span><span class="sxs-lookup"><span data-stu-id="60311-112">Authentication package</span></span>

<span data-ttu-id="60311-113">Wenn eine APP für die Verwendung einzelner Benutzerkonten erstellt wird, erhält die APP automatisch einen Paket Verweis für `Microsoft.AspNetCore.Components.WebAssembly.Authentication` das Paket in der Projektdatei der app.</span><span class="sxs-lookup"><span data-stu-id="60311-113">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="60311-114">Das Paket enthält eine Reihe von primitiven, die der APP helfen, Benutzer zu authentifizieren und Token abzurufen, um geschützte APIs aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="60311-114">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="60311-115">Wenn Sie eine Authentifizierung zu einer APP hinzufügen, fügen Sie das Paket manuell zur Projektdatei der APP hinzu:</span><span class="sxs-lookup"><span data-stu-id="60311-115">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="60311-116">Ersetzen `{VERSION}` Sie im vorherigen Paket Verweis durch die Version des `Microsoft.AspNetCore.Blazor.Templates` Pakets, das im <xref:blazor/get-started> Artikel angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="60311-116">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="60311-117">Unterstützung für Authentifizierungsdienst</span><span class="sxs-lookup"><span data-stu-id="60311-117">Authentication service support</span></span>

<span data-ttu-id="60311-118">Die Unterstützung für das Authentifizieren von Benutzern wird im Dienst Container `AddOidcAuthentication` mit der vom `Microsoft.AspNetCore.Components.WebAssembly.Authentication` Paket bereitgestellten Erweiterungsmethode registriert.</span><span class="sxs-lookup"><span data-stu-id="60311-118">Support for authenticating users is registered in the service container with the `AddOidcAuthentication` extension method provided by the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="60311-119">Diese Methode richtet alle Dienste ein, die erforderlich sind, damit die APP mit dem Identitäts Anbieter (IP) interagieren muss.</span><span class="sxs-lookup"><span data-stu-id="60311-119">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="60311-120">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="60311-120">*Program.cs*:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    options.ProviderOptions.Authority = "{AUTHORITY}";
    options.ProviderOptions.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="60311-121">Die Authentifizierungs Unterstützung für eigenständige apps wird mithilfe von Open ID Connect (oidc) angeboten.</span><span class="sxs-lookup"><span data-stu-id="60311-121">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="60311-122">Die `AddOidcAuthentication` -Methode akzeptiert einen Rückruf, um die Parameter zu konfigurieren, die zum Authentifizieren einer App mithilfe von oidc erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="60311-122">The `AddOidcAuthentication` method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="60311-123">Die für die Konfiguration der APP erforderlichen Werte können von der oidc-kompatiblen IP-Adresse abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="60311-123">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="60311-124">Rufen Sie die Werte ab, wenn Sie die APP registrieren, die in der Regel in Ihrem Onlineportal erfolgt.</span><span class="sxs-lookup"><span data-stu-id="60311-124">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="60311-125">Zugriffs Token-Bereiche</span><span class="sxs-lookup"><span data-stu-id="60311-125">Access token scopes</span></span>

<span data-ttu-id="60311-126">Die Blazor webassemblyvorlage konfiguriert die APP nicht automatisch, um ein Zugriffs Token für eine sichere API anzufordern.</span><span class="sxs-lookup"><span data-stu-id="60311-126">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="60311-127">Fügen Sie zum Bereitstellen eines Tokens als Teil des Anmelde Flusses den Gültigkeitsbereich zu den standardtokenbereichen `OidcProviderOptions`von hinzu:</span><span class="sxs-lookup"><span data-stu-id="60311-127">To provision a token as part of the sign-in flow, add the scope to the default token scopes of the `OidcProviderOptions`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="60311-128">Wenn die Azure-Portal einen Bereichs-URI bereitstellt und **die APP eine nicht behandelte Ausnahme** auslöst, wenn Sie eine 401-nicht *autorisierte* Antwort von der API empfängt, verwenden Sie einen Bereichs-URI, der das Schema und den Host nicht enthält.</span><span class="sxs-lookup"><span data-stu-id="60311-128">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="60311-129">Der Azure-Portal kann z. b. eines der folgenden Bereich-URI-Formate bereitstellen:</span><span class="sxs-lookup"><span data-stu-id="60311-129">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="60311-130">Geben Sie den Bereichs-URI ohne das Schema und den Host an:</span><span class="sxs-lookup"><span data-stu-id="60311-130">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="60311-131">Weitere Informationen finden Sie unter <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="60311-131">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

## <a name="imports-file"></a><span data-ttu-id="60311-132">Import Datei</span><span class="sxs-lookup"><span data-stu-id="60311-132">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="60311-133">Indexseite</span><span class="sxs-lookup"><span data-stu-id="60311-133">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="60311-134">App-Komponente</span><span class="sxs-lookup"><span data-stu-id="60311-134">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="60311-135">Redirecttologin-Komponente</span><span class="sxs-lookup"><span data-stu-id="60311-135">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="60311-136">Logindisplay-Komponente</span><span class="sxs-lookup"><span data-stu-id="60311-136">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="60311-137">Authentifizierungs Komponente</span><span class="sxs-lookup"><span data-stu-id="60311-137">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="60311-138">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="60311-138">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
 
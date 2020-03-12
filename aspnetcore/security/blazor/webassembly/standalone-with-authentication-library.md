---
title: Sichern eines ASP.net Core Blazor eigenständigen Webassembly-App mit der Authentifizierungs Bibliothek
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: f9cc2884dcd94c729c45a056ae4327a2c75d34be
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083590"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="26252-102">Sichern eines ASP.net Core Blazor eigenständigen Webassembly-App mit der Authentifizierungs Bibliothek</span><span class="sxs-lookup"><span data-stu-id="26252-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="26252-103">Von [Javier calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="26252-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="26252-104">Führen Sie den folgenden Befehl in einer Befehlsshell aus, um eine eigenständige Blazor Webassembly-APP zu erstellen, die `Microsoft.AspNetCore.Components.WebAssembly.Authentication` Bibliothek verwendet:</span><span class="sxs-lookup"><span data-stu-id="26252-104">To create a Blazor WebAssembly standalone app that uses `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="26252-105">Um den Ausgabe Speicherort anzugeben, der einen Projektordner erstellt, wenn er nicht vorhanden ist, fügen Sie die Output-Option im Befehl mit einem Pfad (z. b. `-o BlazorSample`) ein.</span><span class="sxs-lookup"><span data-stu-id="26252-105">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="26252-106">Der Name des Ordners wird auch Teil des Projekt namens.</span><span class="sxs-lookup"><span data-stu-id="26252-106">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="26252-107">Erstellen Sie in Visual Studio [eine Blazor Webassembly-App](xref:blazor/get-started).</span><span class="sxs-lookup"><span data-stu-id="26252-107">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="26252-108">Legen Sie die **Authentifizierung** auf **einzelne Benutzerkonten** fest, indem Sie die Option **Benutzerkonten in-APP speichern** auswählen.</span><span class="sxs-lookup"><span data-stu-id="26252-108">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="26252-109">Authentifizierungs Paket</span><span class="sxs-lookup"><span data-stu-id="26252-109">Authentication package</span></span>

<span data-ttu-id="26252-110">Wenn eine APP für die Verwendung einzelner Benutzerkonten erstellt wird, erhält die APP automatisch einen Paket Verweis für das `Microsoft.AspNetCore.Components.WebAssembly.Authentication`-Paket in der Projektdatei der app.</span><span class="sxs-lookup"><span data-stu-id="26252-110">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="26252-111">Das Paket enthält eine Reihe von primitiven, die der APP helfen, Benutzer zu authentifizieren und Token abzurufen, um geschützte APIs aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="26252-111">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="26252-112">Wenn Sie eine Authentifizierung zu einer APP hinzufügen, fügen Sie das Paket manuell zur Projektdatei der APP hinzu:</span><span class="sxs-lookup"><span data-stu-id="26252-112">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="26252-113">Ersetzen Sie `{VERSION}` im vorangehenden Paket Verweis durch die Version des `Microsoft.AspNetCore.Blazor.Templates` Pakets, das im <xref:blazor/get-started> Artikel angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="26252-113">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="26252-114">Unterstützung für Authentifizierungsdienst</span><span class="sxs-lookup"><span data-stu-id="26252-114">Authentication service support</span></span>

<span data-ttu-id="26252-115">Die Unterstützung für das Authentifizieren von Benutzern wird im Dienst Container mit der `AddOidcAuthentication`-Erweiterungsmethode registriert, die vom `Microsoft.AspNetCore.Components.WebAssembly.Authentication`-Paket bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="26252-115">Support for authenticating users is registered in the service container with the `AddOidcAuthentication` extension method provided by the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="26252-116">Diese Methode richtet alle Dienste ein, die erforderlich sind, damit die APP mit dem Identitäts Anbieter (IP) interagieren muss.</span><span class="sxs-lookup"><span data-stu-id="26252-116">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="26252-117">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="26252-117">*Program.cs*:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    options.ProviderOptions.Authority = "{AUTHORITY}";
    options.ProviderOptions.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="26252-118">Die Authentifizierungs Unterstützung für eigenständige apps wird mithilfe von Open ID Connect (oidc) angeboten.</span><span class="sxs-lookup"><span data-stu-id="26252-118">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="26252-119">Die `AddOidcAuthentication`-Methode akzeptiert einen Rückruf, um die Parameter zu konfigurieren, die zum Authentifizieren einer App mithilfe von oidc erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="26252-119">The `AddOidcAuthentication` method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="26252-120">Die für die Konfiguration der APP erforderlichen Werte können von der IP-Adresse abgerufen werden, z. b. Google, Microsoft oder einem anderen oidc-kompatiblen Anbieter.</span><span class="sxs-lookup"><span data-stu-id="26252-120">The values required for configuring the app can be obtained from the IP, such as Google, Microsoft, or other OIDC-compliant provider.</span></span> <span data-ttu-id="26252-121">Rufen Sie die Werte ab, wenn Sie die APP registrieren, die in der Regel in Ihrem Onlineportal erfolgt.</span><span class="sxs-lookup"><span data-stu-id="26252-121">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="index-page"></a><span data-ttu-id="26252-122">Indexseite</span><span class="sxs-lookup"><span data-stu-id="26252-122">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

## <a name="app-component"></a><span data-ttu-id="26252-123">App-Komponente</span><span class="sxs-lookup"><span data-stu-id="26252-123">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="26252-124">Redirecttologin-Komponente</span><span class="sxs-lookup"><span data-stu-id="26252-124">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="26252-125">Logindisplay-Komponente</span><span class="sxs-lookup"><span data-stu-id="26252-125">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="26252-126">Authentifizierungs Komponente</span><span class="sxs-lookup"><span data-stu-id="26252-126">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

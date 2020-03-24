---
title: Sichern eines ASP.net Core Blazor eigenständigen Webassembly-App mit Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: e12c38ed42a4e2714d785ef8f03097246c40d36e
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/24/2020
ms.locfileid: "80218977"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="4d579-102">Sichern eines ASP.net Core Blazor eigenständigen Webassembly-App mit Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="4d579-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="4d579-103">Von [Javier calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="4d579-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="4d579-104">So erstellen Sie eine eigenständige Blazor Webassembly-APP, die [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) für die Authentifizierung verwendet:</span><span class="sxs-lookup"><span data-stu-id="4d579-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="4d579-105">[Erstellen Sie einen Aad-Mandanten und eine Webanwendung](/azure/active-directory/develop/v2-overview):</span><span class="sxs-lookup"><span data-stu-id="4d579-105">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="4d579-106">Registrieren Sie eine Aad-App im Bereich **Azure Active Directory** > **App-Registrierungen** des Azure-Portal:</span><span class="sxs-lookup"><span data-stu-id="4d579-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="4d579-107">Geben Sie einen **Namen** für die APP an (z. b. **Blazor Aad-Client**).</span><span class="sxs-lookup"><span data-stu-id="4d579-107">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="4d579-108">Wählen Sie einen **unterstützten Kontotyp**aus.</span><span class="sxs-lookup"><span data-stu-id="4d579-108">Choose a **Supported account types**.</span></span> <span data-ttu-id="4d579-109">Sie können **Konten in diesem Organisations Verzeichnis nur** für dieses Verfahren auswählen.</span><span class="sxs-lookup"><span data-stu-id="4d579-109">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="4d579-110">Lassen Sie die Dropdown-Einstellung für **Umleitungs-URI** auf **Web**und einen Umleitungs-URI `https://localhost:5001/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="4d579-110">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="4d579-111">Deaktivieren Sie das Kontrollkästchen **Berechtigungen** > **erteilen Sie Administrator Berechtigungen für OpenID und offline_access Berechtigungen** .</span><span class="sxs-lookup"><span data-stu-id="4d579-111">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="4d579-112">Wählen Sie **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="4d579-112">Select **Register**.</span></span>

<span data-ttu-id="4d579-113">In der **Authentifizierung** > **Platt Form Konfigurationen** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="4d579-113">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="4d579-114">Vergewissern Sie sich, dass der **Umleitungs-URI** `https://localhost:5001/authentication/login-callback` vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="4d579-114">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="4d579-115">Aktivieren Sie für **implizite Gewährung**die Kontrollkästchen für **Zugriffs Token** und **ID-Token**.</span><span class="sxs-lookup"><span data-stu-id="4d579-115">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="4d579-116">Die verbleibenden Standardwerte für die APP sind für diese Art von akzeptabel.</span><span class="sxs-lookup"><span data-stu-id="4d579-116">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="4d579-117">Wählen Sie die Schaltfläche **Speichern** aus.</span><span class="sxs-lookup"><span data-stu-id="4d579-117">Select the **Save** button.</span></span>

<span data-ttu-id="4d579-118">Notieren Sie sich die folgenden Informationen:</span><span class="sxs-lookup"><span data-stu-id="4d579-118">Record the following information:</span></span>

* <span data-ttu-id="4d579-119">Anwendungs-ID (Client-ID) (z. b. `11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="4d579-119">Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="4d579-120">Verzeichnis-ID (Mandanten-ID) (z. b. `22222222-2222-2222-2222-222222222222`)</span><span class="sxs-lookup"><span data-stu-id="4d579-120">Directory ID (Tenant ID) (for example, `22222222-2222-2222-2222-222222222222`)</span></span>

<span data-ttu-id="4d579-121">Ersetzen Sie die Platzhalter im folgenden Befehl durch die zuvor aufgezeichneten Informationen, und führen Sie den Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="4d579-121">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="4d579-122">Um den Ausgabe Speicherort anzugeben, der einen Projektordner erstellt, wenn er nicht vorhanden ist, fügen Sie die Output-Option im Befehl mit einem Pfad (z. b. `-o BlazorSample`) ein.</span><span class="sxs-lookup"><span data-stu-id="4d579-122">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="4d579-123">Der Name des Ordners wird auch Teil des Projekt namens.</span><span class="sxs-lookup"><span data-stu-id="4d579-123">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="4d579-124">Authentifizierungs Paket</span><span class="sxs-lookup"><span data-stu-id="4d579-124">Authentication package</span></span>

<span data-ttu-id="4d579-125">Wenn eine APP für die Verwendung von Geschäfts-, Schul-oder unikonten (`SingleOrg`) erstellt wird, erhält die APP automatisch einen Paket Verweis für die [Microsoft-Authentifizierungs Bibliothek](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="4d579-125">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="4d579-126">Das Paket enthält eine Reihe von primitiven, die der APP helfen, Benutzer zu authentifizieren und Token abzurufen, um geschützte APIs aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="4d579-126">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="4d579-127">Wenn Sie eine Authentifizierung zu einer APP hinzufügen, fügen Sie das Paket manuell zur Projektdatei der APP hinzu:</span><span class="sxs-lookup"><span data-stu-id="4d579-127">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="4d579-128">Ersetzen Sie `{VERSION}` im vorangehenden Paket Verweis durch die Version des `Microsoft.AspNetCore.Blazor.Templates` Pakets, das im <xref:blazor/get-started> Artikel angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="4d579-128">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="4d579-129">Das `Microsoft.Authentication.WebAssembly.Msal` Paket fügt das `Microsoft.AspNetCore.Components.WebAssembly.Authentication` Paket transitiv der APP hinzu.</span><span class="sxs-lookup"><span data-stu-id="4d579-129">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="4d579-130">Unterstützung für Authentifizierungsdienst</span><span class="sxs-lookup"><span data-stu-id="4d579-130">Authentication service support</span></span>

<span data-ttu-id="4d579-131">Die Unterstützung für das Authentifizieren von Benutzern wird im Dienst Container mit der `AddMsalAuthentication`-Erweiterungsmethode registriert, die vom `Microsoft.Authentication.WebAssembly.Msal`-Paket bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="4d579-131">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="4d579-132">Diese Methode richtet alle Dienste ein, die erforderlich sind, damit die APP mit dem Identitäts Anbieter (IP) interagieren muss.</span><span class="sxs-lookup"><span data-stu-id="4d579-132">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="4d579-133">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="4d579-133">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="4d579-134">Die `AddMsalAuthentication`-Methode akzeptiert einen Rückruf, um die Parameter zu konfigurieren, die zum Authentifizieren einer APP erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="4d579-134">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="4d579-135">Die für die Konfiguration der APP erforderlichen Werte können aus der Aad-Konfiguration des Azure-Portals abgerufen werden, wenn Sie die APP registrieren.</span><span class="sxs-lookup"><span data-stu-id="4d579-135">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="4d579-136">Die Vorlage Blazor Webassembly konfiguriert die APP nicht automatisch so, dass Sie ein Zugriffs Token für eine sichere API anfordert.</span><span class="sxs-lookup"><span data-stu-id="4d579-136">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="4d579-137">Fügen Sie zum Bereitstellen eines Tokens als Teil des Anmelde Flusses den Gültigkeitsbereich zu den standardzugriffstokenbereichen der `MsalProviderOptions`hinzu:</span><span class="sxs-lookup"><span data-stu-id="4d579-137">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}");
});
```

> [!NOTE]
> <span data-ttu-id="4d579-138">Der standardzugriffstokenbereich muss im Format `{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}` sein (z. b. `11111111-1111-1111-1111-111111111111/API.Access`).</span><span class="sxs-lookup"><span data-stu-id="4d579-138">The default access token scope must be in the format `{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}` (for example, `11111111-1111-1111-1111-111111111111/API.Access`).</span></span> <span data-ttu-id="4d579-139">Wenn für die Bereichseinstellung ein Schema oder ein Schema und ein Host bereitgestellt wird (wie im Azure-Portal gezeigt), löst die *Client-App* eine nicht behandelte Ausnahme aus, wenn Sie eine 401-nicht *autorisierte* Antwort von der Server- *API-APP*empfängt.</span><span class="sxs-lookup"><span data-stu-id="4d579-139">If a scheme or scheme and host is provided to the scope setting (as shown in the Azure Portal), the *Client app* throws an unhandled exception when it receives a *401 Unauthorized* response from the *Server API app*.</span></span>

## <a name="index-page"></a><span data-ttu-id="4d579-140">Indexseite</span><span class="sxs-lookup"><span data-stu-id="4d579-140">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="4d579-141">App-Komponente</span><span class="sxs-lookup"><span data-stu-id="4d579-141">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="4d579-142">Redirecttologin-Komponente</span><span class="sxs-lookup"><span data-stu-id="4d579-142">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="4d579-143">Logindisplay-Komponente</span><span class="sxs-lookup"><span data-stu-id="4d579-143">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="4d579-144">Authentifizierungs Komponente</span><span class="sxs-lookup"><span data-stu-id="4d579-144">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="4d579-145">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="4d579-145">Additional resources</span></span>

* <xref:security/authentication/azure-active-directory/index>

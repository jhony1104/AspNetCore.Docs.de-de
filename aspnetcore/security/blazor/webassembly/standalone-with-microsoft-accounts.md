---
title: Sichern eines ASP.net Core Blazor eigenständigen Webassembly-App mit Microsoft-Konten
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 6883af3486256e7c6905626d8da09e8ae0c4a896
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083650"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="f3721-102">Sichern eines ASP.net Core Blazor eigenständigen Webassembly-App mit Microsoft-Konten</span><span class="sxs-lookup"><span data-stu-id="f3721-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="f3721-103">Von [Javier calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f3721-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="f3721-104">So erstellen Sie eine eigenständige Blazor Webassembly-APP, die [Microsoft-Konten mit Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) für die Authentifizierung verwendet:</span><span class="sxs-lookup"><span data-stu-id="f3721-104">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

1. [<span data-ttu-id="f3721-105">Erstellen eines Aad-Mandanten und einer Webanwendung</span><span class="sxs-lookup"><span data-stu-id="f3721-105">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

   <span data-ttu-id="f3721-106">Registrieren Sie eine Aad-App im Bereich **Azure Active Directory** > **App-Registrierungen** des Azure-Portal:</span><span class="sxs-lookup"><span data-stu-id="f3721-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

   <span data-ttu-id="f3721-107">1 \.</span><span class="sxs-lookup"><span data-stu-id="f3721-107">1\.</span></span> <span data-ttu-id="f3721-108">Geben Sie einen **Namen** für die APP an (z. b. **Blazor Aad-Client**).</span><span class="sxs-lookup"><span data-stu-id="f3721-108">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span><br>
   <span data-ttu-id="f3721-109">2 \.</span><span class="sxs-lookup"><span data-stu-id="f3721-109">2\.</span></span> <span data-ttu-id="f3721-110">Wählen Sie **unter Unterstützte Konto Typen**die Option **Konten in einem beliebigen Organisations Verzeichnis**aus.</span><span class="sxs-lookup"><span data-stu-id="f3721-110">In **Supported account types**, select **Accounts in any organizational directory**.</span></span><br>
   <span data-ttu-id="f3721-111">3 \.</span><span class="sxs-lookup"><span data-stu-id="f3721-111">3\.</span></span> <span data-ttu-id="f3721-112">Lassen Sie die Dropdown-Einstellung für **Umleitungs-URI** auf **Web**und einen Umleitungs-URI `https://localhost:5001/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="f3721-112">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span><br>
   <span data-ttu-id="f3721-113">4 \.</span><span class="sxs-lookup"><span data-stu-id="f3721-113">4\.</span></span> <span data-ttu-id="f3721-114">Deaktivieren Sie das Kontrollkästchen **Berechtigungen** > **erteilen Sie Administrator Berechtigungen für OpenID und offline_access Berechtigungen** .</span><span class="sxs-lookup"><span data-stu-id="f3721-114">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span><br>
   <span data-ttu-id="f3721-115">5 \.</span><span class="sxs-lookup"><span data-stu-id="f3721-115">5\.</span></span> <span data-ttu-id="f3721-116">Wählen Sie **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="f3721-116">Select **Register**.</span></span>

   <span data-ttu-id="f3721-117">In der **Authentifizierung** > **Platt Form Konfigurationen** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="f3721-117">In **Authentication** > **Platform configurations** > **Web**:</span></span>

   <span data-ttu-id="f3721-118">1 \.</span><span class="sxs-lookup"><span data-stu-id="f3721-118">1\.</span></span> <span data-ttu-id="f3721-119">Vergewissern Sie sich, dass der **Umleitungs-URI** `https://localhost:5001/authentication/login-callback` vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="f3721-119">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span><br>
   <span data-ttu-id="f3721-120">2 \.</span><span class="sxs-lookup"><span data-stu-id="f3721-120">2\.</span></span> <span data-ttu-id="f3721-121">Aktivieren Sie für **implizite Gewährung**die Kontrollkästchen für **Zugriffs Token** und **ID-Token**.</span><span class="sxs-lookup"><span data-stu-id="f3721-121">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span><br>
   <span data-ttu-id="f3721-122">3 \.</span><span class="sxs-lookup"><span data-stu-id="f3721-122">3\.</span></span> <span data-ttu-id="f3721-123">Die verbleibenden Standardwerte für die APP sind für diese Art von akzeptabel.</span><span class="sxs-lookup"><span data-stu-id="f3721-123">The remaining defaults for the app are acceptable for this experience.</span></span><br>
   <span data-ttu-id="f3721-124">4 \.</span><span class="sxs-lookup"><span data-stu-id="f3721-124">4\.</span></span> <span data-ttu-id="f3721-125">Wählen Sie die Schaltfläche **Speichern** aus.</span><span class="sxs-lookup"><span data-stu-id="f3721-125">Select the **Save** button.</span></span>

   <span data-ttu-id="f3721-126">Notieren Sie die Anwendungs-ID (Client-ID) (z. b. `11111111-1111-1111-1111-111111111111`).</span><span class="sxs-lookup"><span data-stu-id="f3721-126">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

1. <span data-ttu-id="f3721-127">Ersetzen Sie die Platzhalter im folgenden Befehl durch die zuvor aufgezeichneten Informationen, und führen Sie den Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="f3721-127">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   <span data-ttu-id="f3721-128">Um den Ausgabe Speicherort anzugeben, der einen Projektordner erstellt, wenn er nicht vorhanden ist, fügen Sie die Output-Option im Befehl mit einem Pfad (z. b. `-o BlazorSample`) ein.</span><span class="sxs-lookup"><span data-stu-id="f3721-128">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="f3721-129">Der Name des Ordners wird auch Teil des Projekt namens.</span><span class="sxs-lookup"><span data-stu-id="f3721-129">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="f3721-130">Nachdem Sie die App erstellt haben, sollten Sie folgende Aktionen ausführen können:</span><span class="sxs-lookup"><span data-stu-id="f3721-130">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="f3721-131">Melden Sie sich mit einem Microsoft-Konto bei der APP an.</span><span class="sxs-lookup"><span data-stu-id="f3721-131">Log into the app using a Microsoft Account.</span></span>
* <span data-ttu-id="f3721-132">Anfordern von Zugriffs Token für Microsoft-APIs mit dem gleichen Ansatz wie für eigenständige Blazor-apps, vorausgesetzt, dass Sie die APP ordnungsgemäß konfiguriert haben.</span><span class="sxs-lookup"><span data-stu-id="f3721-132">Request access tokens for Microsoft APIs using the same approach as for standalone Blazor apps provided that you have configured the app correctly.</span></span> <span data-ttu-id="f3721-133">Weitere Informationen finden Sie unter [Schnellstart: Konfigurieren einer Anwendung für die](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)Bereitstellung von Web-APIs.</span><span class="sxs-lookup"><span data-stu-id="f3721-133">For more information, see [Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="f3721-134">Authentifizierungs Paket</span><span class="sxs-lookup"><span data-stu-id="f3721-134">Authentication package</span></span>

<span data-ttu-id="f3721-135">Wenn eine APP für die Verwendung von Geschäfts-, Schul-oder unikonten (`SingleOrg`) erstellt wird, erhält die APP automatisch einen Paket Verweis für die [Microsoft-Authentifizierungs Bibliothek](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="f3721-135">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="f3721-136">Das Paket enthält eine Reihe von primitiven, die der APP helfen, Benutzer zu authentifizieren und Token abzurufen, um geschützte APIs aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="f3721-136">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="f3721-137">Wenn Sie eine Authentifizierung zu einer APP hinzufügen, fügen Sie das Paket manuell zur Projektdatei der APP hinzu:</span><span class="sxs-lookup"><span data-stu-id="f3721-137">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="f3721-138">Ersetzen Sie `{VERSION}` im vorangehenden Paket Verweis durch die Version des `Microsoft.AspNetCore.Blazor.Templates` Pakets, das im <xref:blazor/get-started> Artikel angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="f3721-138">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="f3721-139">Das `Microsoft.Authentication.WebAssembly.Msal` Paket fügt das `Microsoft.AspNetCore.Components.WebAssembly.Authentication` Paket transitiv der APP hinzu.</span><span class="sxs-lookup"><span data-stu-id="f3721-139">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="f3721-140">Unterstützung für Authentifizierungsdienst</span><span class="sxs-lookup"><span data-stu-id="f3721-140">Authentication service support</span></span>

<span data-ttu-id="f3721-141">Die Unterstützung für das Authentifizieren von Benutzern wird im Dienst Container mit der `AddMsalAuthentication`-Erweiterungsmethode registriert, die vom `Microsoft.Authentication.WebAssembly.Msal`-Paket bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="f3721-141">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="f3721-142">Diese Methode richtet alle Dienste ein, die erforderlich sind, damit die APP mit dem Identitäts Anbieter (IP) interagieren muss.</span><span class="sxs-lookup"><span data-stu-id="f3721-142">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="f3721-143">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="f3721-143">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "{AUTHORITY}";
    authentication.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="f3721-144">Die `AddMsalAuthentication`-Methode akzeptiert einen Rückruf, um die Parameter zu konfigurieren, die zum Authentifizieren einer APP erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="f3721-144">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="f3721-145">Die für die Konfiguration der APP erforderlichen Werte können bei der Registrierung der APP aus der Microsoft-Konto Konfiguration abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="f3721-145">The values required for configuring the app can be obtained from the Microsoft Accounts configuration when you register the app.</span></span>

## <a name="index-page"></a><span data-ttu-id="f3721-146">Indexseite</span><span class="sxs-lookup"><span data-stu-id="f3721-146">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

## <a name="app-component"></a><span data-ttu-id="f3721-147">App-Komponente</span><span class="sxs-lookup"><span data-stu-id="f3721-147">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="f3721-148">Redirecttologin-Komponente</span><span class="sxs-lookup"><span data-stu-id="f3721-148">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="f3721-149">Logindisplay-Komponente</span><span class="sxs-lookup"><span data-stu-id="f3721-149">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="f3721-150">Authentifizierungs Komponente</span><span class="sxs-lookup"><span data-stu-id="f3721-150">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="f3721-151">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="f3721-151">Additional resources</span></span>

* [<span data-ttu-id="f3721-152">Schnellstart: Registrieren einer Anwendung bei der Microsoft Identity-Plattform</span><span class="sxs-lookup"><span data-stu-id="f3721-152">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="f3721-153">Schnellstart: Konfigurieren einer Anwendung für die Bereitstellung von Web-APIs</span><span class="sxs-lookup"><span data-stu-id="f3721-153">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)

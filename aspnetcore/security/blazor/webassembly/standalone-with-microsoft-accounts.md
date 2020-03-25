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
ms.openlocfilehash: be73bec971f96bd64afc735a1ea750d47c7bc383
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/24/2020
ms.locfileid: "80219258"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="18ff5-102">Sichern eines ASP.net Core Blazor eigenständigen Webassembly-App mit Microsoft-Konten</span><span class="sxs-lookup"><span data-stu-id="18ff5-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="18ff5-103">Von [Javier calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="18ff5-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="18ff5-104">So erstellen Sie eine eigenständige Blazor Webassembly-APP, die [Microsoft-Konten mit Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) für die Authentifizierung verwendet:</span><span class="sxs-lookup"><span data-stu-id="18ff5-104">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

1. [<span data-ttu-id="18ff5-105">Erstellen eines Aad-Mandanten und einer Webanwendung</span><span class="sxs-lookup"><span data-stu-id="18ff5-105">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

   <span data-ttu-id="18ff5-106">Registrieren Sie eine Aad-App im Bereich **Azure Active Directory** > **App-Registrierungen** des Azure-Portal:</span><span class="sxs-lookup"><span data-stu-id="18ff5-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

   <span data-ttu-id="18ff5-107">1\.</span><span class="sxs-lookup"><span data-stu-id="18ff5-107">1\.</span></span> <span data-ttu-id="18ff5-108">Geben Sie einen **Namen** für die APP an (z. b. **Blazor Aad-Client**).</span><span class="sxs-lookup"><span data-stu-id="18ff5-108">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span><br>
   <span data-ttu-id="18ff5-109">2\.</span><span class="sxs-lookup"><span data-stu-id="18ff5-109">2\.</span></span> <span data-ttu-id="18ff5-110">Wählen Sie **unter Unterstützte Konto Typen**die Option **Konten in einem beliebigen Organisations Verzeichnis**aus.</span><span class="sxs-lookup"><span data-stu-id="18ff5-110">In **Supported account types**, select **Accounts in any organizational directory**.</span></span><br>
   <span data-ttu-id="18ff5-111">3\.</span><span class="sxs-lookup"><span data-stu-id="18ff5-111">3\.</span></span> <span data-ttu-id="18ff5-112">Lassen Sie die Dropdown-Einstellung für **Umleitungs-URI** auf **Web**und einen Umleitungs-URI `https://localhost:5001/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="18ff5-112">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span><br>
   <span data-ttu-id="18ff5-113">4\.</span><span class="sxs-lookup"><span data-stu-id="18ff5-113">4\.</span></span> <span data-ttu-id="18ff5-114">Deaktivieren Sie das Kontrollkästchen **Berechtigungen** > **erteilen Sie Administrator Berechtigungen für OpenID und offline_access Berechtigungen** .</span><span class="sxs-lookup"><span data-stu-id="18ff5-114">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span><br>
   <span data-ttu-id="18ff5-115">5\.</span><span class="sxs-lookup"><span data-stu-id="18ff5-115">5\.</span></span> <span data-ttu-id="18ff5-116">Wählen Sie **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="18ff5-116">Select **Register**.</span></span>

   <span data-ttu-id="18ff5-117">In der **Authentifizierung** > **Platt Form Konfigurationen** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="18ff5-117">In **Authentication** > **Platform configurations** > **Web**:</span></span>

   <span data-ttu-id="18ff5-118">1\.</span><span class="sxs-lookup"><span data-stu-id="18ff5-118">1\.</span></span> <span data-ttu-id="18ff5-119">Vergewissern Sie sich, dass der **Umleitungs-URI** `https://localhost:5001/authentication/login-callback` vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="18ff5-119">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span><br>
   <span data-ttu-id="18ff5-120">2\.</span><span class="sxs-lookup"><span data-stu-id="18ff5-120">2\.</span></span> <span data-ttu-id="18ff5-121">Aktivieren Sie für **implizite Gewährung**die Kontrollkästchen für **Zugriffs Token** und **ID-Token**.</span><span class="sxs-lookup"><span data-stu-id="18ff5-121">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span><br>
   <span data-ttu-id="18ff5-122">3\.</span><span class="sxs-lookup"><span data-stu-id="18ff5-122">3\.</span></span> <span data-ttu-id="18ff5-123">Die verbleibenden Standardwerte für die APP sind für diese Art von akzeptabel.</span><span class="sxs-lookup"><span data-stu-id="18ff5-123">The remaining defaults for the app are acceptable for this experience.</span></span><br>
   <span data-ttu-id="18ff5-124">4\.</span><span class="sxs-lookup"><span data-stu-id="18ff5-124">4\.</span></span> <span data-ttu-id="18ff5-125">Wählen Sie die Schaltfläche **Speichern** aus.</span><span class="sxs-lookup"><span data-stu-id="18ff5-125">Select the **Save** button.</span></span>

   <span data-ttu-id="18ff5-126">Notieren Sie die Anwendungs-ID (Client-ID) (z. b. `11111111-1111-1111-1111-111111111111`).</span><span class="sxs-lookup"><span data-stu-id="18ff5-126">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

1. <span data-ttu-id="18ff5-127">Ersetzen Sie die Platzhalter im folgenden Befehl durch die zuvor aufgezeichneten Informationen, und führen Sie den Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="18ff5-127">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   <span data-ttu-id="18ff5-128">Um den Ausgabe Speicherort anzugeben, der einen Projektordner erstellt, wenn er nicht vorhanden ist, fügen Sie die Output-Option im Befehl mit einem Pfad (z. b. `-o BlazorSample`) ein.</span><span class="sxs-lookup"><span data-stu-id="18ff5-128">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="18ff5-129">Der Name des Ordners wird auch Teil des Projekt namens.</span><span class="sxs-lookup"><span data-stu-id="18ff5-129">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="18ff5-130">Nachdem Sie die App erstellt haben, sollten Sie folgende Aktionen ausführen können:</span><span class="sxs-lookup"><span data-stu-id="18ff5-130">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="18ff5-131">Melden Sie sich mit einem Microsoft-Konto bei der APP an.</span><span class="sxs-lookup"><span data-stu-id="18ff5-131">Log into the app using a Microsoft Account.</span></span>
* <span data-ttu-id="18ff5-132">Anfordern von Zugriffs Token für Microsoft-APIs mit dem gleichen Ansatz wie für eigenständige Blazor-apps, vorausgesetzt, dass Sie die APP ordnungsgemäß konfiguriert haben.</span><span class="sxs-lookup"><span data-stu-id="18ff5-132">Request access tokens for Microsoft APIs using the same approach as for standalone Blazor apps provided that you have configured the app correctly.</span></span> <span data-ttu-id="18ff5-133">Weitere Informationen finden Sie unter [Schnellstart: Konfigurieren einer Anwendung für die](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)Bereitstellung von Web-APIs.</span><span class="sxs-lookup"><span data-stu-id="18ff5-133">For more information, see [Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="18ff5-134">Authentifizierungs Paket</span><span class="sxs-lookup"><span data-stu-id="18ff5-134">Authentication package</span></span>

<span data-ttu-id="18ff5-135">Wenn eine APP für die Verwendung von Geschäfts-, Schul-oder unikonten (`SingleOrg`) erstellt wird, erhält die APP automatisch einen Paket Verweis für die [Microsoft-Authentifizierungs Bibliothek](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="18ff5-135">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="18ff5-136">Das Paket enthält eine Reihe von primitiven, die der APP helfen, Benutzer zu authentifizieren und Token abzurufen, um geschützte APIs aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="18ff5-136">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="18ff5-137">Wenn Sie eine Authentifizierung zu einer APP hinzufügen, fügen Sie das Paket manuell zur Projektdatei der APP hinzu:</span><span class="sxs-lookup"><span data-stu-id="18ff5-137">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="18ff5-138">Ersetzen Sie `{VERSION}` im vorangehenden Paket Verweis durch die Version des `Microsoft.AspNetCore.Blazor.Templates` Pakets, das im <xref:blazor/get-started> Artikel angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="18ff5-138">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="18ff5-139">Das `Microsoft.Authentication.WebAssembly.Msal` Paket fügt das `Microsoft.AspNetCore.Components.WebAssembly.Authentication` Paket transitiv der APP hinzu.</span><span class="sxs-lookup"><span data-stu-id="18ff5-139">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="18ff5-140">Unterstützung für Authentifizierungsdienst</span><span class="sxs-lookup"><span data-stu-id="18ff5-140">Authentication service support</span></span>

<span data-ttu-id="18ff5-141">Die Unterstützung für das Authentifizieren von Benutzern wird im Dienst Container mit der `AddMsalAuthentication`-Erweiterungsmethode registriert, die vom `Microsoft.Authentication.WebAssembly.Msal`-Paket bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="18ff5-141">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="18ff5-142">Diese Methode richtet alle Dienste ein, die erforderlich sind, damit die APP mit dem Identitäts Anbieter (IP) interagieren muss.</span><span class="sxs-lookup"><span data-stu-id="18ff5-142">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="18ff5-143">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="18ff5-143">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "{AUTHORITY}";
    authentication.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="18ff5-144">Die `AddMsalAuthentication`-Methode akzeptiert einen Rückruf, um die Parameter zu konfigurieren, die zum Authentifizieren einer APP erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="18ff5-144">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="18ff5-145">Die für die Konfiguration der APP erforderlichen Werte können bei der Registrierung der APP aus der Microsoft-Konto Konfiguration abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="18ff5-145">The values required for configuring the app can be obtained from the Microsoft Accounts configuration when you register the app.</span></span>

## <a name="index-page"></a><span data-ttu-id="18ff5-146">Indexseite</span><span class="sxs-lookup"><span data-stu-id="18ff5-146">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="18ff5-147">App-Komponente</span><span class="sxs-lookup"><span data-stu-id="18ff5-147">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="18ff5-148">Redirecttologin-Komponente</span><span class="sxs-lookup"><span data-stu-id="18ff5-148">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="18ff5-149">Logindisplay-Komponente</span><span class="sxs-lookup"><span data-stu-id="18ff5-149">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="18ff5-150">Authentifizierungs Komponente</span><span class="sxs-lookup"><span data-stu-id="18ff5-150">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="18ff5-151">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="18ff5-151">Additional resources</span></span>

* [<span data-ttu-id="18ff5-152">Schnellstart: Registrieren einer Anwendung bei der Microsoft Identity-Plattform</span><span class="sxs-lookup"><span data-stu-id="18ff5-152">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="18ff5-153">Schnellstart: Konfigurieren einer Anwendung für die Bereitstellung von Web-APIs</span><span class="sxs-lookup"><span data-stu-id="18ff5-153">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)

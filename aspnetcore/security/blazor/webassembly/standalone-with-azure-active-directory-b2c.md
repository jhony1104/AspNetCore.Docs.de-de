---
title: Sichern eines ASP.net Core Blazor eigenständigen Webassembly-App mit Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 0ea42943c908d8cf9d083c1cfc568c1835588ce9
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083656"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="31b9d-102">Sichern eines ASP.net Core Blazor eigenständigen Webassembly-App mit Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="31b9d-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="31b9d-103">Von [Javier calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="31b9d-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="31b9d-104">So erstellen Sie eine eigenständige Blazor Webassembly-APP, die [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) für die Authentifizierung verwendet:</span><span class="sxs-lookup"><span data-stu-id="31b9d-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

1. <span data-ttu-id="31b9d-105">Befolgen Sie die Anweisungen in den folgenden Themen, um einen Mandanten zu erstellen und eine Web-App im Azure-Portal zu registrieren:</span><span class="sxs-lookup"><span data-stu-id="31b9d-105">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

   * <span data-ttu-id="31b9d-106">[Erstellen Sie einen Aad B2C](/azure/active-directory-b2c/tutorial-create-tenant) Mandanten &ndash; notieren Sie die folgenden Informationen:</span><span class="sxs-lookup"><span data-stu-id="31b9d-106">[Create an AAD B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; Record the following information:</span></span>

     <span data-ttu-id="31b9d-107">1 \.</span><span class="sxs-lookup"><span data-stu-id="31b9d-107">1\.</span></span> <span data-ttu-id="31b9d-108">Aad B2C Instanz (z. b. `https://contoso.b2clogin.com/`, einschließlich des nachgestellten Schrägstrichs)</span><span class="sxs-lookup"><span data-stu-id="31b9d-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span><br>
     <span data-ttu-id="31b9d-109">2 \.</span><span class="sxs-lookup"><span data-stu-id="31b9d-109">2\.</span></span> <span data-ttu-id="31b9d-110">Aad B2C Mandanten Domäne (z. b. `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="31b9d-110">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

   * <span data-ttu-id="31b9d-111">[Registrieren Sie eine Webanwendung](/azure/active-directory-b2c/tutorial-register-applications) &ndash; nehmen Sie bei der APP-Registrierung folgende Auswahl vor:</span><span class="sxs-lookup"><span data-stu-id="31b9d-111">[Register a web application](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Make the following selections during app registration:</span></span>

     <span data-ttu-id="31b9d-112">1 \.</span><span class="sxs-lookup"><span data-stu-id="31b9d-112">1\.</span></span> <span data-ttu-id="31b9d-113">Legen Sie **Web-App/Web-API** auf **Ja**fest.</span><span class="sxs-lookup"><span data-stu-id="31b9d-113">Set **Web App / Web API** to **Yes**.</span></span><br>
     <span data-ttu-id="31b9d-114">2 \.</span><span class="sxs-lookup"><span data-stu-id="31b9d-114">2\.</span></span> <span data-ttu-id="31b9d-115">Legen Sie **implizites Flow zulassen** auf **Ja**fest.</span><span class="sxs-lookup"><span data-stu-id="31b9d-115">Set **Allow implicit flow** to **Yes**.</span></span><br>
     <span data-ttu-id="31b9d-116">3 \.</span><span class="sxs-lookup"><span data-stu-id="31b9d-116">3\.</span></span> <span data-ttu-id="31b9d-117">Fügen Sie eine **Antwort-URL** `https://localhost:5001/authentication/login-callback`hinzu.</span><span class="sxs-lookup"><span data-stu-id="31b9d-117">Add a **Reply URL** of `https://localhost:5001/authentication/login-callback`.</span></span>

     <span data-ttu-id="31b9d-118">Notieren Sie die Anwendungs-ID (Client-ID) (z. b. `11111111-1111-1111-1111-111111111111`).</span><span class="sxs-lookup"><span data-stu-id="31b9d-118">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

   * <span data-ttu-id="31b9d-119">[Erstellen von benutzerflows](/azure/active-directory-b2c/tutorial-create-user-flows) & ndash; Erstellen Sie einen Registrierungs-und Anmelde Benutzer Fluss.</span><span class="sxs-lookup"><span data-stu-id="31b9d-119">[Create user flows](/azure/active-directory-b2c/tutorial-create-user-flows) & ndash; Create a sign-up and sign-in user flow.</span></span>

     <span data-ttu-id="31b9d-120">Zeichnen Sie den für die APP erstellten Registrierungs-und Anmeldedaten Flussnamen auf (z. b. `B2C_1_signupsignin`).</span><span class="sxs-lookup"><span data-stu-id="31b9d-120">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

1. <span data-ttu-id="31b9d-121">Ersetzen Sie die Platzhalter im folgenden Befehl durch die zuvor aufgezeichneten Informationen, und führen Sie den Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="31b9d-121">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   <span data-ttu-id="31b9d-122">Um den Ausgabe Speicherort anzugeben, der einen Projektordner erstellt, wenn er nicht vorhanden ist, fügen Sie die Output-Option im Befehl mit einem Pfad (z. b. `-o BlazorSample`) ein.</span><span class="sxs-lookup"><span data-stu-id="31b9d-122">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="31b9d-123">Der Name des Ordners wird auch Teil des Projekt namens.</span><span class="sxs-lookup"><span data-stu-id="31b9d-123">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="31b9d-124">Authentifizierungs Paket</span><span class="sxs-lookup"><span data-stu-id="31b9d-124">Authentication package</span></span>

<span data-ttu-id="31b9d-125">Wenn eine App erstellt wird, um ein einzelnes B2C-Konto (`IndividualB2C`) zu verwenden, erhält die APP automatisch einen Paket Verweis für die [Microsoft-Authentifizierungs Bibliothek](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="31b9d-125">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="31b9d-126">Das Paket enthält eine Reihe von primitiven, die der APP helfen, Benutzer zu authentifizieren und Token abzurufen, um geschützte APIs aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="31b9d-126">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="31b9d-127">Wenn Sie eine Authentifizierung zu einer APP hinzufügen, fügen Sie das Paket manuell zur Projektdatei der APP hinzu:</span><span class="sxs-lookup"><span data-stu-id="31b9d-127">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="31b9d-128">Ersetzen Sie `{VERSION}` im vorangehenden Paket Verweis durch die Version des `Microsoft.AspNetCore.Blazor.Templates` Pakets, das im <xref:blazor/get-started> Artikel angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="31b9d-128">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="31b9d-129">Das `Microsoft.Authentication.WebAssembly.Msal` Paket fügt das `Microsoft.AspNetCore.Components.WebAssembly.Authentication` Paket transitiv der APP hinzu.</span><span class="sxs-lookup"><span data-stu-id="31b9d-129">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="31b9d-130">Unterstützung für Authentifizierungsdienst</span><span class="sxs-lookup"><span data-stu-id="31b9d-130">Authentication service support</span></span>

<span data-ttu-id="31b9d-131">Die Unterstützung für das Authentifizieren von Benutzern wird im Dienst Container mit der `AddMsalAuthentication`-Erweiterungsmethode registriert, die vom `Microsoft.Authentication.WebAssembly.Msal`-Paket bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="31b9d-131">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="31b9d-132">Diese Methode richtet alle Dienste ein, die erforderlich sind, damit die APP mit dem Identitäts Anbieter (IP) interagieren muss.</span><span class="sxs-lookup"><span data-stu-id="31b9d-132">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="31b9d-133">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="31b9d-133">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
});
```

<span data-ttu-id="31b9d-134">Die `AddMsalAuthentication`-Methode akzeptiert einen Rückruf, um die Parameter zu konfigurieren, die zum Authentifizieren einer APP erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="31b9d-134">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="31b9d-135">Die für die Konfiguration der APP erforderlichen Werte können aus der Aad-Konfiguration des Azure-Portals abgerufen werden, wenn Sie die APP registrieren.</span><span class="sxs-lookup"><span data-stu-id="31b9d-135">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="31b9d-136">Die Vorlage Blazor Webassembly konfiguriert die APP nicht automatisch so, dass Sie ein Zugriffs Token für eine sichere API anfordert.</span><span class="sxs-lookup"><span data-stu-id="31b9d-136">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="31b9d-137">Fügen Sie zum Bereitstellen eines Tokens als Teil des Anmelde Flusses den Gültigkeitsbereich zu den standardzugriffstokenbereichen der `MsalProviderOptions`hinzu:</span><span class="sxs-lookup"><span data-stu-id="31b9d-137">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{API ID URI}/{SCOPE}");
});
```

## <a name="index-page"></a><span data-ttu-id="31b9d-138">Indexseite</span><span class="sxs-lookup"><span data-stu-id="31b9d-138">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

## <a name="app-component"></a><span data-ttu-id="31b9d-139">App-Komponente</span><span class="sxs-lookup"><span data-stu-id="31b9d-139">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="31b9d-140">Redirecttologin-Komponente</span><span class="sxs-lookup"><span data-stu-id="31b9d-140">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="31b9d-141">Logindisplay-Komponente</span><span class="sxs-lookup"><span data-stu-id="31b9d-141">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="31b9d-142">Authentifizierungs Komponente</span><span class="sxs-lookup"><span data-stu-id="31b9d-142">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="31b9d-143">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="31b9d-143">Additional resources</span></span>

* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="31b9d-144">Tutorial zum Erstellen eines Azure Active Directory B2C-Mandanten</span><span class="sxs-lookup"><span data-stu-id="31b9d-144">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
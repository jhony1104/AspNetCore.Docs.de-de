---
title: Sichern einer Blazor ASP.NET eigenständigen Core WebAssembly-App mit Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 0734bad2d4281eb856783a362ef8c608a303c17a
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977053"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="738a2-102">Sichern einer Blazor ASP.NET eigenständigen Core WebAssembly-App mit Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="738a2-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="738a2-103">Von [Javier Calvarro Nelson](https://github.com/javiercn) und Luke [Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="738a2-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="738a2-104">So erstellen Blazor Sie eine eigenständige WebAssembly-App, die [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) für die Authentifizierung verwendet:</span><span class="sxs-lookup"><span data-stu-id="738a2-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

1. <span data-ttu-id="738a2-105">Befolgen Sie die Anleitung in den folgenden Themen, um einen Mandanten zu erstellen und eine Web-App im Azure-Portal zu registrieren:</span><span class="sxs-lookup"><span data-stu-id="738a2-105">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

   * <span data-ttu-id="738a2-106">[Erstellen eines AAD B2C-Mandanten](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; Zeichnen Sie die folgenden Informationen auf:</span><span class="sxs-lookup"><span data-stu-id="738a2-106">[Create an AAD B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; Record the following information:</span></span>

     <span data-ttu-id="738a2-107">1\.</span><span class="sxs-lookup"><span data-stu-id="738a2-107">1\.</span></span> <span data-ttu-id="738a2-108">AAD B2C-Instanz (z. B. `https://contoso.b2clogin.com/`, die den nachfolgenden Schrägstrich enthält)</span><span class="sxs-lookup"><span data-stu-id="738a2-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span><br>
     <span data-ttu-id="738a2-109">2\.</span><span class="sxs-lookup"><span data-stu-id="738a2-109">2\.</span></span> <span data-ttu-id="738a2-110">AAD B2C Mandantendomäne (z. B. `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="738a2-110">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

   * <span data-ttu-id="738a2-111">[Registrieren einer Webanwendung](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Treffen Sie während der App-Registrierung die folgenden Auswahlen:</span><span class="sxs-lookup"><span data-stu-id="738a2-111">[Register a web application](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Make the following selections during app registration:</span></span>

     <span data-ttu-id="738a2-112">1\.</span><span class="sxs-lookup"><span data-stu-id="738a2-112">1\.</span></span> <span data-ttu-id="738a2-113">Legen Sie **Web App / Web API** auf **Yes**fest.</span><span class="sxs-lookup"><span data-stu-id="738a2-113">Set **Web App / Web API** to **Yes**.</span></span><br>
     <span data-ttu-id="738a2-114">2\.</span><span class="sxs-lookup"><span data-stu-id="738a2-114">2\.</span></span> <span data-ttu-id="738a2-115">Festlegen **impliziten Fluss** zulassen auf **Ja**.</span><span class="sxs-lookup"><span data-stu-id="738a2-115">Set **Allow implicit flow** to **Yes**.</span></span><br>
     <span data-ttu-id="738a2-116">3\.</span><span class="sxs-lookup"><span data-stu-id="738a2-116">3\.</span></span> <span data-ttu-id="738a2-117">Fügen Sie eine `https://localhost:5001/authentication/login-callback` **Antwort-URL** von hinzu.</span><span class="sxs-lookup"><span data-stu-id="738a2-117">Add a **Reply URL** of `https://localhost:5001/authentication/login-callback`.</span></span>

     <span data-ttu-id="738a2-118">Zeichnen Sie die Anwendungs-ID (Client-ID) auf (z. B. `11111111-1111-1111-1111-111111111111`).</span><span class="sxs-lookup"><span data-stu-id="738a2-118">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

   * <span data-ttu-id="738a2-119">[Benutzerflüsse](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; erstellen Erstellen Sie einen Anmelde- und Anmeldebenutzerfluss.</span><span class="sxs-lookup"><span data-stu-id="738a2-119">[Create user flows](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; Create a sign-up and sign-in user flow.</span></span>

     <span data-ttu-id="738a2-120">Wählen Sie mindestens das Benutzerattribut > **"Anwendungsname anzeigen"** aus, um `context.User.Identity.Name` das in der `LoginDisplay` Komponente (*Shared/LoginDisplay.razor*) aufzufüllen. **Application claims**</span><span class="sxs-lookup"><span data-stu-id="738a2-120">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

     <span data-ttu-id="738a2-121">Zeichnen Sie den für die App erstellten Anmelde- und `B2C_1_signupsignin`Anmeldebenutzerflussnamen auf (z. B. ).</span><span class="sxs-lookup"><span data-stu-id="738a2-121">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

1. <span data-ttu-id="738a2-122">Ersetzen Sie die Platzhalter im folgenden Befehl durch die zuvor aufgezeichneten Informationen, und führen Sie den Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="738a2-122">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   <span data-ttu-id="738a2-123">Um den Ausgabespeicherort anzugeben, der einen Projektordner erstellt, falls dieser nicht vorhanden ist, fügen `-o BlazorSample`Sie die Ausgabeoption in den Befehl mit einem Pfad ein (z. B. ).</span><span class="sxs-lookup"><span data-stu-id="738a2-123">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="738a2-124">Der Ordnername wird auch Teil des Projektnamens.</span><span class="sxs-lookup"><span data-stu-id="738a2-124">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="738a2-125">Authentifizierungspaket</span><span class="sxs-lookup"><span data-stu-id="738a2-125">Authentication package</span></span>

<span data-ttu-id="738a2-126">Wenn eine App erstellt wird, um ein`IndividualB2C`individuelles B2C-Konto ( )`Microsoft.Authentication.WebAssembly.Msal`zu verwenden, erhält die App automatisch eine Paketreferenz für die [Microsoft-Authentifizierungsbibliothek](/azure/active-directory/develop/msal-overview) ( ).</span><span class="sxs-lookup"><span data-stu-id="738a2-126">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="738a2-127">Das Paket stellt eine Reihe von Primitiven bereit, die der App helfen, Benutzer zu authentifizieren und Token zum Aufrufen geschützter APIs zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="738a2-127">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="738a2-128">Wenn Sie einer App eine Authentifizierung hinzufügen, fügen Sie das Paket manuell zur Projektdatei der App hinzu:</span><span class="sxs-lookup"><span data-stu-id="738a2-128">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="738a2-129">Ersetzen `{VERSION}` Sie im vorherigen Paketverweis `Microsoft.AspNetCore.Blazor.Templates` durch die <xref:blazor/get-started> im Artikel gezeigte Version des Pakets.</span><span class="sxs-lookup"><span data-stu-id="738a2-129">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="738a2-130">Das `Microsoft.Authentication.WebAssembly.Msal` Paket fügt das `Microsoft.AspNetCore.Components.WebAssembly.Authentication` Paket transitiv zur App hinzu.</span><span class="sxs-lookup"><span data-stu-id="738a2-130">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="738a2-131">Unterstützung des Authentifizierungsdienstes</span><span class="sxs-lookup"><span data-stu-id="738a2-131">Authentication service support</span></span>

<span data-ttu-id="738a2-132">Unterstützung für die Authentifizierung von Benutzern wird `AddMsalAuthentication` im Dienstcontainer `Microsoft.Authentication.WebAssembly.Msal` mit der vom Paket bereitgestellten Erweiterungsmethode registriert.</span><span class="sxs-lookup"><span data-stu-id="738a2-132">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="738a2-133">Diese Methode richtet alle Dienste ein, die für die Interaktion der App mit dem Identitätsanbieter (IDENTITY Provider, IP) erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="738a2-133">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="738a2-134">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="738a2-134">*Program.cs*:</span></span>

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

<span data-ttu-id="738a2-135">Die `AddMsalAuthentication` Methode akzeptiert einen Rückruf, um die Parameter zu konfigurieren, die zum Authentifizieren einer App erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="738a2-135">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="738a2-136">Die zum Konfigurieren der App erforderlichen Werte können bei der Registrierung der App über die Azure Portal AAD-Konfiguration abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="738a2-136">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="738a2-137">Zugriffstokenbereiche</span><span class="sxs-lookup"><span data-stu-id="738a2-137">Access token scopes</span></span>

<span data-ttu-id="738a2-138">Die Blazor WebAssembly-Vorlage konfiguriert die App nicht automatisch, um ein Zugriffstoken für eine sichere API anzufordern.</span><span class="sxs-lookup"><span data-stu-id="738a2-138">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="738a2-139">Um ein Token als Teil des Anmeldeflusses zu bereitstellen, fügen Sie den `MsalProviderOptions`Bereich den Standardzugriffstokenbereichen der folgenden Werte hinzu:</span><span class="sxs-lookup"><span data-stu-id="738a2-139">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="738a2-140">Wenn das Azure-Portal einen Bereichs-URI bereitstellt und **die App eine nicht behandelte Ausnahme auslöst,** wenn sie eine *401 nicht autorisierte* Antwort von der API empfängt, versuchen Sie, einen Bereichs-URI zu verwenden, der das Schema und den Host nicht enthält.</span><span class="sxs-lookup"><span data-stu-id="738a2-140">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="738a2-141">Das Azure-Portal kann z. B. eines der folgenden Bereichs-URI-Formate bereitstellen:</span><span class="sxs-lookup"><span data-stu-id="738a2-141">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="738a2-142">Geben Sie den Bereichs-URI ohne Schema und Host:</span><span class="sxs-lookup"><span data-stu-id="738a2-142">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="738a2-143">Weitere Informationen finden Sie unter <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="738a2-143">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

## <a name="imports-file"></a><span data-ttu-id="738a2-144">Imports-Datei</span><span class="sxs-lookup"><span data-stu-id="738a2-144">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="738a2-145">Indexseite</span><span class="sxs-lookup"><span data-stu-id="738a2-145">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="738a2-146">App-Komponente</span><span class="sxs-lookup"><span data-stu-id="738a2-146">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="738a2-147">RedirectToLogin-Komponente</span><span class="sxs-lookup"><span data-stu-id="738a2-147">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="738a2-148">LoginDisplay-Komponente</span><span class="sxs-lookup"><span data-stu-id="738a2-148">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="738a2-149">Authentifizierungskomponente</span><span class="sxs-lookup"><span data-stu-id="738a2-149">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="738a2-150">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="738a2-150">Additional resources</span></span>

* [<span data-ttu-id="738a2-151">Zusätzliche Zugriffstoken anfordern</span><span class="sxs-lookup"><span data-stu-id="738a2-151">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="738a2-152">Tutorial zum Erstellen eines Azure Active Directory B2C-Mandanten</span><span class="sxs-lookup"><span data-stu-id="738a2-152">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="738a2-153">Dokumentation zur Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="738a2-153">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)

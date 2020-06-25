---
title: Sichern einer eigenständigen Blazor WebAssembly-App in ASP.NET Core mit Azure Active Directory B2C
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
uid: blazor/security/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 2229483acc8c3a3fd6e77848e307253b7b317365
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103279"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="50192-102">Sichern einer eigenständigen Blazor WebAssembly-App in ASP.NET Core mit Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="50192-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="50192-103">Von [Javier Calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="50192-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="50192-104">Für das Erstellen einer eigenständigen Blazor WebAssembly-App, die [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) für die Authentifizierung verwendet, gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="50192-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

<span data-ttu-id="50192-105">Befolgen Sie die Anweisungen in den folgenden Themen, um einen Mandanten zu erstellen und eine Web-App im Azure-Portal zu registrieren:</span><span class="sxs-lookup"><span data-stu-id="50192-105">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

[<span data-ttu-id="50192-106">Erstellen eines AAD B2C-Mandanten</span><span class="sxs-lookup"><span data-stu-id="50192-106">Create an AAD B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)

<span data-ttu-id="50192-107">Notieren Sie sich folgende Informationen:</span><span class="sxs-lookup"><span data-stu-id="50192-107">Record the following information:</span></span>

* <span data-ttu-id="50192-108">AAD B2C-Instanz (z. B. `https://contoso.b2clogin.com/`, wobei der nachgestellte Schrägstrich enthalten ist)</span><span class="sxs-lookup"><span data-stu-id="50192-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash).</span></span>
* <span data-ttu-id="50192-109">Domäne des AAD B2C-Mandanten (z. B. `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="50192-109">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="50192-110">Befolgen Sie die Anweisungen unter [Tutorial: Registrieren einer Webanwendung in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) noch mal, um eine AAD-App für die *Client-App* zu registrieren, und gehen Sie dann wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="50192-110">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* and then do the following:</span></span>

1. <span data-ttu-id="50192-111">Klicken Sie unter **Azure Active Directory** > **App-Registrierungen** auf **Neue Registrierung**.</span><span class="sxs-lookup"><span data-stu-id="50192-111">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="50192-112">Geben Sie einen **Namen** für die App an (z. B. **Blazor Standalone AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="50192-112">Provide a **Name** for the app (for example, **Blazor Standalone AAD B2C**).</span></span>
1. <span data-ttu-id="50192-113">Wählen Sie für **Unterstützte Kontotypen** die Option für mehrere Mandanten aus: **Konten in einem beliebigen Organisationsverzeichnis oder ein beliebiger Identitätsanbieter Wählen Sie diese Option aus, um Benutzer bei Azure AD B2C zu authentifizieren.**</span><span class="sxs-lookup"><span data-stu-id="50192-113">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="50192-114">Belassen Sie die Dropdownauswahl für **Umleitungs-URI** auf **Web**, und geben Sie den folgenden Umleitungs-URI an: `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="50192-114">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="50192-115">Der Standardport für eine App, die auf Kestrel ausgeführt wird, ist 5001.</span><span class="sxs-lookup"><span data-stu-id="50192-115">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="50192-116">Wenn die App auf einem anderen Kestrel-Port ausgeführt wird, verwenden Sie den Port der App.</span><span class="sxs-lookup"><span data-stu-id="50192-116">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="50192-117">Für IIS Express finden Sie den zufällig generierten Port für die App in den Eigenschaften der App im Panel **Debuggen**.</span><span class="sxs-lookup"><span data-stu-id="50192-117">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="50192-118">Da die App an dieser Stelle noch nicht existiert und der IIS Express-Port unbekannt ist, kehren Sie nach dem Erstellen der App zu diesem Schritt zurück, und aktualisieren Sie den Umleitungs-URI.</span><span class="sxs-lookup"><span data-stu-id="50192-118">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="50192-119">Weiter unten in diesem Artikel erscheint ein Hinweis, um IIS Express-Benutzer an die Aktualisierung des Umleitungs-URIs zu erinnern.</span><span class="sxs-lookup"><span data-stu-id="50192-119">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="50192-120">Vergewissern Sie sich, dass die Option **Berechtigungen** > **Administratoreinwilligung für openid- und offline_access-Berechtigungen erteilen** aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="50192-120">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="50192-121">Wählen Sie **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="50192-121">Select **Register**.</span></span>

<span data-ttu-id="50192-122">Notieren Sie sich die Anwendungs-ID (Client-ID) (z. B. `11111111-1111-1111-1111-111111111111`).</span><span class="sxs-lookup"><span data-stu-id="50192-122">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="50192-123">Gehen Sie unter **Authentifizierung** > **Plattformkonfigurationen** > **Web** wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="50192-123">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="50192-124">Vergewissern Sie sich, dass der **Umleitungs-URI** von `https://localhost:{PORT}/authentication/login-callback` vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="50192-124">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="50192-125">Aktivieren Sie für **Implizite Gewährung** die Kontrollkästchen **Zugriffstoken** und **ID-Token**.</span><span class="sxs-lookup"><span data-stu-id="50192-125">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="50192-126">Die verbleibenden Standardwerte für die App müssen für dieses Szenario nicht angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="50192-126">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="50192-127">Klicken Sie auf die Schaltfläche **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="50192-127">Select the **Save** button.</span></span>

<span data-ttu-id="50192-128">Gehen Sie unter **Startseite** > **Azure AD B2C** > **Benutzerflows** wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="50192-128">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="50192-129">Erstellen Sie einen Benutzerflow für die Registrierung und Anmeldung</span><span class="sxs-lookup"><span data-stu-id="50192-129">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="50192-130">Wählen Sie mindestens das Benutzerattribut **Anwendungsansprüche** > **Anzeigename** aus, um `context.User.Identity.Name` in der `LoginDisplay`-Komponente (*Shared/LoginDisplay.razor*) auszufüllen.</span><span class="sxs-lookup"><span data-stu-id="50192-130">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="50192-131">Notieren Sie sich den Namen des für die App erstellten Benutzerflows für die Registrierung und Anmeldung (z. B. `B2C_1_signupsignin`).</span><span class="sxs-lookup"><span data-stu-id="50192-131">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

<span data-ttu-id="50192-132">Ersetzen Sie die Platzhalter im folgenden Befehl durch die zuvor notierten Informationen, und führen Sie den Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="50192-132">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{TENANT DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
```

<span data-ttu-id="50192-133">Zum Angeben eines Ausgabespeicherorts (wodurch ein Projektordner erstellt wird, falls noch nicht vorhanden) schließen Sie die Ausgabeoption mit einem Pfad (z. B. `-o BlazorSample`) in den Befehl ein.</span><span class="sxs-lookup"><span data-stu-id="50192-133">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="50192-134">Der Name des Ordners wird auch Teil des Projektnamens.</span><span class="sxs-lookup"><span data-stu-id="50192-134">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="50192-135">Im Azure-Portal ist der **Authentifizierung** > **Plattformkonfigurationen** > **Web** > **Umleitungs-URI** der App für Apps für Port 5001 konfiguriert, die auf dem Kestrel-Server mit Standardeinstellungen ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="50192-135">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="50192-136">Wenn die App auf einem zufälligen IIS Express-Port ausgeführt wird, finden Sie den Port der App in den Eigenschaften der App im Panel **Debuggen**.</span><span class="sxs-lookup"><span data-stu-id="50192-136">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="50192-137">Wenn der Port nicht zuvor mit dem bekannten Port der App konfiguriert wurde, kehren Sie zur App-Registrierung im Azure-Portal zurück, und aktualisieren Sie den Umleitungs-URI mit dem korrekten Port.</span><span class="sxs-lookup"><span data-stu-id="50192-137">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="50192-138">Nachdem Sie die App erstellt haben, sollten Sie folgende Aktionen durchführen können:</span><span class="sxs-lookup"><span data-stu-id="50192-138">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="50192-139">Anmelden bei der App mit einem AAD-Benutzerkonto</span><span class="sxs-lookup"><span data-stu-id="50192-139">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="50192-140">Anfordern von Zugriffstoken für Microsoft-APIs</span><span class="sxs-lookup"><span data-stu-id="50192-140">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="50192-141">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="50192-141">For more information, see:</span></span>
  * [<span data-ttu-id="50192-142">Zugriffstokenbereiche</span><span class="sxs-lookup"><span data-stu-id="50192-142">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="50192-143">[Schnellstart: Konfigurieren einer Anwendung für das Verfügbarmachen von Web-APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="50192-143">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="50192-144">Authentifizierungspaket</span><span class="sxs-lookup"><span data-stu-id="50192-144">Authentication package</span></span>

<span data-ttu-id="50192-145">Wenn eine App zum Verwenden eines B2C-Einzelkontos (`IndividualB2C`) erstellt wird, erhält die App automatisch einen Paketverweis für die [Authentifizierungsbibliothek von Microsoft](/azure/active-directory/develop/msal-overview) ([Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span><span class="sxs-lookup"><span data-stu-id="50192-145">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="50192-146">Das Paket stellt einige Primitive bereit, die der App beim Authentifizieren von Benutzern und beim Abrufen von Token zum Aufrufen geschützter APIs helfen.</span><span class="sxs-lookup"><span data-stu-id="50192-146">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="50192-147">Wenn Sie einer App eine Authentifizierung hinzufügen, fügen Sie das Paket manuell der Projektdatei der App hinzu:</span><span class="sxs-lookup"><span data-stu-id="50192-147">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="50192-148">Das [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)-Paket fügt der App das Paket [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) transitiv hinzu.</span><span class="sxs-lookup"><span data-stu-id="50192-148">The [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="50192-149">Unterstützung für Authentifizierungsdienste</span><span class="sxs-lookup"><span data-stu-id="50192-149">Authentication service support</span></span>

<span data-ttu-id="50192-150">Die Unterstützung für die Authentifizierung von Benutzern wird im Dienstcontainer mit der vom Paket [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) bereitgestellten <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>-Erweiterungsmethode registriert.</span><span class="sxs-lookup"><span data-stu-id="50192-150">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="50192-151">Diese Methode richtet alle Dienste ein, die erforderlich sind, damit die App mit dem Identitätsanbieter interagiert.</span><span class="sxs-lookup"><span data-stu-id="50192-151">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="50192-152">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="50192-152">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="50192-153">Die <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>-Methode akzeptiert einen Rückruf zum Konfigurieren der für die Authentifizierung der App erforderlichen Parameter.</span><span class="sxs-lookup"><span data-stu-id="50192-153">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="50192-154">Die zum Konfigurieren der App erforderlichen Werte können von der AAD-Konfiguration beim Registrieren der App abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="50192-154">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="50192-155">Die Konfiguration wird durch die Datei *wwwroot/appsettings.json* bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="50192-155">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="50192-156">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="50192-156">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": false
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="50192-157">Zugriffstokenbereiche</span><span class="sxs-lookup"><span data-stu-id="50192-157">Access token scopes</span></span>

<span data-ttu-id="50192-158">Die Blazor WebAssembly-Vorlage konfiguriert die App nicht automatisch so, dass diese ein Zugriffstoken für eine sichere API anfordert.</span><span class="sxs-lookup"><span data-stu-id="50192-158">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="50192-159">Zum Bereitstellen eines Zugriffstokens als Teil des Anmeldeflows fügen Sie den Bereich den Standard-Zugriffstokenbereichen von <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> hinzu:</span><span class="sxs-lookup"><span data-stu-id="50192-159">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="50192-160">Weitere Informationen finden Sie in den folgenden Abschnitten des Artikels zu *zusätzlichen Szenarios*:</span><span class="sxs-lookup"><span data-stu-id="50192-160">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="50192-161">Anfordern zusätzlicher Zugriffstoken</span><span class="sxs-lookup"><span data-stu-id="50192-161">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="50192-162">Anfügen von Token an ausgehende Anforderungen</span><span class="sxs-lookup"><span data-stu-id="50192-162">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="50192-163">Imports-Datei</span><span class="sxs-lookup"><span data-stu-id="50192-163">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="50192-164">Indexseite</span><span class="sxs-lookup"><span data-stu-id="50192-164">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="50192-165">App-Komponente</span><span class="sxs-lookup"><span data-stu-id="50192-165">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="50192-166">RedirectToLogin-Komponente</span><span class="sxs-lookup"><span data-stu-id="50192-166">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="50192-167">LoginDisplay-Komponente</span><span class="sxs-lookup"><span data-stu-id="50192-167">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="50192-168">Authentication-Komponente</span><span class="sxs-lookup"><span data-stu-id="50192-168">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="50192-169">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="50192-169">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="50192-170">Nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen in einer App mit einem sicheren Standardclient</span><span class="sxs-lookup"><span data-stu-id="50192-170">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="50192-171">Tutorial: Erstellen eines Azure Active Directory B2C-Mandanten</span><span class="sxs-lookup"><span data-stu-id="50192-171">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="50192-172">Dokumentation zu Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="50192-172">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)

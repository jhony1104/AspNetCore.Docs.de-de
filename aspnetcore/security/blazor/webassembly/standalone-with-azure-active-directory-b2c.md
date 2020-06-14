---
title: Sichern einer ASP.net Core Blazor eigenständigen Webassembly-App mit Azure Active Directory B2C
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
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: ec35614e3bc4b5b6422b254dfe579c1cb7ca8310
ms.sourcegitcommit: d243fadeda20ad4f142ea60301ae5f5e0d41ed60
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/12/2020
ms.locfileid: "84724392"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="795e3-102">Sichern einer ASP.net Core Blazor eigenständigen Webassembly-App mit Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="795e3-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="795e3-103">Von [Javier calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="795e3-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="795e3-104">So erstellen Sie eine Blazor eigenständige Webassembly-APP, die [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) für die Authentifizierung verwendet:</span><span class="sxs-lookup"><span data-stu-id="795e3-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

<span data-ttu-id="795e3-105">Befolgen Sie die Anweisungen in den folgenden Themen, um einen Mandanten zu erstellen und eine Web-App im Azure-Portal zu registrieren:</span><span class="sxs-lookup"><span data-stu-id="795e3-105">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

[<span data-ttu-id="795e3-106">Erstellen eines Aad B2C Mandanten</span><span class="sxs-lookup"><span data-stu-id="795e3-106">Create an AAD B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)

<span data-ttu-id="795e3-107">Notieren Sie sich die folgenden Informationen:</span><span class="sxs-lookup"><span data-stu-id="795e3-107">Record the following information:</span></span>

* <span data-ttu-id="795e3-108">Aad B2C Instanz (z. b `https://contoso.b2clogin.com/` ., die den nachgestellten Schrägstrich einschließt).</span><span class="sxs-lookup"><span data-stu-id="795e3-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash).</span></span>
* <span data-ttu-id="795e3-109">Aad B2C Mandanten Domäne (z. b `contoso.onmicrosoft.com` .).</span><span class="sxs-lookup"><span data-stu-id="795e3-109">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="795e3-110">Befolgen Sie die Anweisungen im [Tutorial: Registrieren einer Anwendung in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) erneut, um eine Aad-App für die *Client-App* zu registrieren, und gehen Sie dann wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="795e3-110">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* and then do the following:</span></span>

1. <span data-ttu-id="795e3-111">Wählen Sie in **Azure Active Directory**  >  **App-Registrierungen**die Option **neue Registrierung**aus.</span><span class="sxs-lookup"><span data-stu-id="795e3-111">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="795e3-112">Geben Sie einen **Namen** für die APP an (z. b. \*\* Blazor eigenständiges Aad B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="795e3-112">Provide a **Name** for the app (for example, **Blazor Standalone AAD B2C**).</span></span>
1. <span data-ttu-id="795e3-113">Wählen Sie für **unterstützte Konto Typen**die Option für mehrere Mandanten aus: **Konten in einem beliebigen Organisations Verzeichnis oder einem beliebigen Identitäts Anbieter. Zum Authentifizieren von Benutzern mit Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="795e3-113">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="795e3-114">Lassen Sie die Dropdown-Einstellung für **Umleitungs-URI** auf **Web** fest, und geben Sie folgenden Umleitungs-URI `https://localhost:{PORT}/authentication/login-callback`</span><span class="sxs-lookup"><span data-stu-id="795e3-114">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="795e3-115">Der Standardport für eine APP, die auf Kestrel ausgeführt wird, ist 5001.</span><span class="sxs-lookup"><span data-stu-id="795e3-115">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="795e3-116">Wenn die APP auf einem anderen Kestrel-Port ausgeführt wird, verwenden Sie den Port der app.</span><span class="sxs-lookup"><span data-stu-id="795e3-116">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="795e3-117">Für IIS Express befindet sich der zufällig generierte Port für die app in den Eigenschaften der APP im **Debug** -Panel.</span><span class="sxs-lookup"><span data-stu-id="795e3-117">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="795e3-118">Da die APP zu diesem Zeitpunkt noch nicht vorhanden ist und der IIS Express Port nicht bekannt ist, kehren Sie zu diesem Schritt zurück, nachdem die App erstellt wurde, und aktualisieren Sie den Umleitungs-URI.</span><span class="sxs-lookup"><span data-stu-id="795e3-118">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="795e3-119">Eine Anmerkung wird später in diesem Thema angezeigt, um IIS Express Benutzer daran zu erinnern, den Umleitungs-URI zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="795e3-119">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="795e3-120">Vergewissern Sie **sich, dass Berechtigungen**  >  **für OpenID und offline_access Berechtigungen Administrator Zustimmung erteilen** aktiviert sind.</span><span class="sxs-lookup"><span data-stu-id="795e3-120">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="795e3-121">Wählen Sie **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="795e3-121">Select **Register**.</span></span>

<span data-ttu-id="795e3-122">Notieren Sie sich die Anwendungs-ID (Client-ID) (z `11111111-1111-1111-1111-111111111111` . b.).</span><span class="sxs-lookup"><span data-stu-id="795e3-122">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="795e3-123">Im Web der **Authentifizierungs**  >  **Platt Form Konfigurationen**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="795e3-123">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="795e3-124">Bestätigen Sie, dass der **Umleitungs-URI** von `https://localhost:{PORT}/authentication/login-callback` vorhanden ist</span><span class="sxs-lookup"><span data-stu-id="795e3-124">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="795e3-125">Aktivieren Sie für **implizite Gewährung**die Kontrollkästchen für **Zugriffs Token** und **ID-Token**.</span><span class="sxs-lookup"><span data-stu-id="795e3-125">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="795e3-126">Die verbleibenden Standardwerte für die APP sind für diese Art von akzeptabel.</span><span class="sxs-lookup"><span data-stu-id="795e3-126">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="795e3-127">Wählen Sie die Schaltfläche **Speichern** aus.</span><span class="sxs-lookup"><span data-stu-id="795e3-127">Select the **Save** button.</span></span>

<span data-ttu-id="795e3-128">In der **Start**  >  **Azure AD B2C**  >  **benutzerflows**:</span><span class="sxs-lookup"><span data-stu-id="795e3-128">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="795e3-129">Erstellen eines Benutzerflows für Registrierung und Anmeldung</span><span class="sxs-lookup"><span data-stu-id="795e3-129">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="795e3-130">Wählen Sie mindestens das Benutzer Attribut **Anwendungs Anspruchs**  >  **Anzeige Name** aus, um das `context.User.Identity.Name` in der `LoginDisplay` Komponente (*Shared/logindisplay. Razor*) aufzufüllen.</span><span class="sxs-lookup"><span data-stu-id="795e3-130">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="795e3-131">Zeichnen Sie den für die APP erstellten Registrierungs-und Anmeldedaten Flussnamen auf (z `B2C_1_signupsignin` . b.).</span><span class="sxs-lookup"><span data-stu-id="795e3-131">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

<span data-ttu-id="795e3-132">Ersetzen Sie die Platzhalter im folgenden Befehl durch die zuvor aufgezeichneten Informationen, und führen Sie den Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="795e3-132">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{TENANT DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
```

<span data-ttu-id="795e3-133">Um den Ausgabe Speicherort anzugeben, der einen Projektordner erstellt, wenn er nicht vorhanden ist, schließen Sie die Output-Option in den Befehl mit einem Pfad (z `-o BlazorSample` . b.) ein.</span><span class="sxs-lookup"><span data-stu-id="795e3-133">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="795e3-134">Der Name des Ordners wird auch Teil des Projekt namens.</span><span class="sxs-lookup"><span data-stu-id="795e3-134">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="795e3-135">Im Azure-Portal wird der **Authentication**  >  **Platform configurations**  >  **webumleitungs**-URI der Authentifizierungsplattform der APP  >  **Redirect URI** für apps, die auf dem Kestrel-Server mit Standardeinstellungen ausgeführt werden, für Port 5001 konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="795e3-135">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="795e3-136">Wenn die APP auf einem zufälligen IIS Express Port ausgeführt wird, finden Sie den Port für die app in den Eigenschaften der APP im **Debug** -Panel.</span><span class="sxs-lookup"><span data-stu-id="795e3-136">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="795e3-137">Wenn der Port nicht zuvor mit dem bekannten Port der App konfiguriert wurde, kehren Sie zur Registrierung der APP im Azure-Portal zurück, und aktualisieren Sie den Umleitungs-URI mit dem richtigen Port.</span><span class="sxs-lookup"><span data-stu-id="795e3-137">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="795e3-138">Nachdem Sie die App erstellt haben, sollten Sie folgende Aktionen ausführen können:</span><span class="sxs-lookup"><span data-stu-id="795e3-138">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="795e3-139">Melden Sie sich mit einem Aad-Benutzerkonto bei der APP an.</span><span class="sxs-lookup"><span data-stu-id="795e3-139">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="795e3-140">Anfordern von Zugriffs Token für Microsoft-APIs.</span><span class="sxs-lookup"><span data-stu-id="795e3-140">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="795e3-141">Weitere Informationen finden Sie in folgenden Quellen:</span><span class="sxs-lookup"><span data-stu-id="795e3-141">For more information, see:</span></span>
  * [<span data-ttu-id="795e3-142">Zugriffs Token-Bereiche</span><span class="sxs-lookup"><span data-stu-id="795e3-142">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="795e3-143">[Schnellstart: Konfigurieren einer Anwendung für die Bereitstellung von Web-APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="795e3-143">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="795e3-144">Authentifizierungs Paket</span><span class="sxs-lookup"><span data-stu-id="795e3-144">Authentication package</span></span>

<span data-ttu-id="795e3-145">Wenn eine APP für die Verwendung eines einzelnen B2C-Kontos ( `IndividualB2C` ) erstellt wird, erhält die APP automatisch einen Paket Verweis für die [Microsoft-Authentifizierungs Bibliothek](/azure/active-directory/develop/msal-overview) ([Microsoft. Authentication. Webassembly. msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span><span class="sxs-lookup"><span data-stu-id="795e3-145">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="795e3-146">Das Paket enthält eine Reihe von primitiven, die der APP helfen, Benutzer zu authentifizieren und Token abzurufen, um geschützte APIs aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="795e3-146">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="795e3-147">Wenn Sie eine Authentifizierung zu einer APP hinzufügen, fügen Sie das Paket manuell zur Projektdatei der APP hinzu:</span><span class="sxs-lookup"><span data-stu-id="795e3-147">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="795e3-148">Das [Microsoft. Authentication. Webassembly. msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) -Paket fügt transitiv das Paket [Microsoft. aspnetcore. Components. Webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) der APP hinzu.</span><span class="sxs-lookup"><span data-stu-id="795e3-148">The [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="795e3-149">Unterstützung für Authentifizierungsdienst</span><span class="sxs-lookup"><span data-stu-id="795e3-149">Authentication service support</span></span>

<span data-ttu-id="795e3-150">Die Unterstützung für das Authentifizieren von Benutzern wird im Dienst Container mit der <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> vom [Microsoft. Authentication. Webassembly. msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) -Paket bereitgestellten Erweiterungsmethode registriert.</span><span class="sxs-lookup"><span data-stu-id="795e3-150">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="795e3-151">Diese Methode richtet alle Dienste ein, die für die Interaktion der APP mit dem Identity Anbieter (IP) erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="795e3-151">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="795e3-152">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="795e3-152">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="795e3-153">Die- <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> Methode akzeptiert einen Rückruf, um die Parameter zu konfigurieren, die zum Authentifizieren einer APP erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="795e3-153">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="795e3-154">Die für die Konfiguration der APP erforderlichen Werte können aus der Aad-Konfiguration abgerufen werden, wenn Sie die APP registrieren.</span><span class="sxs-lookup"><span data-stu-id="795e3-154">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="795e3-155">Die Konfiguration wird durch die Datei *wwwroot/appsettings.jsin* der Datei bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="795e3-155">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="795e3-156">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="795e3-156">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": false
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="795e3-157">Zugriffs Token-Bereiche</span><span class="sxs-lookup"><span data-stu-id="795e3-157">Access token scopes</span></span>

<span data-ttu-id="795e3-158">Die Blazor webassemblyvorlage konfiguriert die APP nicht automatisch, um ein Zugriffs Token für eine sichere API anzufordern.</span><span class="sxs-lookup"><span data-stu-id="795e3-158">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="795e3-159">Fügen Sie zum Bereitstellen eines Zugriffs Tokens als Teil des Anmelde Flusses den Gültigkeitsbereich zu den standardzugriffstokenbereichen von hinzu <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="795e3-159">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="795e3-160">Weitere Informationen finden Sie in den folgenden Abschnitten des Artikels *weitere Szenarien* :</span><span class="sxs-lookup"><span data-stu-id="795e3-160">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="795e3-161">Anfordern zusätzlicher Zugriffstoken</span><span class="sxs-lookup"><span data-stu-id="795e3-161">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="795e3-162">Anfügen von Token an ausgehende Anforderungen</span><span class="sxs-lookup"><span data-stu-id="795e3-162">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="795e3-163">Import Datei</span><span class="sxs-lookup"><span data-stu-id="795e3-163">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="795e3-164">Indexseite</span><span class="sxs-lookup"><span data-stu-id="795e3-164">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="795e3-165">App-Komponente</span><span class="sxs-lookup"><span data-stu-id="795e3-165">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="795e3-166">Redirecttologin-Komponente</span><span class="sxs-lookup"><span data-stu-id="795e3-166">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="795e3-167">Logindisplay-Komponente</span><span class="sxs-lookup"><span data-stu-id="795e3-167">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="795e3-168">Authentifizierungs Komponente</span><span class="sxs-lookup"><span data-stu-id="795e3-168">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="795e3-169">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="795e3-169">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="795e3-170">Nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen in einer APP mit einem sicheren Standard Client</span><span class="sxs-lookup"><span data-stu-id="795e3-170">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="795e3-171">Tutorial zum Erstellen eines Azure Active Directory B2C-Mandanten</span><span class="sxs-lookup"><span data-stu-id="795e3-171">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="795e3-172">Dokumentation zur Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="795e3-172">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)

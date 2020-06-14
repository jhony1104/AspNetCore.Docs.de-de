---
title: Sichern einer ASP.net Core Blazor eigenständigen Webassembly-App mit Azure Active Directory
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
uid: security/blazor/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 6d8db8f546e21ccce27cca06b4112ebc52653782
ms.sourcegitcommit: d243fadeda20ad4f142ea60301ae5f5e0d41ed60
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/12/2020
ms.locfileid: "84724379"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="b4c6f-102">Sichern einer ASP.net Core Blazor eigenständigen Webassembly-App mit Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="b4c6f-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="b4c6f-103">Von [Javier calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b4c6f-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b4c6f-104">So erstellen Sie eine Blazor eigenständige Webassembly-APP, die [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) für die Authentifizierung verwendet:</span><span class="sxs-lookup"><span data-stu-id="b4c6f-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="b4c6f-105">[Erstellen Sie einen Aad-Mandanten und eine Webanwendung](/azure/active-directory/develop/v2-overview):</span><span class="sxs-lookup"><span data-stu-id="b4c6f-105">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="b4c6f-106">Registrieren Sie eine Aad-App im **Azure Active Directory**  >  **App-Registrierungen** Bereich der Azure-Portal:</span><span class="sxs-lookup"><span data-stu-id="b4c6f-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="b4c6f-107">Geben Sie einen **Namen** für die APP an (z. b. \*\* Blazor eigenständiges Aad\*\*).</span><span class="sxs-lookup"><span data-stu-id="b4c6f-107">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="b4c6f-108">Wählen Sie einen **unterstützten Kontotyp**aus.</span><span class="sxs-lookup"><span data-stu-id="b4c6f-108">Choose a **Supported account types**.</span></span> <span data-ttu-id="b4c6f-109">Sie können **Konten in diesem Organisations Verzeichnis nur** für dieses Verfahren auswählen.</span><span class="sxs-lookup"><span data-stu-id="b4c6f-109">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="b4c6f-110">Lassen Sie die Dropdown-Einstellung für **Umleitungs-URI** auf **Web** fest, und geben Sie folgenden Umleitungs-URI `https://localhost:{PORT}/authentication/login-callback`</span><span class="sxs-lookup"><span data-stu-id="b4c6f-110">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="b4c6f-111">Der Standardport für eine APP, die auf Kestrel ausgeführt wird, ist 5001.</span><span class="sxs-lookup"><span data-stu-id="b4c6f-111">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="b4c6f-112">Wenn die APP auf einem anderen Kestrel-Port ausgeführt wird, verwenden Sie den Port der app.</span><span class="sxs-lookup"><span data-stu-id="b4c6f-112">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="b4c6f-113">Für IIS Express befindet sich der zufällig generierte Port für die app in den Eigenschaften der APP im **Debug** -Panel.</span><span class="sxs-lookup"><span data-stu-id="b4c6f-113">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="b4c6f-114">Da die APP zu diesem Zeitpunkt noch nicht vorhanden ist und der IIS Express Port nicht bekannt ist, kehren Sie zu diesem Schritt zurück, nachdem die App erstellt wurde, und aktualisieren Sie den Umleitungs-URI.</span><span class="sxs-lookup"><span data-stu-id="b4c6f-114">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="b4c6f-115">Eine Anmerkung wird später in diesem Thema angezeigt, um IIS Express Benutzer daran zu erinnern, den Umleitungs-URI zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="b4c6f-115">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="b4c6f-116">Deaktivieren Sie **das**  >  Kontrollkästchen**Administrator Zustimmung für OpenID und offline_access Berechtigungen gewähren** .</span><span class="sxs-lookup"><span data-stu-id="b4c6f-116">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="b4c6f-117">Wählen Sie **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="b4c6f-117">Select **Register**.</span></span>

<span data-ttu-id="b4c6f-118">Notieren Sie sich die folgenden Informationen:</span><span class="sxs-lookup"><span data-stu-id="b4c6f-118">Record the following information:</span></span>

* <span data-ttu-id="b4c6f-119">Anwendungs-ID (Client-ID) (z. b. `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="b4c6f-119">Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="b4c6f-120">Verzeichnis-ID (Mandanten-ID) (z. b. `22222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="b4c6f-120">Directory ID (Tenant ID) (for example, `22222222-2222-2222-2222-222222222222`)</span></span>

<span data-ttu-id="b4c6f-121">Im Web der **Authentifizierungs**  >  **Platt Form Konfigurationen**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="b4c6f-121">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="b4c6f-122">Bestätigen Sie, dass der **Umleitungs-URI** von `https://localhost:{PORT}/authentication/login-callback` vorhanden ist</span><span class="sxs-lookup"><span data-stu-id="b4c6f-122">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="b4c6f-123">Aktivieren Sie für **implizite Gewährung**die Kontrollkästchen für **Zugriffs Token** und **ID-Token**.</span><span class="sxs-lookup"><span data-stu-id="b4c6f-123">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="b4c6f-124">Die verbleibenden Standardwerte für die APP sind für diese Art von akzeptabel.</span><span class="sxs-lookup"><span data-stu-id="b4c6f-124">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="b4c6f-125">Wählen Sie die Schaltfläche **Speichern** aus.</span><span class="sxs-lookup"><span data-stu-id="b4c6f-125">Select the **Save** button.</span></span>

<span data-ttu-id="b4c6f-126">Erstellen der App</span><span class="sxs-lookup"><span data-stu-id="b4c6f-126">Create the app.</span></span> <span data-ttu-id="b4c6f-127">Ersetzen Sie die Platzhalter im folgenden Befehl durch die zuvor aufgezeichneten Informationen, und führen Sie den Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="b4c6f-127">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="b4c6f-128">Um den Ausgabe Speicherort anzugeben, der einen Projektordner erstellt, wenn er nicht vorhanden ist, schließen Sie die Output-Option in den Befehl mit einem Pfad (z `-o BlazorSample` . b.) ein.</span><span class="sxs-lookup"><span data-stu-id="b4c6f-128">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="b4c6f-129">Der Name des Ordners wird auch Teil des Projekt namens.</span><span class="sxs-lookup"><span data-stu-id="b4c6f-129">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="b4c6f-130">Im Azure-Portal wird der **Authentication**  >  **Platform configurations**  >  **webumleitungs**-URI der Authentifizierungsplattform der APP  >  **Redirect URI** für apps, die auf dem Kestrel-Server mit Standardeinstellungen ausgeführt werden, für Port 5001 konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="b4c6f-130">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="b4c6f-131">Wenn die APP auf einem zufälligen IIS Express Port ausgeführt wird, finden Sie den Port für die app in den Eigenschaften der APP im **Debug** -Panel.</span><span class="sxs-lookup"><span data-stu-id="b4c6f-131">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="b4c6f-132">Wenn der Port nicht zuvor mit dem bekannten Port der App konfiguriert wurde, kehren Sie zur Registrierung der APP im Azure-Portal zurück, und aktualisieren Sie den Umleitungs-URI mit dem richtigen Port.</span><span class="sxs-lookup"><span data-stu-id="b4c6f-132">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="b4c6f-133">Nachdem Sie die App erstellt haben, sollten Sie folgende Aktionen ausführen können:</span><span class="sxs-lookup"><span data-stu-id="b4c6f-133">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="b4c6f-134">Melden Sie sich mit einem Aad-Benutzerkonto bei der APP an.</span><span class="sxs-lookup"><span data-stu-id="b4c6f-134">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="b4c6f-135">Anfordern von Zugriffs Token für Microsoft-APIs.</span><span class="sxs-lookup"><span data-stu-id="b4c6f-135">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="b4c6f-136">Weitere Informationen finden Sie in folgenden Quellen:</span><span class="sxs-lookup"><span data-stu-id="b4c6f-136">For more information, see:</span></span>
  * [<span data-ttu-id="b4c6f-137">Zugriffs Token-Bereiche</span><span class="sxs-lookup"><span data-stu-id="b4c6f-137">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="b4c6f-138">[Schnellstart: Konfigurieren einer Anwendung für die Bereitstellung von Web-APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="b4c6f-138">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="b4c6f-139">Authentifizierungs Paket</span><span class="sxs-lookup"><span data-stu-id="b4c6f-139">Authentication package</span></span>

<span data-ttu-id="b4c6f-140">Wenn eine APP für die Verwendung von Geschäfts-, Schul-oder unikonten () erstellt wird `SingleOrg` , erhält die APP automatisch einen Paket Verweis für die [Microsoft-Authentifizierungs Bibliothek](/azure/active-directory/develop/msal-overview) ([Microsoft. Authentication. Webassembly. msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span><span class="sxs-lookup"><span data-stu-id="b4c6f-140">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="b4c6f-141">Das Paket enthält eine Reihe von primitiven, die der APP helfen, Benutzer zu authentifizieren und Token abzurufen, um geschützte APIs aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="b4c6f-141">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="b4c6f-142">Wenn Sie eine Authentifizierung zu einer APP hinzufügen, fügen Sie das Paket manuell zur Projektdatei der APP hinzu:</span><span class="sxs-lookup"><span data-stu-id="b4c6f-142">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="b4c6f-143">Das [Microsoft. Authentication. Webassembly. msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) -Paket fügt transitiv das Paket [Microsoft. aspnetcore. Components. Webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) der APP hinzu.</span><span class="sxs-lookup"><span data-stu-id="b4c6f-143">The [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="b4c6f-144">Unterstützung für Authentifizierungsdienst</span><span class="sxs-lookup"><span data-stu-id="b4c6f-144">Authentication service support</span></span>

<span data-ttu-id="b4c6f-145">Die Unterstützung für das Authentifizieren von Benutzern wird im Dienst Container mit der <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> vom [Microsoft. Authentication. Webassembly. msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) -Paket bereitgestellten Erweiterungsmethode registriert.</span><span class="sxs-lookup"><span data-stu-id="b4c6f-145">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="b4c6f-146">Diese Methode richtet die Dienste ein, die für die Interaktion der APP mit dem Identity Anbieter (IP) erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="b4c6f-146">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="b4c6f-147">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="b4c6f-147">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="b4c6f-148">Die- <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> Methode akzeptiert einen Rückruf, um die Parameter zu konfigurieren, die zum Authentifizieren einer APP erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="b4c6f-148">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="b4c6f-149">Die für die Konfiguration der APP erforderlichen Werte können aus der Aad-Konfiguration abgerufen werden, wenn Sie die APP registrieren.</span><span class="sxs-lookup"><span data-stu-id="b4c6f-149">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="b4c6f-150">Die Konfiguration wird durch die Datei *wwwroot/appsettings.jsin* der Datei bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="b4c6f-150">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="b4c6f-151">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="b4c6f-151">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="b4c6f-152">Zugriffs Token-Bereiche</span><span class="sxs-lookup"><span data-stu-id="b4c6f-152">Access token scopes</span></span>

<span data-ttu-id="b4c6f-153">Die Blazor webassemblyvorlage konfiguriert die APP nicht automatisch, um ein Zugriffs Token für eine sichere API anzufordern.</span><span class="sxs-lookup"><span data-stu-id="b4c6f-153">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="b4c6f-154">Fügen Sie zum Bereitstellen eines Zugriffs Tokens als Teil des Anmelde Flusses den Gültigkeitsbereich zu den standardzugriffstokenbereichen von hinzu <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="b4c6f-154">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="b4c6f-155">Weitere Informationen finden Sie in den folgenden Abschnitten des Artikels *weitere Szenarien* :</span><span class="sxs-lookup"><span data-stu-id="b4c6f-155">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="b4c6f-156">Anfordern zusätzlicher Zugriffstoken</span><span class="sxs-lookup"><span data-stu-id="b4c6f-156">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="b4c6f-157">Anfügen von Token an ausgehende Anforderungen</span><span class="sxs-lookup"><span data-stu-id="b4c6f-157">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="b4c6f-158">Import Datei</span><span class="sxs-lookup"><span data-stu-id="b4c6f-158">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="b4c6f-159">Indexseite</span><span class="sxs-lookup"><span data-stu-id="b4c6f-159">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="b4c6f-160">App-Komponente</span><span class="sxs-lookup"><span data-stu-id="b4c6f-160">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="b4c6f-161">Redirecttologin-Komponente</span><span class="sxs-lookup"><span data-stu-id="b4c6f-161">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="b4c6f-162">Logindisplay-Komponente</span><span class="sxs-lookup"><span data-stu-id="b4c6f-162">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="b4c6f-163">Authentifizierungs Komponente</span><span class="sxs-lookup"><span data-stu-id="b4c6f-163">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="b4c6f-164">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="b4c6f-164">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="b4c6f-165">Nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen in einer APP mit einem sicheren Standard Client</span><span class="sxs-lookup"><span data-stu-id="b4c6f-165">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="b4c6f-166">Dokumentation zur Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="b4c6f-166">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)

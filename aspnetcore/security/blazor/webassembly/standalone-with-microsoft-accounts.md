---
title: Sichern einer Blazor eigenständigen Core WebAssembly-App mit ASP.NET Core WebAssembly mit Microsoft-Konten
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 8c409651b3338c2baeae497bef43b994823a20f9
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977079"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="cb864-102">Sichern einer Blazor eigenständigen Core WebAssembly-App mit ASP.NET Core WebAssembly mit Microsoft-Konten</span><span class="sxs-lookup"><span data-stu-id="cb864-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="cb864-103">Von [Javier Calvarro Nelson](https://github.com/javiercn) und Luke [Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="cb864-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="cb864-104">So erstellen Blazor Sie eine eigenständige WebAssembly-App, die [Microsoft-Konten mit Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) für die Authentifizierung verwendet:</span><span class="sxs-lookup"><span data-stu-id="cb864-104">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

1. [<span data-ttu-id="cb864-105">Erstellen eines AAD-Mandanten und einer Webanwendung</span><span class="sxs-lookup"><span data-stu-id="cb864-105">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

   <span data-ttu-id="cb864-106">Registrieren einer AAD-App im Registrierungsbereich der Azure Active > **Directory-App** im Azure-Portal: **Azure Active Directory**</span><span class="sxs-lookup"><span data-stu-id="cb864-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

   <span data-ttu-id="cb864-107">1\.</span><span class="sxs-lookup"><span data-stu-id="cb864-107">1\.</span></span> <span data-ttu-id="cb864-108">Geben Sie einen **Namen** für die App an (z. B. \*\* Blazor Client AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="cb864-108">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span><br>
   <span data-ttu-id="cb864-109">2\.</span><span class="sxs-lookup"><span data-stu-id="cb864-109">2\.</span></span> <span data-ttu-id="cb864-110">Wählen Sie unter **Unterstützte Kontotypen** **Konten in einem beliebigen Organisationsverzeichnis**aus.</span><span class="sxs-lookup"><span data-stu-id="cb864-110">In **Supported account types**, select **Accounts in any organizational directory**.</span></span><br>
   <span data-ttu-id="cb864-111">3\.</span><span class="sxs-lookup"><span data-stu-id="cb864-111">3\.</span></span> <span data-ttu-id="cb864-112">Lassen Sie die Droptauchliste **"Umleitungs-URI"** auf **Web**festgelegt, und stellen Sie einen Umleitungs-URI von `https://localhost:5001/authentication/login-callback`bereit.</span><span class="sxs-lookup"><span data-stu-id="cb864-112">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span><br>
   <span data-ttu-id="cb864-113">4\.</span><span class="sxs-lookup"><span data-stu-id="cb864-113">4\.</span></span> <span data-ttu-id="cb864-114">Deaktivieren Sie das Kontrollkästchen **Permissions** > **Grant admin concent zum öffnen und offline_access Berechtigungen.**</span><span class="sxs-lookup"><span data-stu-id="cb864-114">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span><br>
   <span data-ttu-id="cb864-115">5\.</span><span class="sxs-lookup"><span data-stu-id="cb864-115">5\.</span></span> <span data-ttu-id="cb864-116">Wählen Sie **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="cb864-116">Select **Register**.</span></span>

   <span data-ttu-id="cb864-117">In > **Authentifizierungsplattform-Konfigurationen** > **Web**: **Authentication**</span><span class="sxs-lookup"><span data-stu-id="cb864-117">In **Authentication** > **Platform configurations** > **Web**:</span></span>

   <span data-ttu-id="cb864-118">1\.</span><span class="sxs-lookup"><span data-stu-id="cb864-118">1\.</span></span> <span data-ttu-id="cb864-119">Bestätigen Sie, dass `https://localhost:5001/authentication/login-callback` der **Umleitungs-URI** von vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="cb864-119">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span><br>
   <span data-ttu-id="cb864-120">2\.</span><span class="sxs-lookup"><span data-stu-id="cb864-120">2\.</span></span> <span data-ttu-id="cb864-121">Aktivieren Sie für **Implizite Erteilung**die Kontrollkästchen für **Access-Token** und **ID-Token**.</span><span class="sxs-lookup"><span data-stu-id="cb864-121">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span><br>
   <span data-ttu-id="cb864-122">3\.</span><span class="sxs-lookup"><span data-stu-id="cb864-122">3\.</span></span> <span data-ttu-id="cb864-123">Die verbleibenden Standardeinstellungen für die App sind für diese Erfahrung akzeptabel.</span><span class="sxs-lookup"><span data-stu-id="cb864-123">The remaining defaults for the app are acceptable for this experience.</span></span><br>
   <span data-ttu-id="cb864-124">4\.</span><span class="sxs-lookup"><span data-stu-id="cb864-124">4\.</span></span> <span data-ttu-id="cb864-125">Wählen Sie die Schaltfläche **Speichern** aus.</span><span class="sxs-lookup"><span data-stu-id="cb864-125">Select the **Save** button.</span></span>

   <span data-ttu-id="cb864-126">Zeichnen Sie die Anwendungs-ID (Client-ID) auf (z. B. `11111111-1111-1111-1111-111111111111`).</span><span class="sxs-lookup"><span data-stu-id="cb864-126">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

1. <span data-ttu-id="cb864-127">Ersetzen Sie die Platzhalter im folgenden Befehl durch die zuvor aufgezeichneten Informationen, und führen Sie den Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="cb864-127">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   <span data-ttu-id="cb864-128">Um den Ausgabespeicherort anzugeben, der einen Projektordner erstellt, falls dieser nicht vorhanden ist, fügen `-o BlazorSample`Sie die Ausgabeoption in den Befehl mit einem Pfad ein (z. B. ).</span><span class="sxs-lookup"><span data-stu-id="cb864-128">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="cb864-129">Der Ordnername wird auch Teil des Projektnamens.</span><span class="sxs-lookup"><span data-stu-id="cb864-129">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="cb864-130">Nach dem Erstellen der App sollten Sie in der Lage sein:</span><span class="sxs-lookup"><span data-stu-id="cb864-130">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="cb864-131">Melden Sie sich mit einem Microsoft-Konto bei der App an.</span><span class="sxs-lookup"><span data-stu-id="cb864-131">Log into the app using a Microsoft Account.</span></span>
* <span data-ttu-id="cb864-132">Anfordern von Zugriffstoken für Microsoft-APIs mit Blazor dem gleichen Ansatz wie für eigenständige Apps, sofern Sie die App ordnungsgemäß konfiguriert haben.</span><span class="sxs-lookup"><span data-stu-id="cb864-132">Request access tokens for Microsoft APIs using the same approach as for standalone Blazor apps provided that you have configured the app correctly.</span></span> <span data-ttu-id="cb864-133">Weitere Informationen finden Sie unter [Schnellstart: Konfigurieren einer Anwendung zum Verfügbarmachen von Web-APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="cb864-133">For more information, see [Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="cb864-134">Authentifizierungspaket</span><span class="sxs-lookup"><span data-stu-id="cb864-134">Authentication package</span></span>

<span data-ttu-id="cb864-135">Wenn eine App für die Verwendung`SingleOrg`von Arbeits- oder Schulkonten ( erstellt`Microsoft.Authentication.WebAssembly.Msal`wird, erhält die App automatisch eine Paketreferenz für die [Microsoft-Authentifizierungsbibliothek](/azure/active-directory/develop/msal-overview) ( ).</span><span class="sxs-lookup"><span data-stu-id="cb864-135">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="cb864-136">Das Paket stellt eine Reihe von Primitiven bereit, die der App helfen, Benutzer zu authentifizieren und Token zum Aufrufen geschützter APIs zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="cb864-136">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="cb864-137">Wenn Sie einer App eine Authentifizierung hinzufügen, fügen Sie das Paket manuell zur Projektdatei der App hinzu:</span><span class="sxs-lookup"><span data-stu-id="cb864-137">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="cb864-138">Ersetzen `{VERSION}` Sie im vorherigen Paketverweis `Microsoft.AspNetCore.Blazor.Templates` durch die <xref:blazor/get-started> im Artikel gezeigte Version des Pakets.</span><span class="sxs-lookup"><span data-stu-id="cb864-138">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="cb864-139">Das `Microsoft.Authentication.WebAssembly.Msal` Paket fügt das `Microsoft.AspNetCore.Components.WebAssembly.Authentication` Paket transitiv zur App hinzu.</span><span class="sxs-lookup"><span data-stu-id="cb864-139">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="cb864-140">Unterstützung des Authentifizierungsdienstes</span><span class="sxs-lookup"><span data-stu-id="cb864-140">Authentication service support</span></span>

<span data-ttu-id="cb864-141">Unterstützung für die Authentifizierung von Benutzern wird `AddMsalAuthentication` im Dienstcontainer `Microsoft.Authentication.WebAssembly.Msal` mit der vom Paket bereitgestellten Erweiterungsmethode registriert.</span><span class="sxs-lookup"><span data-stu-id="cb864-141">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="cb864-142">Diese Methode richtet alle Dienste ein, die für die Interaktion der App mit dem Identitätsanbieter (IDENTITY Provider, IP) erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="cb864-142">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="cb864-143">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="cb864-143">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "{AUTHORITY}";
    authentication.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="cb864-144">Die `AddMsalAuthentication` Methode akzeptiert einen Rückruf, um die Parameter zu konfigurieren, die zum Authentifizieren einer App erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="cb864-144">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="cb864-145">Die zum Konfigurieren der App erforderlichen Werte können bei der Registrierung der App über die Microsoft-Kontenkonfiguration abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="cb864-145">The values required for configuring the app can be obtained from the Microsoft Accounts configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="cb864-146">Zugriffstokenbereiche</span><span class="sxs-lookup"><span data-stu-id="cb864-146">Access token scopes</span></span>

<span data-ttu-id="cb864-147">Die Blazor WebAssembly-Vorlage konfiguriert die App nicht automatisch, um ein Zugriffstoken für eine sichere API anzufordern.</span><span class="sxs-lookup"><span data-stu-id="cb864-147">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="cb864-148">Um ein Token als Teil des Anmeldeflusses zu bereitstellen, fügen Sie den `MsalProviderOptions`Bereich den Standardzugriffstokenbereichen der folgenden Werte hinzu:</span><span class="sxs-lookup"><span data-stu-id="cb864-148">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="cb864-149">Wenn das Azure-Portal einen Bereichs-URI bereitstellt und **die App eine nicht behandelte Ausnahme auslöst,** wenn sie eine *401 nicht autorisierte* Antwort von der API empfängt, versuchen Sie, einen Bereichs-URI zu verwenden, der das Schema und den Host nicht enthält.</span><span class="sxs-lookup"><span data-stu-id="cb864-149">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="cb864-150">Das Azure-Portal kann z. B. eines der folgenden Bereichs-URI-Formate bereitstellen:</span><span class="sxs-lookup"><span data-stu-id="cb864-150">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="cb864-151">Geben Sie den Bereichs-URI ohne Schema und Host:</span><span class="sxs-lookup"><span data-stu-id="cb864-151">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="cb864-152">Weitere Informationen finden Sie unter <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="cb864-152">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

## <a name="imports-file"></a><span data-ttu-id="cb864-153">Imports-Datei</span><span class="sxs-lookup"><span data-stu-id="cb864-153">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="cb864-154">Indexseite</span><span class="sxs-lookup"><span data-stu-id="cb864-154">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="cb864-155">App-Komponente</span><span class="sxs-lookup"><span data-stu-id="cb864-155">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="cb864-156">RedirectToLogin-Komponente</span><span class="sxs-lookup"><span data-stu-id="cb864-156">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="cb864-157">LoginDisplay-Komponente</span><span class="sxs-lookup"><span data-stu-id="cb864-157">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="cb864-158">Authentifizierungskomponente</span><span class="sxs-lookup"><span data-stu-id="cb864-158">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="cb864-159">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="cb864-159">Additional resources</span></span>

* [<span data-ttu-id="cb864-160">Zusätzliche Zugriffstoken anfordern</span><span class="sxs-lookup"><span data-stu-id="cb864-160">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="cb864-161">Schnellstart: Registrieren einer Anwendung mit der Microsoft-Identitätsplattform</span><span class="sxs-lookup"><span data-stu-id="cb864-161">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="cb864-162">Schnellstart: Konfigurieren einer Anwendung zum Verfügbarmachen von Web-APIs</span><span class="sxs-lookup"><span data-stu-id="cb864-162">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)

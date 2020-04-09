---
title: Sichern einer Blazor ASP.NET eigenständigen Core WebAssembly-App mit Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 7e132723657b7e12803b67ec12c3a33f1945baa3
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976994"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="5cbd7-102">Sichern einer Blazor ASP.NET eigenständigen Core WebAssembly-App mit Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="5cbd7-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="5cbd7-103">Von [Javier Calvarro Nelson](https://github.com/javiercn) und Luke [Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5cbd7-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="5cbd7-104">So erstellen Blazor Sie eine eigenständige WebAssembly-App, die [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) für die Authentifizierung verwendet:</span><span class="sxs-lookup"><span data-stu-id="5cbd7-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="5cbd7-105">[Erstellen eines AAD-Mandanten und einer Webanwendung:](/azure/active-directory/develop/v2-overview)</span><span class="sxs-lookup"><span data-stu-id="5cbd7-105">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="5cbd7-106">Registrieren einer AAD-App im Registrierungsbereich der Azure Active > **Directory-App** im Azure-Portal: **Azure Active Directory**</span><span class="sxs-lookup"><span data-stu-id="5cbd7-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="5cbd7-107">Geben Sie einen **Namen** für die App an (z. B. \*\* Blazor Client AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="5cbd7-107">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="5cbd7-108">Wählen Sie einen **unterstützten Kontotyp aus.**</span><span class="sxs-lookup"><span data-stu-id="5cbd7-108">Choose a **Supported account types**.</span></span> <span data-ttu-id="5cbd7-109">Sie können **Konten in diesem Organisationsverzeichnis nur** für diese Erfahrung auswählen.</span><span class="sxs-lookup"><span data-stu-id="5cbd7-109">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="5cbd7-110">Lassen Sie die Droptauchliste **"Umleitungs-URI"** auf **Web**festgelegt, und stellen Sie einen Umleitungs-URI von `https://localhost:5001/authentication/login-callback`bereit.</span><span class="sxs-lookup"><span data-stu-id="5cbd7-110">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="5cbd7-111">Deaktivieren Sie das Kontrollkästchen **Permissions** > **Grant admin concent zum öffnen und offline_access Berechtigungen.**</span><span class="sxs-lookup"><span data-stu-id="5cbd7-111">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="5cbd7-112">Wählen Sie **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="5cbd7-112">Select **Register**.</span></span>

<span data-ttu-id="5cbd7-113">In > **Authentifizierungsplattform-Konfigurationen** > **Web**: **Authentication**</span><span class="sxs-lookup"><span data-stu-id="5cbd7-113">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="5cbd7-114">Bestätigen Sie, dass `https://localhost:5001/authentication/login-callback` der **Umleitungs-URI** von vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="5cbd7-114">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="5cbd7-115">Aktivieren Sie für **Implizite Erteilung**die Kontrollkästchen für **Access-Token** und **ID-Token**.</span><span class="sxs-lookup"><span data-stu-id="5cbd7-115">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="5cbd7-116">Die verbleibenden Standardeinstellungen für die App sind für diese Erfahrung akzeptabel.</span><span class="sxs-lookup"><span data-stu-id="5cbd7-116">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="5cbd7-117">Wählen Sie die Schaltfläche **Speichern** aus.</span><span class="sxs-lookup"><span data-stu-id="5cbd7-117">Select the **Save** button.</span></span>

<span data-ttu-id="5cbd7-118">Zeichnen Sie die folgenden Informationen auf:</span><span class="sxs-lookup"><span data-stu-id="5cbd7-118">Record the following information:</span></span>

* <span data-ttu-id="5cbd7-119">Anwendungs-ID (Client-ID) `11111111-1111-1111-1111-111111111111`(z. B. )</span><span class="sxs-lookup"><span data-stu-id="5cbd7-119">Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="5cbd7-120">Verzeichnis-ID (Tenant ID) `22222222-2222-2222-2222-222222222222`(z. B. )</span><span class="sxs-lookup"><span data-stu-id="5cbd7-120">Directory ID (Tenant ID) (for example, `22222222-2222-2222-2222-222222222222`)</span></span>

<span data-ttu-id="5cbd7-121">Ersetzen Sie die Platzhalter im folgenden Befehl durch die zuvor aufgezeichneten Informationen, und führen Sie den Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="5cbd7-121">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="5cbd7-122">Um den Ausgabespeicherort anzugeben, der einen Projektordner erstellt, falls dieser nicht vorhanden ist, fügen `-o BlazorSample`Sie die Ausgabeoption in den Befehl mit einem Pfad ein (z. B. ).</span><span class="sxs-lookup"><span data-stu-id="5cbd7-122">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="5cbd7-123">Der Ordnername wird auch Teil des Projektnamens.</span><span class="sxs-lookup"><span data-stu-id="5cbd7-123">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="5cbd7-124">Authentifizierungspaket</span><span class="sxs-lookup"><span data-stu-id="5cbd7-124">Authentication package</span></span>

<span data-ttu-id="5cbd7-125">Wenn eine App für die Verwendung`SingleOrg`von Arbeits- oder Schulkonten ( erstellt`Microsoft.Authentication.WebAssembly.Msal`wird, erhält die App automatisch eine Paketreferenz für die [Microsoft-Authentifizierungsbibliothek](/azure/active-directory/develop/msal-overview) ( ).</span><span class="sxs-lookup"><span data-stu-id="5cbd7-125">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="5cbd7-126">Das Paket stellt eine Reihe von Primitiven bereit, die der App helfen, Benutzer zu authentifizieren und Token zum Aufrufen geschützter APIs zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="5cbd7-126">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="5cbd7-127">Wenn Sie einer App eine Authentifizierung hinzufügen, fügen Sie das Paket manuell zur Projektdatei der App hinzu:</span><span class="sxs-lookup"><span data-stu-id="5cbd7-127">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="5cbd7-128">Ersetzen `{VERSION}` Sie im vorherigen Paketverweis `Microsoft.AspNetCore.Blazor.Templates` durch die <xref:blazor/get-started> im Artikel gezeigte Version des Pakets.</span><span class="sxs-lookup"><span data-stu-id="5cbd7-128">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="5cbd7-129">Das `Microsoft.Authentication.WebAssembly.Msal` Paket fügt das `Microsoft.AspNetCore.Components.WebAssembly.Authentication` Paket transitiv zur App hinzu.</span><span class="sxs-lookup"><span data-stu-id="5cbd7-129">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="5cbd7-130">Unterstützung des Authentifizierungsdienstes</span><span class="sxs-lookup"><span data-stu-id="5cbd7-130">Authentication service support</span></span>

<span data-ttu-id="5cbd7-131">Unterstützung für die Authentifizierung von Benutzern wird `AddMsalAuthentication` im Dienstcontainer `Microsoft.Authentication.WebAssembly.Msal` mit der vom Paket bereitgestellten Erweiterungsmethode registriert.</span><span class="sxs-lookup"><span data-stu-id="5cbd7-131">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="5cbd7-132">Diese Methode richtet alle Dienste ein, die für die Interaktion der App mit dem Identitätsanbieter (IDENTITY Provider, IP) erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="5cbd7-132">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="5cbd7-133">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="5cbd7-133">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="5cbd7-134">Die `AddMsalAuthentication` Methode akzeptiert einen Rückruf, um die Parameter zu konfigurieren, die zum Authentifizieren einer App erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="5cbd7-134">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="5cbd7-135">Die zum Konfigurieren der App erforderlichen Werte können bei der Registrierung der App über die Azure Portal AAD-Konfiguration abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="5cbd7-135">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="5cbd7-136">Zugriffstokenbereiche</span><span class="sxs-lookup"><span data-stu-id="5cbd7-136">Access token scopes</span></span>

<span data-ttu-id="5cbd7-137">Die Blazor WebAssembly-Vorlage konfiguriert die App nicht automatisch, um ein Zugriffstoken für eine sichere API anzufordern.</span><span class="sxs-lookup"><span data-stu-id="5cbd7-137">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="5cbd7-138">Um ein Token als Teil des Anmeldeflusses zu bereitstellen, fügen Sie den `MsalProviderOptions`Bereich den Standardzugriffstokenbereichen der folgenden Werte hinzu:</span><span class="sxs-lookup"><span data-stu-id="5cbd7-138">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="5cbd7-139">Wenn das Azure-Portal einen Bereichs-URI bereitstellt und **die App eine nicht behandelte Ausnahme auslöst,** wenn sie eine *401 nicht autorisierte* Antwort von der API empfängt, versuchen Sie, einen Bereichs-URI zu verwenden, der das Schema und den Host nicht enthält.</span><span class="sxs-lookup"><span data-stu-id="5cbd7-139">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="5cbd7-140">Das Azure-Portal kann z. B. eines der folgenden Bereichs-URI-Formate bereitstellen:</span><span class="sxs-lookup"><span data-stu-id="5cbd7-140">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="5cbd7-141">Geben Sie den Bereichs-URI ohne Schema und Host:</span><span class="sxs-lookup"><span data-stu-id="5cbd7-141">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="5cbd7-142">Weitere Informationen finden Sie unter <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="5cbd7-142">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

## <a name="imports-file"></a><span data-ttu-id="5cbd7-143">Imports-Datei</span><span class="sxs-lookup"><span data-stu-id="5cbd7-143">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="5cbd7-144">Indexseite</span><span class="sxs-lookup"><span data-stu-id="5cbd7-144">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="5cbd7-145">App-Komponente</span><span class="sxs-lookup"><span data-stu-id="5cbd7-145">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="5cbd7-146">RedirectToLogin-Komponente</span><span class="sxs-lookup"><span data-stu-id="5cbd7-146">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="5cbd7-147">LoginDisplay-Komponente</span><span class="sxs-lookup"><span data-stu-id="5cbd7-147">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="5cbd7-148">Authentifizierungskomponente</span><span class="sxs-lookup"><span data-stu-id="5cbd7-148">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="5cbd7-149">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="5cbd7-149">Additional resources</span></span>

* [<span data-ttu-id="5cbd7-150">Zusätzliche Zugriffstoken anfordern</span><span class="sxs-lookup"><span data-stu-id="5cbd7-150">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="5cbd7-151">Dokumentation zur Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="5cbd7-151">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)

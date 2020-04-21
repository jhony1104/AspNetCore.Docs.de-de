---
title: Sichern einer Blazor ASP.NET gehosteten Core WebAssembly-App mit Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: a80be8d145b7c58be35e2c353a448db7e234e20b
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661823"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="aaba9-102">Sichern einer Blazor ASP.NET gehosteten Core WebAssembly-App mit Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="aaba9-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="aaba9-103">Von [Javier Calvarro Nelson](https://github.com/javiercn) und Luke [Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="aaba9-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="aaba9-104">In diesem Artikel wird beschrieben, wie Sie eine [ Blazor gehostete WebAssembly-App](xref:blazor/hosting-models#blazor-webassembly) erstellen, die [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) für die Authentifizierung verwendet.</span><span class="sxs-lookup"><span data-stu-id="aaba9-104">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="aaba9-105">Registrieren von Apps in AAD B2C und Erstellen einer Lösung</span><span class="sxs-lookup"><span data-stu-id="aaba9-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="aaba9-106">Erstellen eines Mandanten</span><span class="sxs-lookup"><span data-stu-id="aaba9-106">Create a tenant</span></span>

<span data-ttu-id="aaba9-107">Befolgen Sie die Anleitung unter [Schnellstart: Richten Sie einen Mandanten ein,](/azure/active-directory/develop/quickstart-create-new-tenant) um einen Mandanten in AAD zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="aaba9-107">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="aaba9-108">Registrieren einer Server-API-App</span><span class="sxs-lookup"><span data-stu-id="aaba9-108">Register a server API app</span></span>

<span data-ttu-id="aaba9-109">Befolgen Sie die Anleitung unter [Schnellstart: Registrieren Sie eine Anwendung mit der Microsoft-Identitätsplattform](/azure/active-directory/develop/quickstart-register-app) und nachfolgenden Azure AAD-Themen, um eine AAD-App für die *Server-API-App* im Registrierungsbereich der Azure Active > **Directory-App** des Azure-Portals zu registrieren: **Azure Active Directory**</span><span class="sxs-lookup"><span data-stu-id="aaba9-109">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="aaba9-110">Wählen Sie **Neue Registrierung** aus.</span><span class="sxs-lookup"><span data-stu-id="aaba9-110">Select **New registration**.</span></span>
1. <span data-ttu-id="aaba9-111">Geben Sie einen **Namen** für die App an (z. B. \*\* Blazor Server AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="aaba9-111">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="aaba9-112">Wählen Sie einen **unterstützten Kontotyp aus.**</span><span class="sxs-lookup"><span data-stu-id="aaba9-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="aaba9-113">Für diese Erfahrung können Sie **nur Konten in diesem Organisationsverzeichnis** (Einzelmandant) auswählen.</span><span class="sxs-lookup"><span data-stu-id="aaba9-113">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="aaba9-114">Die *Server-API-App* benötigt in diesem Szenario keinen **Umleitungs-URI,** sodass die Dropdown-Liste auf **Web** festgelegt ist, und geben Sie keinen Umleitungs-URI ein.</span><span class="sxs-lookup"><span data-stu-id="aaba9-114">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="aaba9-115">Deaktivieren Sie das Kontrollkästchen **Permissions** > **Grant admin concent zum öffnen und offline_access Berechtigungen.**</span><span class="sxs-lookup"><span data-stu-id="aaba9-115">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="aaba9-116">Wählen Sie **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="aaba9-116">Select **Register**.</span></span>

<span data-ttu-id="aaba9-117">Entfernen Sie in **API-Berechtigungen**die **Berechtigung Microsoft Graph** > **User.Read,** da die App keinen Anmelden oder keinen Profilzugriff erfordert.</span><span class="sxs-lookup"><span data-stu-id="aaba9-117">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or uer profile access.</span></span>

<span data-ttu-id="aaba9-118">In **Expose eine API**:</span><span class="sxs-lookup"><span data-stu-id="aaba9-118">In **Expose an API**:</span></span>

1. <span data-ttu-id="aaba9-119">Wählen Sie **Bereich hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="aaba9-119">Select **Add a scope**.</span></span>
1. <span data-ttu-id="aaba9-120">Wählen Sie **Speichern und fortfahren** aus.</span><span class="sxs-lookup"><span data-stu-id="aaba9-120">Select **Save and continue**.</span></span>
1. <span data-ttu-id="aaba9-121">Geben Sie einen **Bereichsnamen** an (z. B. `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="aaba9-121">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="aaba9-122">Geben Sie einen **Administrator-Zustimmungsanzeigenamen** an (z. B. `Access API`).</span><span class="sxs-lookup"><span data-stu-id="aaba9-122">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="aaba9-123">Geben Sie eine **Administrator-Zustimmungsbeschreibung** an (z. B. `Allows the app to access server app API endpoints.`).</span><span class="sxs-lookup"><span data-stu-id="aaba9-123">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="aaba9-124">Bestätigen Sie, dass der **Status** auf **Aktiviert**festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="aaba9-124">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="aaba9-125">Wählen Sie **Bereich hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="aaba9-125">Select **Add scope**.</span></span>

<span data-ttu-id="aaba9-126">Zeichnen Sie die folgenden Informationen auf:</span><span class="sxs-lookup"><span data-stu-id="aaba9-126">Record the following information:</span></span>

* <span data-ttu-id="aaba9-127">*Server-API-App* Anwendungs-ID (Client-ID) `11111111-1111-1111-1111-111111111111`(z. B. )</span><span class="sxs-lookup"><span data-stu-id="aaba9-127">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="aaba9-128">App-ID-URI (z. B. `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`oder der von Ihnen angegebene benutzerdefinierte Wert)</span><span class="sxs-lookup"><span data-stu-id="aaba9-128">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="aaba9-129">Verzeichnis-ID (Tenant ID) `222222222-2222-2222-2222-222222222222`(z. B. )</span><span class="sxs-lookup"><span data-stu-id="aaba9-129">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="aaba9-130">AAD-Mandantendomäne (z. B. `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="aaba9-130">AAD Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>
* <span data-ttu-id="aaba9-131">Standardbereich (z. `API.Access`B. )</span><span class="sxs-lookup"><span data-stu-id="aaba9-131">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="aaba9-132">Registrieren einer Client-App</span><span class="sxs-lookup"><span data-stu-id="aaba9-132">Register a client app</span></span>

<span data-ttu-id="aaba9-133">Befolgen Sie die Anleitung unter [Schnellstart: Registrieren Sie eine Anwendung mit der Microsoft-Identitätsplattform](/azure/active-directory/develop/quickstart-register-app) und nachfolgenden Azure AAD-Themen, um eine AAD-App für die *Client-App* im Registrierungsbereich der Azure Active > **Directory-App** im Azure-Portal zu registrieren: **Azure Active Directory**</span><span class="sxs-lookup"><span data-stu-id="aaba9-133">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="aaba9-134">Wählen Sie **Neue Registrierung** aus.</span><span class="sxs-lookup"><span data-stu-id="aaba9-134">Select **New registration**.</span></span>
1. <span data-ttu-id="aaba9-135">Geben Sie einen **Namen** für die App an (z. B. \*\* Blazor Client AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="aaba9-135">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="aaba9-136">Wählen Sie einen **unterstützten Kontotyp aus.**</span><span class="sxs-lookup"><span data-stu-id="aaba9-136">Choose a **Supported account types**.</span></span> <span data-ttu-id="aaba9-137">Für diese Erfahrung können Sie **nur Konten in diesem Organisationsverzeichnis** (Einzelmandant) auswählen.</span><span class="sxs-lookup"><span data-stu-id="aaba9-137">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="aaba9-138">Lassen Sie die Droptauchliste **"Umleitungs-URI"** auf **Web**festgelegt, und stellen Sie einen Umleitungs-URI von `https://localhost:5001/authentication/login-callback`bereit.</span><span class="sxs-lookup"><span data-stu-id="aaba9-138">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="aaba9-139">Deaktivieren Sie das Kontrollkästchen **Permissions** > **Grant admin concent zum öffnen und offline_access Berechtigungen.**</span><span class="sxs-lookup"><span data-stu-id="aaba9-139">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="aaba9-140">Wählen Sie **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="aaba9-140">Select **Register**.</span></span>

<span data-ttu-id="aaba9-141">In > **Authentifizierungsplattform-Konfigurationen** > **Web**: **Authentication**</span><span class="sxs-lookup"><span data-stu-id="aaba9-141">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="aaba9-142">Bestätigen Sie, dass `https://localhost:5001/authentication/login-callback` der **Umleitungs-URI** von vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="aaba9-142">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="aaba9-143">Aktivieren Sie für **Implizite Erteilung**die Kontrollkästchen für **Access-Token** und **ID-Token**.</span><span class="sxs-lookup"><span data-stu-id="aaba9-143">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="aaba9-144">Die verbleibenden Standardeinstellungen für die App sind für diese Erfahrung akzeptabel.</span><span class="sxs-lookup"><span data-stu-id="aaba9-144">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="aaba9-145">Wählen Sie die Schaltfläche **Speichern** aus.</span><span class="sxs-lookup"><span data-stu-id="aaba9-145">Select the **Save** button.</span></span>

<span data-ttu-id="aaba9-146">In **API-Berechtigungen**:</span><span class="sxs-lookup"><span data-stu-id="aaba9-146">In **API permissions**:</span></span>

1. <span data-ttu-id="aaba9-147">Vergewissern Sie sich, dass die App über die **Berechtigung Microsoft Graph** > **User.Read** verfügt.</span><span class="sxs-lookup"><span data-stu-id="aaba9-147">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="aaba9-148">Wählen Sie **Eine Berechtigung hinzufügen,** gefolgt von **Meine APIs**.</span><span class="sxs-lookup"><span data-stu-id="aaba9-148">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="aaba9-149">Wählen Sie die *Server-API-App* aus der Spalte **Name** (z. B. \*\* Blazor Server AAD\*\*) aus.</span><span class="sxs-lookup"><span data-stu-id="aaba9-149">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="aaba9-150">Öffnen Sie die **API-Liste.**</span><span class="sxs-lookup"><span data-stu-id="aaba9-150">Open the **API** list.</span></span>
1. <span data-ttu-id="aaba9-151">Aktivieren sie den Zugriff auf `API.Access`die API (z. B. ).</span><span class="sxs-lookup"><span data-stu-id="aaba9-151">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="aaba9-152">Wählen Sie **Berechtigungen hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="aaba9-152">Select **Add permissions**.</span></span>
1. <span data-ttu-id="aaba9-153">Wählen Sie die Schaltfläche **Admin-Inhalt für "TENANT NAME"** gewähren aus.</span><span class="sxs-lookup"><span data-stu-id="aaba9-153">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="aaba9-154">Klicken Sie auf **Ja**, um zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="aaba9-154">Select **Yes** to confirm.</span></span>

<span data-ttu-id="aaba9-155">Zeichnen Sie die *Client-App-Anwendungs-ID* `33333333-3333-3333-3333-333333333333`(Client-ID) auf (z. B. ).</span><span class="sxs-lookup"><span data-stu-id="aaba9-155">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="aaba9-156">Erstellen der App</span><span class="sxs-lookup"><span data-stu-id="aaba9-156">Create the app</span></span>

<span data-ttu-id="aaba9-157">Ersetzen Sie die Platzhalter im folgenden Befehl durch die zuvor aufgezeichneten Informationen, und führen Sie den Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="aaba9-157">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="aaba9-158">Um den Ausgabespeicherort anzugeben, der einen Projektordner erstellt, falls dieser nicht vorhanden ist, fügen `-o BlazorSample`Sie die Ausgabeoption in den Befehl mit einem Pfad ein (z. B. ).</span><span class="sxs-lookup"><span data-stu-id="aaba9-158">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="aaba9-159">Der Ordnername wird auch Teil des Projektnamens.</span><span class="sxs-lookup"><span data-stu-id="aaba9-159">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="aaba9-160">Übergeben Sie den App-ID-URI an die `app-id-uri` Option, beachten Sie jedoch, dass möglicherweise eine Konfigurationsänderung in der Client-App erforderlich ist, die im Abschnitt [Access-Tokenbereiche](#access-token-scopes) beschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="aaba9-160">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="aaba9-161">Server-App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="aaba9-161">Server app configuration</span></span>

<span data-ttu-id="aaba9-162">*Dieser Abschnitt bezieht sich auf die **Server-App** der Lösung.*</span><span class="sxs-lookup"><span data-stu-id="aaba9-162">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="aaba9-163">Authentifizierungspaket</span><span class="sxs-lookup"><span data-stu-id="aaba9-163">Authentication package</span></span>

<span data-ttu-id="aaba9-164">Die Unterstützung für die Authentifizierung und Autorisierung von Aufrufen von `Microsoft.AspNetCore.Authentication.AzureAD.UI`ASP.NET Core Web APIs wird von der bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="aaba9-164">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="aaba9-165">Unterstützung des Authentifizierungsdienstes</span><span class="sxs-lookup"><span data-stu-id="aaba9-165">Authentication service support</span></span>

<span data-ttu-id="aaba9-166">Die `AddAuthentication` Methode richtet Authentifizierungsdienste in der App ein und konfiguriert den JWT Bearer-Handler als Standardauthentifizierungsmethode.</span><span class="sxs-lookup"><span data-stu-id="aaba9-166">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="aaba9-167">Die `AddAzureADBearer` Methode richtet die spezifischen Parameter im JWT Bearer-Handler ein, die zum Überprüfen von Token erforderlich sind, die von Azure Active Directory emittiert werden:</span><span class="sxs-lookup"><span data-stu-id="aaba9-167">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="aaba9-168">`UseAuthentication`und `UseAuthorization` stellen Sie sicher, dass:</span><span class="sxs-lookup"><span data-stu-id="aaba9-168">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="aaba9-169">Die App versucht, Token für eingehende Anforderungen zu analysieren und zu validieren.</span><span class="sxs-lookup"><span data-stu-id="aaba9-169">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="aaba9-170">Jede Anforderung, die versucht, ohne ordnungsgemäße Anmeldeinformationen auf eine geschützte Ressource zuzugreifen, schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="aaba9-170">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="aaba9-171">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="aaba9-171">User.Identity.Name</span></span>

<span data-ttu-id="aaba9-172">Standardmäßig füllt die Server-App-API `User.Identity.Name` den Wert `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` aus dem Anspruchstyp (z. `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`B. ).</span><span class="sxs-lookup"><span data-stu-id="aaba9-172">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="aaba9-173">Um die App so zu `name` konfigurieren, dass sie den Wert vom Anspruchstyp empfängt, konfigurieren Sie die [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) der <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in: `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="aaba9-173">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="aaba9-174">App-Einstellungen</span><span class="sxs-lookup"><span data-stu-id="aaba9-174">App settings</span></span>

<span data-ttu-id="aaba9-175">Die Datei *appsettings.json* enthält die Optionen zum Konfigurieren des JWT-Trägerhandlers, der zum Überprüfen von Zugriffstoken verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="aaba9-175">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{API CLIENT ID}",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="aaba9-176">WeatherForecast-Controller</span><span class="sxs-lookup"><span data-stu-id="aaba9-176">WeatherForecast controller</span></span>

<span data-ttu-id="aaba9-177">Der WeatherForecast-Controller (*Controllers/WeatherForecastController.cs*) macht `[Authorize]` eine geschützte API mit dem attribut verfügbar, das auf den Controller angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="aaba9-177">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="aaba9-178">Es ist **wichtig** zu verstehen, dass:</span><span class="sxs-lookup"><span data-stu-id="aaba9-178">It's **important** to understand that:</span></span>

* <span data-ttu-id="aaba9-179">Das `[Authorize]` Attribut in diesem API-Controller ist das einzige, was diese API vor unbefugtem Zugriff schützt.</span><span class="sxs-lookup"><span data-stu-id="aaba9-179">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="aaba9-180">Das `[Authorize]` in der Blazor WebAssembly-App verwendete Attribut dient nur als Hinweis für die App, dass der Benutzer autorisiert werden soll, damit die App ordnungsgemäß funktioniert.</span><span class="sxs-lookup"><span data-stu-id="aaba9-180">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="aaba9-181">Client-App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="aaba9-181">Client app configuration</span></span>

<span data-ttu-id="aaba9-182">*Dieser Abschnitt bezieht sich auf die **Client-App** der Lösung.*</span><span class="sxs-lookup"><span data-stu-id="aaba9-182">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="aaba9-183">Authentifizierungspaket</span><span class="sxs-lookup"><span data-stu-id="aaba9-183">Authentication package</span></span>

<span data-ttu-id="aaba9-184">Wenn eine App für die Verwendung`SingleOrg`von Arbeits- oder Schulkonten ( erstellt`Microsoft.Authentication.WebAssembly.Msal`wird, erhält die App automatisch eine Paketreferenz für die [Microsoft-Authentifizierungsbibliothek](/azure/active-directory/develop/msal-overview) ( ).</span><span class="sxs-lookup"><span data-stu-id="aaba9-184">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="aaba9-185">Das Paket stellt eine Reihe von Primitiven bereit, die der App helfen, Benutzer zu authentifizieren und Token zum Aufrufen geschützter APIs zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="aaba9-185">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="aaba9-186">Wenn Sie einer App eine Authentifizierung hinzufügen, fügen Sie das Paket manuell zur Projektdatei der App hinzu:</span><span class="sxs-lookup"><span data-stu-id="aaba9-186">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="aaba9-187">Ersetzen `{VERSION}` Sie im vorherigen Paketverweis `Microsoft.AspNetCore.Blazor.Templates` durch die <xref:blazor/get-started> im Artikel gezeigte Version des Pakets.</span><span class="sxs-lookup"><span data-stu-id="aaba9-187">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="aaba9-188">Das `Microsoft.Authentication.WebAssembly.Msal` Paket fügt das `Microsoft.AspNetCore.Components.WebAssembly.Authentication` Paket transitiv zur App hinzu.</span><span class="sxs-lookup"><span data-stu-id="aaba9-188">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="aaba9-189">Unterstützung des Authentifizierungsdienstes</span><span class="sxs-lookup"><span data-stu-id="aaba9-189">Authentication service support</span></span>

<span data-ttu-id="aaba9-190">Unterstützung für die Authentifizierung von Benutzern wird `AddMsalAuthentication` im Dienstcontainer `Microsoft.Authentication.WebAssembly.Msal` mit der vom Paket bereitgestellten Erweiterungsmethode registriert.</span><span class="sxs-lookup"><span data-stu-id="aaba9-190">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="aaba9-191">Diese Methode richtet alle Dienste ein, die für die Interaktion der App mit dem Identitätsanbieter (IDENTITY Provider, IP) erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="aaba9-191">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="aaba9-192">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="aaba9-192">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="aaba9-193">Die `AddMsalAuthentication` Methode akzeptiert einen Rückruf, um die Parameter zu konfigurieren, die zum Authentifizieren einer App erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="aaba9-193">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="aaba9-194">Die zum Konfigurieren der App erforderlichen Werte können bei der Registrierung der App über die Azure Portal AAD-Konfiguration abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="aaba9-194">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

### <a name="access-token-scopes"></a><span data-ttu-id="aaba9-195">Zugriffstokenbereiche</span><span class="sxs-lookup"><span data-stu-id="aaba9-195">Access token scopes</span></span>

<span data-ttu-id="aaba9-196">Die Standardzugriffstokenbereiche stellen die Liste der Zugriffstokenbereiche dar, die:</span><span class="sxs-lookup"><span data-stu-id="aaba9-196">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="aaba9-197">Standardmäßig in der Anmeldeanforderung enthalten.</span><span class="sxs-lookup"><span data-stu-id="aaba9-197">Included by default in the sign in request.</span></span>
* <span data-ttu-id="aaba9-198">Wird verwendet, um ein Zugriffstoken unmittelbar nach der Authentifizierung zu bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="aaba9-198">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="aaba9-199">Alle Bereiche müssen gemäß Azure Active Directory-Regeln derselben App angehören.</span><span class="sxs-lookup"><span data-stu-id="aaba9-199">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="aaba9-200">Bei Bedarf können zusätzliche Bereiche für zusätzliche API-Apps hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="aaba9-200">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="aaba9-201">Wenn das Azure-Portal einen Bereichs-URI bereitstellt und **die App eine nicht behandelte Ausnahme auslöst,** wenn sie eine *401 nicht autorisierte* Antwort von der API empfängt, versuchen Sie, einen Bereichs-URI zu verwenden, der das Schema und den Host nicht enthält.</span><span class="sxs-lookup"><span data-stu-id="aaba9-201">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="aaba9-202">Das Azure-Portal kann z. B. eines der folgenden Bereichs-URI-Formate bereitstellen:</span><span class="sxs-lookup"><span data-stu-id="aaba9-202">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="aaba9-203">Geben Sie den Bereichs-URI ohne Schema und Host:</span><span class="sxs-lookup"><span data-stu-id="aaba9-203">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="aaba9-204">Weitere Informationen finden Sie unter <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="aaba9-204">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

### <a name="imports-file"></a><span data-ttu-id="aaba9-205">Imports-Datei</span><span class="sxs-lookup"><span data-stu-id="aaba9-205">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="aaba9-206">Indexseite</span><span class="sxs-lookup"><span data-stu-id="aaba9-206">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="aaba9-207">App-Komponente</span><span class="sxs-lookup"><span data-stu-id="aaba9-207">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="aaba9-208">RedirectToLogin-Komponente</span><span class="sxs-lookup"><span data-stu-id="aaba9-208">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="aaba9-209">LoginDisplay-Komponente</span><span class="sxs-lookup"><span data-stu-id="aaba9-209">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="aaba9-210">Authentifizierungskomponente</span><span class="sxs-lookup"><span data-stu-id="aaba9-210">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="aaba9-211">FetchData-Komponente</span><span class="sxs-lookup"><span data-stu-id="aaba9-211">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="aaba9-212">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="aaba9-212">Run the app</span></span>

<span data-ttu-id="aaba9-213">Führen Sie die App aus dem Serverprojekt aus.</span><span class="sxs-lookup"><span data-stu-id="aaba9-213">Run the app from the Server project.</span></span> <span data-ttu-id="aaba9-214">Wenn Sie Visual Studio verwenden, wählen Sie das Serverprojekt im **Projektmappen-Explorer** aus, und wählen Sie die Schaltfläche **Ausführen** in der Symbolleiste aus, oder starten Sie die App im **Menü Debug.**</span><span class="sxs-lookup"><span data-stu-id="aaba9-214">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="aaba9-215">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="aaba9-215">Additional resources</span></span>

* [<span data-ttu-id="aaba9-216">Zusätzliche Zugriffstoken anfordern</span><span class="sxs-lookup"><span data-stu-id="aaba9-216">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="aaba9-217">Dokumentation zur Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="aaba9-217">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)

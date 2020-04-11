---
title: Sichern einer Blazor ASP.NET gehosteten Core WebAssembly-App mit Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: e2bb0c1bd807d590331b714e3b80d8c4ab434e2f
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123469"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="5db2e-102">Sichern einer Blazor ASP.NET gehosteten Core WebAssembly-App mit Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="5db2e-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="5db2e-103">Von [Javier Calvarro Nelson](https://github.com/javiercn) und Luke [Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5db2e-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="5db2e-104">In diesem Artikel wird Blazor beschrieben, wie Sie eine eigenständige WebAssembly-App erstellen, die [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) für die Authentifizierung verwendet.</span><span class="sxs-lookup"><span data-stu-id="5db2e-104">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="5db2e-105">Registrieren von Apps in AAD B2C und Erstellen einer Lösung</span><span class="sxs-lookup"><span data-stu-id="5db2e-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="5db2e-106">Erstellen eines Mandanten</span><span class="sxs-lookup"><span data-stu-id="5db2e-106">Create a tenant</span></span>

<span data-ttu-id="5db2e-107">Befolgen Sie die Anleitung im [Tutorial: Erstellen Sie einen Azure Active Directory B2C-Mandanten,](/azure/active-directory-b2c/tutorial-create-tenant) um einen AAD B2C-Mandanten zu erstellen und die folgenden Informationen aufzuzeichnen:</span><span class="sxs-lookup"><span data-stu-id="5db2e-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant and record the following information:</span></span>

* <span data-ttu-id="5db2e-108">AAD B2C-Instanz (z. B. `https://contoso.b2clogin.com/`, die den nachfolgenden Schrägstrich enthält)</span><span class="sxs-lookup"><span data-stu-id="5db2e-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="5db2e-109">AAD B2C Mandantendomäne (z. B. `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="5db2e-109">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="5db2e-110">Registrieren einer Server-API-App</span><span class="sxs-lookup"><span data-stu-id="5db2e-110">Register a server API app</span></span>

<span data-ttu-id="5db2e-111">Befolgen Sie die Anleitung im [Tutorial: Registrieren einer Anwendung in Azure Active Directory B2C,](/azure/active-directory-b2c/tutorial-register-applications) um eine AAD-App für die *Server-API-App* im Registrierungsbereich der Azure Active > **Directory-App** des Azure-Portals zu registrieren: **Azure Active Directory**</span><span class="sxs-lookup"><span data-stu-id="5db2e-111">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="5db2e-112">Wählen Sie **Neue Registrierung** aus.</span><span class="sxs-lookup"><span data-stu-id="5db2e-112">Select **New registration**.</span></span>
1. <span data-ttu-id="5db2e-113">Geben Sie einen **Namen** für die App an (z. B. \*\* Blazor Server AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="5db2e-113">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="5db2e-114">Wählen Sie für **unterstützte Kontotypen** **Konten in einem beliebigen Organisationsverzeichnis oder einem beliebigen Identitätsanbieter aus. Zum Authentifizieren von Benutzern mit Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="5db2e-114">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="5db2e-115">(Mehrmieter) für diese Erfahrung.</span><span class="sxs-lookup"><span data-stu-id="5db2e-115">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="5db2e-116">Die *Server-API-App* benötigt in diesem Szenario keinen **Umleitungs-URI,** sodass die Dropdown-Liste auf **Web** festgelegt ist, und geben Sie keinen Umleitungs-URI ein.</span><span class="sxs-lookup"><span data-stu-id="5db2e-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="5db2e-117">Vergewissern Sie sich, dass **Permissions** > **Grant admin concent für openid und offline_access Berechtigungen** aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="5db2e-117">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="5db2e-118">Wählen Sie **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="5db2e-118">Select **Register**.</span></span>

<span data-ttu-id="5db2e-119">In **Expose eine API**:</span><span class="sxs-lookup"><span data-stu-id="5db2e-119">In **Expose an API**:</span></span>

1. <span data-ttu-id="5db2e-120">Wählen Sie **Bereich hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="5db2e-120">Select **Add a scope**.</span></span>
1. <span data-ttu-id="5db2e-121">Wählen Sie **Speichern und fortfahren** aus.</span><span class="sxs-lookup"><span data-stu-id="5db2e-121">Select **Save and continue**.</span></span>
1. <span data-ttu-id="5db2e-122">Geben Sie einen **Bereichsnamen** an (z. B. `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="5db2e-122">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="5db2e-123">Geben Sie einen **Administrator-Zustimmungsanzeigenamen** an (z. B. `Access API`).</span><span class="sxs-lookup"><span data-stu-id="5db2e-123">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="5db2e-124">Geben Sie eine **Administrator-Zustimmungsbeschreibung** an (z. B. `Allows the app to access server app API endpoints.`).</span><span class="sxs-lookup"><span data-stu-id="5db2e-124">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="5db2e-125">Bestätigen Sie, dass der **Status** auf **Aktiviert**festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="5db2e-125">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="5db2e-126">Wählen Sie **Bereich hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="5db2e-126">Select **Add scope**.</span></span>

<span data-ttu-id="5db2e-127">Zeichnen Sie die folgenden Informationen auf:</span><span class="sxs-lookup"><span data-stu-id="5db2e-127">Record the following information:</span></span>

* <span data-ttu-id="5db2e-128">*Server-API-App* Anwendungs-ID (Client-ID) `11111111-1111-1111-1111-111111111111`(z. B. )</span><span class="sxs-lookup"><span data-stu-id="5db2e-128">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="5db2e-129">App-ID-URI (z. B. `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`oder der von Ihnen angegebene benutzerdefinierte Wert)</span><span class="sxs-lookup"><span data-stu-id="5db2e-129">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="5db2e-130">Verzeichnis-ID (Tenant ID) `222222222-2222-2222-2222-222222222222`(z. B. )</span><span class="sxs-lookup"><span data-stu-id="5db2e-130">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="5db2e-131">*Server-API-App* App-ID-URI (z. B. `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`kann das Azure-Portal den Wert standardmäßig auf die Client-ID festlegen)</span><span class="sxs-lookup"><span data-stu-id="5db2e-131">*Server API app* App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, the Azure portal might default the value to the Client ID)</span></span>
* <span data-ttu-id="5db2e-132">Standardbereich (z. `API.Access`B. )</span><span class="sxs-lookup"><span data-stu-id="5db2e-132">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="5db2e-133">Registrieren einer Client-App</span><span class="sxs-lookup"><span data-stu-id="5db2e-133">Register a client app</span></span>

<span data-ttu-id="5db2e-134">Befolgen Sie die Anleitung im [Tutorial: Registrieren Sie eine Anwendung erneut in Azure Active Directory B2C,](/azure/active-directory-b2c/tutorial-register-applications) um eine AAD-App für die *Client-App* im Registrierungsbereich der Azure Active > **Directory-App** des Azure-Portals zu registrieren: **Azure Active Directory**</span><span class="sxs-lookup"><span data-stu-id="5db2e-134">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="5db2e-135">Wählen Sie **Neue Registrierung** aus.</span><span class="sxs-lookup"><span data-stu-id="5db2e-135">Select **New registration**.</span></span>
1. <span data-ttu-id="5db2e-136">Geben Sie einen **Namen** für die App an (z. B. \*\* Blazor Client AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="5db2e-136">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="5db2e-137">Wählen Sie für **unterstützte Kontotypen** **Konten in einem beliebigen Organisationsverzeichnis oder einem beliebigen Identitätsanbieter aus. Zum Authentifizieren von Benutzern mit Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="5db2e-137">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="5db2e-138">(Mehrmieter) für diese Erfahrung.</span><span class="sxs-lookup"><span data-stu-id="5db2e-138">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="5db2e-139">Lassen Sie die Droptauchliste **"Umleitungs-URI"** auf **Web**festgelegt, und stellen Sie einen Umleitungs-URI von `https://localhost:5001/authentication/login-callback`bereit.</span><span class="sxs-lookup"><span data-stu-id="5db2e-139">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="5db2e-140">Vergewissern Sie sich, dass **Permissions** > **Grant admin concent für openid und offline_access Berechtigungen** aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="5db2e-140">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="5db2e-141">Wählen Sie **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="5db2e-141">Select **Register**.</span></span>

<span data-ttu-id="5db2e-142">In > **Authentifizierungsplattform-Konfigurationen** > **Web**: **Authentication**</span><span class="sxs-lookup"><span data-stu-id="5db2e-142">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="5db2e-143">Bestätigen Sie, dass `https://localhost:5001/authentication/login-callback` der **Umleitungs-URI** von vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="5db2e-143">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="5db2e-144">Aktivieren Sie für **Implizite Erteilung**die Kontrollkästchen für **Access-Token** und **ID-Token**.</span><span class="sxs-lookup"><span data-stu-id="5db2e-144">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="5db2e-145">Die verbleibenden Standardeinstellungen für die App sind für diese Erfahrung akzeptabel.</span><span class="sxs-lookup"><span data-stu-id="5db2e-145">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="5db2e-146">Wählen Sie die Schaltfläche **Speichern** aus.</span><span class="sxs-lookup"><span data-stu-id="5db2e-146">Select the **Save** button.</span></span>

<span data-ttu-id="5db2e-147">In **API-Berechtigungen**:</span><span class="sxs-lookup"><span data-stu-id="5db2e-147">In **API permissions**:</span></span>

1. <span data-ttu-id="5db2e-148">Vergewissern Sie sich, dass die App über die **Berechtigung Microsoft Graph** > **User.Read** verfügt.</span><span class="sxs-lookup"><span data-stu-id="5db2e-148">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="5db2e-149">Wählen Sie **Eine Berechtigung hinzufügen,** gefolgt von **Meine APIs**.</span><span class="sxs-lookup"><span data-stu-id="5db2e-149">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="5db2e-150">Wählen Sie die *Server-API-App* aus der Spalte **Name** aus (z. B. \*\* Blazor Server AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="5db2e-150">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="5db2e-151">Öffnen Sie die **API-Liste.**</span><span class="sxs-lookup"><span data-stu-id="5db2e-151">Open the **API** list.</span></span>
1. <span data-ttu-id="5db2e-152">Aktivieren sie den Zugriff auf `API.Access`die API (z. B. ).</span><span class="sxs-lookup"><span data-stu-id="5db2e-152">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="5db2e-153">Wählen Sie **Berechtigungen hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="5db2e-153">Select **Add permissions**.</span></span>
1. <span data-ttu-id="5db2e-154">Wählen Sie die Schaltfläche **Admin-Inhalt für "TENANT NAME"** gewähren aus.</span><span class="sxs-lookup"><span data-stu-id="5db2e-154">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="5db2e-155">Klicken Sie auf **Ja**, um zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="5db2e-155">Select **Yes** to confirm.</span></span>

<span data-ttu-id="5db2e-156">In **Home** > **Azure AD B2C-Benutzerflüsse:** > **User flows**</span><span class="sxs-lookup"><span data-stu-id="5db2e-156">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="5db2e-157">Erstellen eines Benutzerflows für Registrierung und Anmeldung</span><span class="sxs-lookup"><span data-stu-id="5db2e-157">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="5db2e-158">Wählen Sie mindestens das Benutzerattribut > **"Anwendungsname anzeigen"** aus, um `context.User.Identity.Name` das in der `LoginDisplay` Komponente (*Shared/LoginDisplay.razor*) aufzufüllen. **Application claims**</span><span class="sxs-lookup"><span data-stu-id="5db2e-158">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="5db2e-159">Zeichnen Sie die folgenden Informationen auf:</span><span class="sxs-lookup"><span data-stu-id="5db2e-159">Record the following information:</span></span>

* <span data-ttu-id="5db2e-160">Zeichnen Sie die *Client-App-Anwendungs-ID* `33333333-3333-3333-3333-333333333333`(Client-ID) auf (z. B. ).</span><span class="sxs-lookup"><span data-stu-id="5db2e-160">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>
* <span data-ttu-id="5db2e-161">Zeichnen Sie den für die App erstellten Anmelde- und `B2C_1_signupsignin`Anmeldebenutzerflussnamen auf (z. B. ).</span><span class="sxs-lookup"><span data-stu-id="5db2e-161">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="5db2e-162">Erstellen der App</span><span class="sxs-lookup"><span data-stu-id="5db2e-162">Create the app</span></span>

<span data-ttu-id="5db2e-163">Ersetzen Sie die Platzhalter im folgenden Befehl durch die zuvor aufgezeichneten Informationen, und führen Sie den Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="5db2e-163">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="5db2e-164">Um den Ausgabespeicherort anzugeben, der einen Projektordner erstellt, falls dieser nicht vorhanden ist, fügen `-o BlazorSample`Sie die Ausgabeoption in den Befehl mit einem Pfad ein (z. B. ).</span><span class="sxs-lookup"><span data-stu-id="5db2e-164">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="5db2e-165">Der Ordnername wird auch Teil des Projektnamens.</span><span class="sxs-lookup"><span data-stu-id="5db2e-165">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="5db2e-166">Übergeben Sie den App-ID-URI an die `app-id-uri` Option, beachten Sie jedoch, dass möglicherweise eine Konfigurationsänderung in der Client-App erforderlich ist, die im Abschnitt [Access-Tokenbereiche](#access-token-scopes) beschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="5db2e-166">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="5db2e-167">Server-App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="5db2e-167">Server app configuration</span></span>

<span data-ttu-id="5db2e-168">*Dieser Abschnitt bezieht sich auf die **Server-App** der Lösung.*</span><span class="sxs-lookup"><span data-stu-id="5db2e-168">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="5db2e-169">Authentifizierungspaket</span><span class="sxs-lookup"><span data-stu-id="5db2e-169">Authentication package</span></span>

<span data-ttu-id="5db2e-170">Die Unterstützung für die Authentifizierung und Autorisierung von Aufrufen von `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`ASP.NET Core Web APIs wird von der bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="5db2e-170">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="5db2e-171">Unterstützung des Authentifizierungsdienstes</span><span class="sxs-lookup"><span data-stu-id="5db2e-171">Authentication service support</span></span>

<span data-ttu-id="5db2e-172">Die `AddAuthentication` Methode richtet Authentifizierungsdienste in der App ein und konfiguriert den JWT Bearer-Handler als Standardauthentifizierungsmethode.</span><span class="sxs-lookup"><span data-stu-id="5db2e-172">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="5db2e-173">Die `AddAzureADB2CBearer` Methode legt die spezifischen Parameter im JWT Bearer-Handler fest, die zum Überprüfen von Token erforderlich sind, die vom Azure Active Directory B2C emittiert werden:</span><span class="sxs-lookup"><span data-stu-id="5db2e-173">The `AddAzureADB2CBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="5db2e-174">`UseAuthentication`und `UseAuthorization` stellen Sie sicher, dass:</span><span class="sxs-lookup"><span data-stu-id="5db2e-174">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="5db2e-175">Die App versucht, Token für eingehende Anforderungen zu analysieren und zu validieren.</span><span class="sxs-lookup"><span data-stu-id="5db2e-175">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="5db2e-176">Jede Anforderung, die versucht, ohne ordnungsgemäße Anmeldeinformationen auf eine geschützte Ressource zuzugreifen, schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="5db2e-176">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="5db2e-177">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="5db2e-177">User.Identity.Name</span></span>

<span data-ttu-id="5db2e-178">Standardmäßig wird `User.Identity.Name` der nicht aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="5db2e-178">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="5db2e-179">Um die App so zu `name` konfigurieren, dass sie den Wert vom Anspruchstyp empfängt, konfigurieren Sie die [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) der <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in: `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="5db2e-179">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="5db2e-180">App-Einstellungen</span><span class="sxs-lookup"><span data-stu-id="5db2e-180">App settings</span></span>

<span data-ttu-id="5db2e-181">Die Datei *appsettings.json* enthält die Optionen zum Konfigurieren des JWT-Trägerhandlers, der zum Überprüfen von Zugriffstoken verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="5db2e-181">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://{ORGANIZATION}.b2clogin.com/",
    "ClientId": "{API CLIENT ID}",
    "Domain": "{DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="5db2e-182">WeatherForecast-Controller</span><span class="sxs-lookup"><span data-stu-id="5db2e-182">WeatherForecast controller</span></span>

<span data-ttu-id="5db2e-183">Der WeatherForecast-Controller (*Controllers/WeatherForecastController.cs*) macht `[Authorize]` eine geschützte API mit dem attribut verfügbar, das auf den Controller angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="5db2e-183">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="5db2e-184">Es ist **wichtig** zu verstehen, dass:</span><span class="sxs-lookup"><span data-stu-id="5db2e-184">It's **important** to understand that:</span></span>

* <span data-ttu-id="5db2e-185">Das `[Authorize]` Attribut in diesem API-Controller ist das einzige, was diese API vor unbefugtem Zugriff schützt.</span><span class="sxs-lookup"><span data-stu-id="5db2e-185">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="5db2e-186">Das `[Authorize]` in der Blazor WebAssembly-App verwendete Attribut dient nur als Hinweis für die App, dass der Benutzer autorisiert werden soll, damit die App ordnungsgemäß funktioniert.</span><span class="sxs-lookup"><span data-stu-id="5db2e-186">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="5db2e-187">Client-App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="5db2e-187">Client app configuration</span></span>

<span data-ttu-id="5db2e-188">*Dieser Abschnitt bezieht sich auf die **Client-App** der Lösung.*</span><span class="sxs-lookup"><span data-stu-id="5db2e-188">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="5db2e-189">Authentifizierungspaket</span><span class="sxs-lookup"><span data-stu-id="5db2e-189">Authentication package</span></span>

<span data-ttu-id="5db2e-190">Wenn eine App erstellt wird, um ein`IndividualB2C`individuelles B2C-Konto ( )`Microsoft.Authentication.WebAssembly.Msal`zu verwenden, erhält die App automatisch eine Paketreferenz für die [Microsoft-Authentifizierungsbibliothek](/azure/active-directory/develop/msal-overview) ( ).</span><span class="sxs-lookup"><span data-stu-id="5db2e-190">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="5db2e-191">Das Paket stellt eine Reihe von Primitiven bereit, die der App helfen, Benutzer zu authentifizieren und Token zum Aufrufen geschützter APIs zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="5db2e-191">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="5db2e-192">Wenn Sie einer App eine Authentifizierung hinzufügen, fügen Sie das Paket manuell zur Projektdatei der App hinzu:</span><span class="sxs-lookup"><span data-stu-id="5db2e-192">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="5db2e-193">Ersetzen `{VERSION}` Sie im vorherigen Paketverweis `Microsoft.AspNetCore.Blazor.Templates` durch die <xref:blazor/get-started> im Artikel gezeigte Version des Pakets.</span><span class="sxs-lookup"><span data-stu-id="5db2e-193">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="5db2e-194">Das `Microsoft.Authentication.WebAssembly.Msal` Paket fügt das `Microsoft.AspNetCore.Components.WebAssembly.Authentication` Paket transitiv zur App hinzu.</span><span class="sxs-lookup"><span data-stu-id="5db2e-194">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="5db2e-195">Unterstützung des Authentifizierungsdienstes</span><span class="sxs-lookup"><span data-stu-id="5db2e-195">Authentication service support</span></span>

<span data-ttu-id="5db2e-196">Unterstützung für die Authentifizierung von Benutzern wird `AddMsalAuthentication` im Dienstcontainer `Microsoft.Authentication.WebAssembly.Msal` mit der vom Paket bereitgestellten Erweiterungsmethode registriert.</span><span class="sxs-lookup"><span data-stu-id="5db2e-196">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="5db2e-197">Diese Methode richtet alle Dienste ein, die für die Interaktion der App mit dem Identitätsanbieter (IDENTITY Provider, IP) erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="5db2e-197">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="5db2e-198">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="5db2e-198">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="5db2e-199">Die `AddMsalAuthentication` Methode akzeptiert einen Rückruf, um die Parameter zu konfigurieren, die zum Authentifizieren einer App erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="5db2e-199">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="5db2e-200">Die zum Konfigurieren der App erforderlichen Werte können bei der Registrierung der App über die Azure Portal AAD-Konfiguration abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="5db2e-200">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

### <a name="access-token-scopes"></a><span data-ttu-id="5db2e-201">Zugriffstokenbereiche</span><span class="sxs-lookup"><span data-stu-id="5db2e-201">Access token scopes</span></span>

<span data-ttu-id="5db2e-202">Die Standardzugriffstokenbereiche stellen die Liste der Zugriffstokenbereiche dar, die:</span><span class="sxs-lookup"><span data-stu-id="5db2e-202">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="5db2e-203">Standardmäßig in der Anmeldeanforderung enthalten.</span><span class="sxs-lookup"><span data-stu-id="5db2e-203">Included by default in the sign in request.</span></span>
* <span data-ttu-id="5db2e-204">Wird verwendet, um ein Zugriffstoken unmittelbar nach der Authentifizierung zu bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="5db2e-204">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="5db2e-205">Alle Bereiche müssen gemäß Azure Active Directory-Regeln derselben App angehören.</span><span class="sxs-lookup"><span data-stu-id="5db2e-205">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="5db2e-206">Bei Bedarf können zusätzliche Bereiche für zusätzliche API-Apps hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="5db2e-206">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="5db2e-207">Wenn das Azure-Portal einen Bereichs-URI bereitstellt und **die App eine nicht behandelte Ausnahme auslöst,** wenn sie eine *401 nicht autorisierte* Antwort von der API empfängt, versuchen Sie, einen Bereichs-URI zu verwenden, der das Schema und den Host nicht enthält.</span><span class="sxs-lookup"><span data-stu-id="5db2e-207">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="5db2e-208">Das Azure-Portal kann z. B. eines der folgenden Bereichs-URI-Formate bereitstellen:</span><span class="sxs-lookup"><span data-stu-id="5db2e-208">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="5db2e-209">Geben Sie den Bereichs-URI ohne Schema und Host:</span><span class="sxs-lookup"><span data-stu-id="5db2e-209">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="5db2e-210">Weitere Informationen finden Sie unter <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="5db2e-210">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

### <a name="imports-file"></a><span data-ttu-id="5db2e-211">Imports-Datei</span><span class="sxs-lookup"><span data-stu-id="5db2e-211">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="5db2e-212">Indexseite</span><span class="sxs-lookup"><span data-stu-id="5db2e-212">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="5db2e-213">App-Komponente</span><span class="sxs-lookup"><span data-stu-id="5db2e-213">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="5db2e-214">RedirectToLogin-Komponente</span><span class="sxs-lookup"><span data-stu-id="5db2e-214">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="5db2e-215">LoginDisplay-Komponente</span><span class="sxs-lookup"><span data-stu-id="5db2e-215">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="5db2e-216">Authentifizierungskomponente</span><span class="sxs-lookup"><span data-stu-id="5db2e-216">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="5db2e-217">FetchData-Komponente</span><span class="sxs-lookup"><span data-stu-id="5db2e-217">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="5db2e-218">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="5db2e-218">Run the app</span></span>

<span data-ttu-id="5db2e-219">Führen Sie die App aus dem Serverprojekt aus.</span><span class="sxs-lookup"><span data-stu-id="5db2e-219">Run the app from the Server project.</span></span> <span data-ttu-id="5db2e-220">Wenn Sie Visual Studio verwenden, wählen Sie das Serverprojekt im **Projektmappen-Explorer** aus, und wählen Sie die Schaltfläche **Ausführen** in der Symbolleiste aus, oder starten Sie die App im **Menü Debug.**</span><span class="sxs-lookup"><span data-stu-id="5db2e-220">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="5db2e-221">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="5db2e-221">Additional resources</span></span>

* [<span data-ttu-id="5db2e-222">Zusätzliche Zugriffstoken anfordern</span><span class="sxs-lookup"><span data-stu-id="5db2e-222">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="5db2e-223">Tutorial zum Erstellen eines Azure Active Directory B2C-Mandanten</span><span class="sxs-lookup"><span data-stu-id="5db2e-223">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="5db2e-224">Dokumentation zur Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="5db2e-224">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)

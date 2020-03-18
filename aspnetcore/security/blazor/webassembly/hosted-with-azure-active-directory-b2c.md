---
title: Sichern eines ASP.net Core Blazor gehosteten Webassembly-App mit Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 12e09cf7e27f85473d84f42564d13e1c0ed5dff1
ms.sourcegitcommit: 5bdc54162d7dea8d9fa54ac3055678db23586af1
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/17/2020
ms.locfileid: "79434446"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="02153-102">Sichern eines ASP.net Core Blazor gehosteten Webassembly-App mit Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="02153-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="02153-103">Von [Javier calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="02153-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="02153-104">In diesem Artikel wird beschrieben, wie Sie eine eigenständige Blazor Webassembly-app erstellen, die [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) für die Authentifizierung verwendet.</span><span class="sxs-lookup"><span data-stu-id="02153-104">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="02153-105">Registrieren von apps in Aad B2C und Erstellen einer Lösung</span><span class="sxs-lookup"><span data-stu-id="02153-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="02153-106">Erstellen eines Mandanten</span><span class="sxs-lookup"><span data-stu-id="02153-106">Create a tenant</span></span>

<span data-ttu-id="02153-107">Befolgen Sie die Anweisungen im [Tutorial: Erstellen eines Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) Mandanten, um einen Aad B2C-Mandanten zu erstellen, und notieren Sie die folgenden Informationen:</span><span class="sxs-lookup"><span data-stu-id="02153-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant and record the following information:</span></span>

* <span data-ttu-id="02153-108">Aad B2C Instanz (z. b. `https://contoso.b2clogin.com/`, einschließlich des nachgestellten Schrägstrichs)</span><span class="sxs-lookup"><span data-stu-id="02153-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="02153-109">Aad B2C Mandanten Domäne (z. b. `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="02153-109">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="02153-110">Registrieren einer Server-API-APP</span><span class="sxs-lookup"><span data-stu-id="02153-110">Register a server API app</span></span>

<span data-ttu-id="02153-111">Befolgen Sie die Anweisungen im [Tutorial: Registrieren einer Anwendung in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) , um eine Aad-App für die *Server-API-APP* im **Azure Active Directory** > **App-Registrierungen** Bereich der Azure-Portal zu registrieren:</span><span class="sxs-lookup"><span data-stu-id="02153-111">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="02153-112">Wählen Sie **Neue Registrierung** aus.</span><span class="sxs-lookup"><span data-stu-id="02153-112">Select **New registration**.</span></span>
1. <span data-ttu-id="02153-113">Geben Sie einen **Namen** für die APP an (z. b. **Blazor Server Aad B2C**).</span><span class="sxs-lookup"><span data-stu-id="02153-113">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="02153-114">Wählen Sie für **unterstützte Konto Typen** **in einem beliebigen Organisations Verzeichnis oder einem beliebigen Identitäts Anbieter Konten aus. Zum Authentifizieren von Benutzern mit Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="02153-114">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="02153-115">(mehr Instanzen fähig).</span><span class="sxs-lookup"><span data-stu-id="02153-115">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="02153-116">Für die *Server-API-APP* ist in diesem Szenario kein **Umleitungs-URI** erforderlich. lassen Sie die Dropdown-Datei also auf **Web** festgelegt und geben Sie keinen Umleitungs-URI</span><span class="sxs-lookup"><span data-stu-id="02153-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="02153-117">Vergewissern Sie sich \*\*, dass die Berechtigungen > \*\* **admin keine Zustimmung für OpenID und offline_access Berechtigungen gewähren** aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="02153-117">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="02153-118">Wählen Sie **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="02153-118">Select **Register**.</span></span>

<span data-ttu-id="02153-119">In machen Sie **eine API**verfügbar:</span><span class="sxs-lookup"><span data-stu-id="02153-119">In **Expose an API**:</span></span>

1. <span data-ttu-id="02153-120">Wählen Sie **Bereich hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="02153-120">Select **Add a scope**.</span></span>
1. <span data-ttu-id="02153-121">Wählen Sie **Speichern und fortfahren** aus.</span><span class="sxs-lookup"><span data-stu-id="02153-121">Select **Save and continue**.</span></span>
1. <span data-ttu-id="02153-122">Geben Sie einen **Bereichs Namen** an (z. b. `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="02153-122">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="02153-123">Geben Sie einen **anzeigen Amen für die Administrator Zustimmung** an (z. b. `Access API`).</span><span class="sxs-lookup"><span data-stu-id="02153-123">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="02153-124">Geben Sie eine Beschreibung für die **Administrator Zustimmung** an (z. b. `Allows the app to access server app API endpoints.`).</span><span class="sxs-lookup"><span data-stu-id="02153-124">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="02153-125">Vergewissern Sie sich, dass der **Status** auf **aktiviert**festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="02153-125">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="02153-126">Wählen Sie **Bereich hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="02153-126">Select **Add scope**.</span></span>

<span data-ttu-id="02153-127">Notieren Sie sich die folgenden Informationen:</span><span class="sxs-lookup"><span data-stu-id="02153-127">Record the following information:</span></span>

* <span data-ttu-id="02153-128">*Server-API-APP* Anwendungs-ID (Client-ID) (z. b. `11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="02153-128">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="02153-129">Verzeichnis-ID (Mandanten-ID) (z. b. `222222222-2222-2222-2222-222222222222`)</span><span class="sxs-lookup"><span data-stu-id="02153-129">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="02153-130">*Server-API-APP* APP-ID-URI (z. b. `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`die Azure-Portal den Wert möglicherweise als Standardwert für die Client-ID)</span><span class="sxs-lookup"><span data-stu-id="02153-130">*Server API app* App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, the Azure portal might default the value to the Client ID)</span></span>
* <span data-ttu-id="02153-131">Standardbereich (z. b. `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="02153-131">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="02153-132">Registrieren einer Client-App</span><span class="sxs-lookup"><span data-stu-id="02153-132">Register a client app</span></span>

<span data-ttu-id="02153-133">Befolgen Sie die Anweisungen im [Tutorial: Registrieren einer Anwendung in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) erneut, um eine Aad-App für die *Client-App* in der **Azure Active Directory** > **App-Registrierungen** Bereich der Azure-Portal zu registrieren:</span><span class="sxs-lookup"><span data-stu-id="02153-133">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="02153-134">Wählen Sie **Neue Registrierung** aus.</span><span class="sxs-lookup"><span data-stu-id="02153-134">Select **New registration**.</span></span>
1. <span data-ttu-id="02153-135">Geben Sie einen **Namen** für die APP an (z. b. **Blazor Client Aad B2C**).</span><span class="sxs-lookup"><span data-stu-id="02153-135">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="02153-136">Wählen Sie für **unterstützte Konto Typen** **in einem beliebigen Organisations Verzeichnis oder einem beliebigen Identitäts Anbieter Konten aus. Zum Authentifizieren von Benutzern mit Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="02153-136">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="02153-137">(mehr Instanzen fähig).</span><span class="sxs-lookup"><span data-stu-id="02153-137">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="02153-138">Lassen Sie die Dropdown-Einstellung für **Umleitungs-URI** auf **Web**und einen Umleitungs-URI `https://localhost:5001/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="02153-138">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="02153-139">Vergewissern Sie sich \*\*, dass die Berechtigungen > \*\* **admin keine Zustimmung für OpenID und offline_access Berechtigungen gewähren** aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="02153-139">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="02153-140">Wählen Sie **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="02153-140">Select **Register**.</span></span>

<span data-ttu-id="02153-141">In der **Authentifizierung** > **Platt Form Konfigurationen** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="02153-141">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="02153-142">Vergewissern Sie sich, dass der **Umleitungs-URI** `https://localhost:5001/authentication/login-callback` vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="02153-142">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="02153-143">Aktivieren Sie für **implizite Gewährung**die Kontrollkästchen für **Zugriffs Token** und **ID-Token**.</span><span class="sxs-lookup"><span data-stu-id="02153-143">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="02153-144">Die verbleibenden Standardwerte für die APP sind für diese Art von akzeptabel.</span><span class="sxs-lookup"><span data-stu-id="02153-144">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="02153-145">Wählen Sie die Schaltfläche **Speichern** aus.</span><span class="sxs-lookup"><span data-stu-id="02153-145">Select the **Save** button.</span></span>

<span data-ttu-id="02153-146">In **API-Berechtigungen**:</span><span class="sxs-lookup"><span data-stu-id="02153-146">In **API permissions**:</span></span>

1. <span data-ttu-id="02153-147">Vergewissern Sie sich, dass die APP über **Microsoft Graph** > **User. Read** -Berechtigung verfügt.</span><span class="sxs-lookup"><span data-stu-id="02153-147">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="02153-148">Wählen Sie **Berechtigung hinzufügen** und dann **meine APIs**aus.</span><span class="sxs-lookup"><span data-stu-id="02153-148">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="02153-149">Wählen Sie die *Server-API-APP* aus der Spalte **Name** aus (z. b. **Blazor Server Aad B2C**).</span><span class="sxs-lookup"><span data-stu-id="02153-149">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="02153-150">Öffnen Sie die **API** -Liste.</span><span class="sxs-lookup"><span data-stu-id="02153-150">Open the **API** list.</span></span>
1. <span data-ttu-id="02153-151">Aktivieren Sie den Zugriff auf die API (z. b. `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="02153-151">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="02153-152">Wählen Sie **Berechtigungen hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="02153-152">Select **Add permissions**.</span></span>
1. <span data-ttu-id="02153-153">Wählen Sie die Schaltfläche **Administrator Inhalt für {TENANT Name} erteilen aus** .</span><span class="sxs-lookup"><span data-stu-id="02153-153">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="02153-154">Klicken Sie auf **Ja**, um zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="02153-154">Select **Yes** to confirm.</span></span>

<span data-ttu-id="02153-155">In der **Start** > **Azure AD B2C** > **benutzerflows**:</span><span class="sxs-lookup"><span data-stu-id="02153-155">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="02153-156">Erstellen eines Anmeldungs-und Anmelde-benutzerflows</span><span class="sxs-lookup"><span data-stu-id="02153-156">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="02153-157">Wählen Sie mindestens das **Anwendungs Anspruchs** > Benutzer Attribut " **Anzeige Name** " aus, um die `context.User.Identity.Name` in der `LoginDisplay` Komponente (*Shared/logindisplay. Razor*) aufzufüllen.</span><span class="sxs-lookup"><span data-stu-id="02153-157">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="02153-158">Notieren Sie sich die folgenden Informationen:</span><span class="sxs-lookup"><span data-stu-id="02153-158">Record the following information:</span></span>

* <span data-ttu-id="02153-159">Notieren Sie die Anwendungs-ID der *Client* Anwendung (Client-ID) (z. b. `33333333-3333-3333-3333-333333333333`).</span><span class="sxs-lookup"><span data-stu-id="02153-159">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>
* <span data-ttu-id="02153-160">Zeichnen Sie den für die APP erstellten Registrierungs-und Anmeldedaten Flussnamen auf (z. b. `B2C_1_signupsignin`).</span><span class="sxs-lookup"><span data-stu-id="02153-160">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="02153-161">Erstellen der App</span><span class="sxs-lookup"><span data-stu-id="02153-161">Create the app</span></span>

<span data-ttu-id="02153-162">Ersetzen Sie die Platzhalter im folgenden Befehl durch die zuvor aufgezeichneten Informationen, und führen Sie den Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="02153-162">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="02153-163">Um den Ausgabe Speicherort anzugeben, der einen Projektordner erstellt, wenn er nicht vorhanden ist, fügen Sie die Output-Option im Befehl mit einem Pfad (z. b. `-o BlazorSample`) ein.</span><span class="sxs-lookup"><span data-stu-id="02153-163">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="02153-164">Der Name des Ordners wird auch Teil des Projekt namens.</span><span class="sxs-lookup"><span data-stu-id="02153-164">The folder name also becomes part of the project's name.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="02153-165">Server-App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="02153-165">Server app configuration</span></span>

<span data-ttu-id="02153-166">*Dieser Abschnitt bezieht sich auf die **Server** -App der Lösung.*</span><span class="sxs-lookup"><span data-stu-id="02153-166">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="02153-167">Authentifizierungs Paket</span><span class="sxs-lookup"><span data-stu-id="02153-167">Authentication package</span></span>

<span data-ttu-id="02153-168">Die Unterstützung für die Authentifizierung und Autorisierung von Aufrufen von ASP.net Core Web-APIs wird vom `Microsoft.AspNetCore.Authentication.AzureAD.UI`bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="02153-168">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="02153-169">Unterstützung für Authentifizierungsdienst</span><span class="sxs-lookup"><span data-stu-id="02153-169">Authentication service support</span></span>

<span data-ttu-id="02153-170">Die `AddAuthentication`-Methode richtet Authentifizierungsdienste innerhalb der APP ein und konfiguriert den JWT-bearerhandler als Standard Authentifizierungsmethode.</span><span class="sxs-lookup"><span data-stu-id="02153-170">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="02153-171">Die `AddAzureADBearer`-Methode richtet die spezifischen Parameter im JWT-bearerhandler ein, der zum Überprüfen der vom Azure Active Directory ausgegebenen Token erforderlich ist:</span><span class="sxs-lookup"><span data-stu-id="02153-171">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="02153-172">`UseAuthentication` und `UseAuthorization` Folgendes sicherstellen:</span><span class="sxs-lookup"><span data-stu-id="02153-172">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="02153-173">Die APP versucht, Token bei eingehenden Anforderungen zu analysieren und zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="02153-173">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="02153-174">Jede Anforderung, die versucht, auf eine geschützte Ressource ohne korrekte Anmelde Informationen zuzugreifen, schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="02153-174">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="app-settings"></a><span data-ttu-id="02153-175">App-Einstellungen</span><span class="sxs-lookup"><span data-stu-id="02153-175">App settings</span></span>

<span data-ttu-id="02153-176">Die Datei *appSettings. JSON* enthält die Optionen zum Konfigurieren des JWT-bearerhandlers, der zum Überprüfen von Zugriffs Token verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="02153-176">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "{API CLIENT ID}",
    "Domain": "{DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="02153-177">Weatherforecast-Controller</span><span class="sxs-lookup"><span data-stu-id="02153-177">WeatherForecast controller</span></span>

<span data-ttu-id="02153-178">Der weatherforecast-Controller (*Controllers/weatherforecastcontroller. cs*) macht eine geschützte API verfügbar, wobei das `[Authorize]`-Attribut auf den Controller angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="02153-178">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="02153-179">Es ist **wichtig** , Folgendes zu verstehen:</span><span class="sxs-lookup"><span data-stu-id="02153-179">It's **important** to understand that:</span></span>

* <span data-ttu-id="02153-180">Das `[Authorize]`-Attribut in diesem API-Controller ist die einzige Möglichkeit, diese API vor nicht autorisiertem Zugriff zu schützen.</span><span class="sxs-lookup"><span data-stu-id="02153-180">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="02153-181">Das `[Authorize]`-Attribut, das in der Blazor Webassembly-App verwendet wird, dient nur als Hinweis für die APP, dass der Benutzer autorisiert werden muss, damit die APP ordnungsgemäß funktioniert.</span><span class="sxs-lookup"><span data-stu-id="02153-181">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="02153-182">Client-App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="02153-182">Client app configuration</span></span>

<span data-ttu-id="02153-183">*Dieser Abschnitt bezieht sich auf die **Client** -App der Lösung.*</span><span class="sxs-lookup"><span data-stu-id="02153-183">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="02153-184">Authentifizierungs Paket</span><span class="sxs-lookup"><span data-stu-id="02153-184">Authentication package</span></span>

<span data-ttu-id="02153-185">Wenn eine App erstellt wird, um ein einzelnes B2C-Konto (`IndividualB2C`) zu verwenden, erhält die APP automatisch einen Paket Verweis für die [Microsoft-Authentifizierungs Bibliothek](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="02153-185">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="02153-186">Das Paket enthält eine Reihe von primitiven, die der APP helfen, Benutzer zu authentifizieren und Token abzurufen, um geschützte APIs aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="02153-186">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="02153-187">Wenn Sie eine Authentifizierung zu einer APP hinzufügen, fügen Sie das Paket manuell zur Projektdatei der APP hinzu:</span><span class="sxs-lookup"><span data-stu-id="02153-187">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="02153-188">Ersetzen Sie `{VERSION}` im vorangehenden Paket Verweis durch die Version des `Microsoft.AspNetCore.Blazor.Templates` Pakets, das im <xref:blazor/get-started> Artikel angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="02153-188">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="02153-189">Das `Microsoft.Authentication.WebAssembly.Msal` Paket fügt das `Microsoft.AspNetCore.Components.WebAssembly.Authentication` Paket transitiv der APP hinzu.</span><span class="sxs-lookup"><span data-stu-id="02153-189">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="02153-190">Unterstützung für Authentifizierungsdienst</span><span class="sxs-lookup"><span data-stu-id="02153-190">Authentication service support</span></span>

<span data-ttu-id="02153-191">Die Unterstützung für das Authentifizieren von Benutzern wird im Dienst Container mit der `AddMsalAuthentication`-Erweiterungsmethode registriert, die vom `Microsoft.Authentication.WebAssembly.Msal`-Paket bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="02153-191">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="02153-192">Diese Methode richtet alle Dienste ein, die erforderlich sind, damit die APP mit dem Identitäts Anbieter (IP) interagieren muss.</span><span class="sxs-lookup"><span data-stu-id="02153-192">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="02153-193">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="02153-193">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{APP ID URI}/{DEFAULT SCOPE}");
});
```

<span data-ttu-id="02153-194">Die `AddMsalAuthentication`-Methode akzeptiert einen Rückruf, um die Parameter zu konfigurieren, die zum Authentifizieren einer APP erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="02153-194">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="02153-195">Die für die Konfiguration der APP erforderlichen Werte können aus der Aad-Konfiguration des Azure-Portals abgerufen werden, wenn Sie die APP registrieren.</span><span class="sxs-lookup"><span data-stu-id="02153-195">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="02153-196">Mit der Vorlage Blazor Webassembly wird die APP automatisch so konfiguriert, dass ein Zugriffs Token für eine sichere API für den Standardbereich angefordert wird, der für den `dotnet new` Befehl (`{APP ID URI}/{DEFAULT SCOPE}`) bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="02153-196">The Blazor WebAssembly template automatically configures the app to request an access token for a secure API for the default scope provided to the `dotnet new` command (`{APP ID URI}/{DEFAULT SCOPE}`).</span></span>

<span data-ttu-id="02153-197">Die standardzugriffstokenbereiche stellen die Liste der zugriffstokenbereiche dar, die lauten:</span><span class="sxs-lookup"><span data-stu-id="02153-197">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="02153-198">Standardmäßig in der Anmelde Anforderung enthalten.</span><span class="sxs-lookup"><span data-stu-id="02153-198">Included by default in the sign in request.</span></span>
* <span data-ttu-id="02153-199">Wird verwendet, um unmittelbar nach der Authentifizierung ein Zugriffs Token bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="02153-199">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="02153-200">Alle Bereiche müssen pro Azure Active Directory Regeln zur gleichen App gehören.</span><span class="sxs-lookup"><span data-stu-id="02153-200">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="02153-201">Für zusätzliche API-Apps können nach Bedarf weitere Bereiche hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="02153-201">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{APP ID URI}/{SCOPE}");
});
```

### <a name="index-page"></a><span data-ttu-id="02153-202">Indexseite</span><span class="sxs-lookup"><span data-stu-id="02153-202">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

### <a name="app-component"></a><span data-ttu-id="02153-203">App-Komponente</span><span class="sxs-lookup"><span data-stu-id="02153-203">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="02153-204">Redirecttologin-Komponente</span><span class="sxs-lookup"><span data-stu-id="02153-204">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="02153-205">Logindisplay-Komponente</span><span class="sxs-lookup"><span data-stu-id="02153-205">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="02153-206">Authentifizierungs Komponente</span><span class="sxs-lookup"><span data-stu-id="02153-206">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="02153-207">Fetchdata-Komponente</span><span class="sxs-lookup"><span data-stu-id="02153-207">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="02153-208">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="02153-208">Run the app</span></span>

<span data-ttu-id="02153-209">Führen Sie die APP aus dem Server Projekt aus.</span><span class="sxs-lookup"><span data-stu-id="02153-209">Run the app from the Server project.</span></span> <span data-ttu-id="02153-210">Wenn Sie Visual Studio verwenden, wählen Sie das Server Projekt in **Projektmappen-Explorer** aus, und klicken Sie auf der Symbolleiste auf die Schaltfläche **Ausführen** , oder starten Sie die APP im Menü **Debuggen**</span><span class="sxs-lookup"><span data-stu-id="02153-210">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="02153-211">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="02153-211">Additional resources</span></span>

* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="02153-212">Tutorial zum Erstellen eines Azure Active Directory B2C-Mandanten</span><span class="sxs-lookup"><span data-stu-id="02153-212">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)

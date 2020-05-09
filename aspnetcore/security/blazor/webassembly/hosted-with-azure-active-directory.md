---
title: Sichern einer ASP.net Core Blazor Webassembly-gehosteten App mit Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/06/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: aaae2b755d6d6e74db0cb7676820d01964c2add4
ms.sourcegitcommit: 363e3a2a035f4082cb92e7b75ed150ba304258b3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/08/2020
ms.locfileid: "82976804"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="d60ac-102">Sichern einer ASP.net Core Blazor Webassembly-gehosteten App mit Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="d60ac-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="d60ac-103">Von [Javier calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d60ac-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="d60ac-104">In diesem Artikel wird beschrieben, wie Sie eine [ Blazor Webassembly-gehostete App](xref:blazor/hosting-models#blazor-webassembly) erstellen, die [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) für die Authentifizierung verwendet.</span><span class="sxs-lookup"><span data-stu-id="d60ac-104">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="d60ac-105">Registrieren von apps in Aad B2C und Erstellen einer Lösung</span><span class="sxs-lookup"><span data-stu-id="d60ac-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="d60ac-106">Erstellen eines Mandanten</span><span class="sxs-lookup"><span data-stu-id="d60ac-106">Create a tenant</span></span>

<span data-ttu-id="d60ac-107">Befolgen Sie die Anweisungen unter [Schnellstart: Einrichten eines](/azure/active-directory/develop/quickstart-create-new-tenant) Mandanten zum Erstellen eines Mandanten in Aad.</span><span class="sxs-lookup"><span data-stu-id="d60ac-107">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="d60ac-108">Registrieren einer Server-API-APP</span><span class="sxs-lookup"><span data-stu-id="d60ac-108">Register a server API app</span></span>

<span data-ttu-id="d60ac-109">Befolgen Sie die Anleitungen [unter Schnellstart: Registrieren einer Anwendung mit der Microsoft Identity Platform](/azure/active-directory/develop/quickstart-register-app) und nachfolgende Azure Aad-Themen zum Registrieren einer Aad-App für die *Server-API-APP* im **Azure Active Directory** > **App-Registrierungen** Bereich der Azure-Portal:</span><span class="sxs-lookup"><span data-stu-id="d60ac-109">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="d60ac-110">Wählen Sie **Neue Registrierung** aus.</span><span class="sxs-lookup"><span data-stu-id="d60ac-110">Select **New registration**.</span></span>
1. <span data-ttu-id="d60ac-111">Geben Sie einen **Namen** für die APP an (z. b. \*\* Blazor Server Aad\*\*).</span><span class="sxs-lookup"><span data-stu-id="d60ac-111">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="d60ac-112">Wählen Sie einen **unterstützten Kontotyp**aus.</span><span class="sxs-lookup"><span data-stu-id="d60ac-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="d60ac-113">Für diese Vorgehens Art können Sie **nur Konten in diesem Organisations Verzeichnis** auswählen (einzelner Mandant).</span><span class="sxs-lookup"><span data-stu-id="d60ac-113">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="d60ac-114">Für die *Server-API-APP* ist in diesem Szenario kein **Umleitungs-URI** erforderlich. lassen Sie die Dropdown-Datei also auf **Web** festgelegt und geben Sie keinen Umleitungs-URI</span><span class="sxs-lookup"><span data-stu-id="d60ac-114">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="d60ac-115">Deaktivieren Sie **das** > Kontrollkästchen**Administrator Berechtigungen für OpenID und offline_access Berechtigungen gewähren** .</span><span class="sxs-lookup"><span data-stu-id="d60ac-115">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="d60ac-116">Wählen Sie **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="d60ac-116">Select **Register**.</span></span>

<span data-ttu-id="d60ac-117">Entfernen Sie unter **API-Berechtigungen**die Berechtigung **Microsoft Graph** > **User. Read** , da die APP keinen Anmelde-oder uerprofilzugriff erfordert.</span><span class="sxs-lookup"><span data-stu-id="d60ac-117">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or uer profile access.</span></span>

<span data-ttu-id="d60ac-118">In machen Sie **eine API**verfügbar:</span><span class="sxs-lookup"><span data-stu-id="d60ac-118">In **Expose an API**:</span></span>

1. <span data-ttu-id="d60ac-119">Wählen Sie **Bereich hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="d60ac-119">Select **Add a scope**.</span></span>
1. <span data-ttu-id="d60ac-120">Wählen Sie **Speichern und fortfahren** aus.</span><span class="sxs-lookup"><span data-stu-id="d60ac-120">Select **Save and continue**.</span></span>
1. <span data-ttu-id="d60ac-121">Geben Sie einen **Bereichs Namen** an (z `API.Access`. b.).</span><span class="sxs-lookup"><span data-stu-id="d60ac-121">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="d60ac-122">Geben Sie einen **anzeigen Amen für die Administrator Zustimmung** an `Access API`(z. b.).</span><span class="sxs-lookup"><span data-stu-id="d60ac-122">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="d60ac-123">Geben Sie eine Beschreibung für die **Administrator Zustimmung** an `Allows the app to access server app API endpoints.`(z. b.).</span><span class="sxs-lookup"><span data-stu-id="d60ac-123">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="d60ac-124">Vergewissern Sie sich, dass der **Status** auf **aktiviert**festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="d60ac-124">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="d60ac-125">Wählen Sie **Bereich hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="d60ac-125">Select **Add scope**.</span></span>

<span data-ttu-id="d60ac-126">Notieren Sie sich die folgenden Informationen:</span><span class="sxs-lookup"><span data-stu-id="d60ac-126">Record the following information:</span></span>

* <span data-ttu-id="d60ac-127">*Server-API-APP* Anwendungs-ID (Client-ID) (z `11111111-1111-1111-1111-111111111111`. b.)</span><span class="sxs-lookup"><span data-stu-id="d60ac-127">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="d60ac-128">APP-ID-URI (z `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111`. b., oder der von Ihnen angegebene benutzerdefinierte Wert)</span><span class="sxs-lookup"><span data-stu-id="d60ac-128">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="d60ac-129">Verzeichnis-ID (Mandanten-ID) (z `222222222-2222-2222-2222-222222222222`. b.)</span><span class="sxs-lookup"><span data-stu-id="d60ac-129">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="d60ac-130">Aad-Mandanten Domäne (z. `contoso.onmicrosoft.com`b.)</span><span class="sxs-lookup"><span data-stu-id="d60ac-130">AAD Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>
* <span data-ttu-id="d60ac-131">Standardbereich (z. b `API.Access`.)</span><span class="sxs-lookup"><span data-stu-id="d60ac-131">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="d60ac-132">Registrieren einer Client-App</span><span class="sxs-lookup"><span data-stu-id="d60ac-132">Register a client app</span></span>

<span data-ttu-id="d60ac-133">Befolgen Sie die Anleitungen [unter Schnellstart: Registrieren einer Anwendung mit der Microsoft Identity Platform](/azure/active-directory/develop/quickstart-register-app) und nachfolgende Azure Aad-Themen zum Registrieren einer Aad-App für die *Client-App* im **Azure Active Directory** > **App-Registrierungen** Bereich der Azure-Portal:</span><span class="sxs-lookup"><span data-stu-id="d60ac-133">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="d60ac-134">Wählen Sie **Neue Registrierung** aus.</span><span class="sxs-lookup"><span data-stu-id="d60ac-134">Select **New registration**.</span></span>
1. <span data-ttu-id="d60ac-135">Geben Sie einen **Namen** für die APP an (z. b. \*\* Blazor Client Aad\*\*).</span><span class="sxs-lookup"><span data-stu-id="d60ac-135">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="d60ac-136">Wählen Sie einen **unterstützten Kontotyp**aus.</span><span class="sxs-lookup"><span data-stu-id="d60ac-136">Choose a **Supported account types**.</span></span> <span data-ttu-id="d60ac-137">Für diese Vorgehens Art können Sie **nur Konten in diesem Organisations Verzeichnis** auswählen (einzelner Mandant).</span><span class="sxs-lookup"><span data-stu-id="d60ac-137">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="d60ac-138">Belassen Sie den **Umleitungs-URI** -Dropdown auf **Web**, und geben Sie `https://localhost:5001/authentication/login-callback`einen Umleitungs-URI von an.</span><span class="sxs-lookup"><span data-stu-id="d60ac-138">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="d60ac-139">Deaktivieren Sie **das** > Kontrollkästchen**Administrator Berechtigungen für OpenID und offline_access Berechtigungen gewähren** .</span><span class="sxs-lookup"><span data-stu-id="d60ac-139">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="d60ac-140">Wählen Sie **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="d60ac-140">Select **Register**.</span></span>

<span data-ttu-id="d60ac-141">Im**Web**der **Authentifizierungs** > **Platt Form Konfigurationen** > :</span><span class="sxs-lookup"><span data-stu-id="d60ac-141">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="d60ac-142">Bestätigen Sie, dass der `https://localhost:5001/authentication/login-callback` **Umleitungs-URI** von vorhanden ist</span><span class="sxs-lookup"><span data-stu-id="d60ac-142">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="d60ac-143">Aktivieren Sie für **implizite Gewährung**die Kontrollkästchen für **Zugriffs Token** und **ID-Token**.</span><span class="sxs-lookup"><span data-stu-id="d60ac-143">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="d60ac-144">Die verbleibenden Standardwerte für die APP sind für diese Art von akzeptabel.</span><span class="sxs-lookup"><span data-stu-id="d60ac-144">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="d60ac-145">Wählen Sie die Schaltfläche **Speichern** aus.</span><span class="sxs-lookup"><span data-stu-id="d60ac-145">Select the **Save** button.</span></span>

<span data-ttu-id="d60ac-146">In **API-Berechtigungen**:</span><span class="sxs-lookup"><span data-stu-id="d60ac-146">In **API permissions**:</span></span>

1. <span data-ttu-id="d60ac-147">Vergewissern Sie sich, dass die APP über **Microsoft Graph** > **User. Read** -Berechtigung verfügt.</span><span class="sxs-lookup"><span data-stu-id="d60ac-147">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="d60ac-148">Wählen Sie **Berechtigung hinzufügen** und dann **meine APIs**aus.</span><span class="sxs-lookup"><span data-stu-id="d60ac-148">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="d60ac-149">Wählen Sie die *Server-API-APP* aus der Spalte **Name** (z. b. \*\* Blazor Server Aad\*\*) aus.</span><span class="sxs-lookup"><span data-stu-id="d60ac-149">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="d60ac-150">Öffnen Sie die **API** -Liste.</span><span class="sxs-lookup"><span data-stu-id="d60ac-150">Open the **API** list.</span></span>
1. <span data-ttu-id="d60ac-151">Aktivieren Sie den Zugriff auf die API (z `API.Access`. b.).</span><span class="sxs-lookup"><span data-stu-id="d60ac-151">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="d60ac-152">Wählen Sie **Berechtigungen hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="d60ac-152">Select **Add permissions**.</span></span>
1. <span data-ttu-id="d60ac-153">Wählen Sie die Schaltfläche **Administrator Inhalt für {TENANT Name} erteilen aus** .</span><span class="sxs-lookup"><span data-stu-id="d60ac-153">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="d60ac-154">Klicken Sie zum Bestätigen auf **Ja**.</span><span class="sxs-lookup"><span data-stu-id="d60ac-154">Select **Yes** to confirm.</span></span>

<span data-ttu-id="d60ac-155">Notieren Sie *Client app* sich `33333333-3333-3333-3333-333333333333`die Anwendungs-ID der Client-app (Client-ID) (z. b.).</span><span class="sxs-lookup"><span data-stu-id="d60ac-155">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="d60ac-156">Erstellen der App</span><span class="sxs-lookup"><span data-stu-id="d60ac-156">Create the app</span></span>

<span data-ttu-id="d60ac-157">Ersetzen Sie die Platzhalter im folgenden Befehl durch die zuvor aufgezeichneten Informationen, und führen Sie den Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="d60ac-157">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="d60ac-158">Um den Ausgabe Speicherort anzugeben, der einen Projektordner erstellt, wenn er nicht vorhanden ist, schließen Sie die Output-Option in den Befehl mit einem `-o BlazorSample`Pfad (z. b.) ein.</span><span class="sxs-lookup"><span data-stu-id="d60ac-158">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="d60ac-159">Der Name des Ordners wird auch Teil des Projekt namens.</span><span class="sxs-lookup"><span data-stu-id="d60ac-159">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="d60ac-160">Übergeben Sie den APP-ID- `app-id-uri` URI an die-Option, aber beachten Sie, dass in der Client-App möglicherweise eine Konfigurationsänderung erforderlich ist, die im Abschnitt [Zugriffs Token-Bereiche](#access-token-scopes) beschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="d60ac-160">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="d60ac-161">Server-App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="d60ac-161">Server app configuration</span></span>

<span data-ttu-id="d60ac-162">*Dieser Abschnitt bezieht sich auf die **Server** -App der Lösung.*</span><span class="sxs-lookup"><span data-stu-id="d60ac-162">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="d60ac-163">Authentifizierungs Paket</span><span class="sxs-lookup"><span data-stu-id="d60ac-163">Authentication package</span></span>

<span data-ttu-id="d60ac-164">Die Unterstützung für das Authentifizieren und autorialisieren von Aufrufen von ASP.net Core Web-APIs `Microsoft.AspNetCore.Authentication.AzureAD.UI`wird von bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="d60ac-164">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="{VERSION}" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="d60ac-165">Unterstützung für Authentifizierungsdienst</span><span class="sxs-lookup"><span data-stu-id="d60ac-165">Authentication service support</span></span>

<span data-ttu-id="d60ac-166">Die `AddAuthentication` -Methode richtet Authentifizierungsdienste innerhalb der APP ein und konfiguriert den JWT-bearerhandler als Standard Authentifizierungsmethode.</span><span class="sxs-lookup"><span data-stu-id="d60ac-166">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="d60ac-167">Die `AddAzureADBearer` -Methode richtet die spezifischen Parameter im JWT-bearerhandler ein, der zum Überprüfen der vom Azure Active Directory ausgegebenen Token erforderlich ist:</span><span class="sxs-lookup"><span data-stu-id="d60ac-167">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="d60ac-168">`UseAuthentication`und `UseAuthorization` stellen Sie Folgendes sicher:</span><span class="sxs-lookup"><span data-stu-id="d60ac-168">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="d60ac-169">Die APP versucht, Token bei eingehenden Anforderungen zu analysieren und zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="d60ac-169">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="d60ac-170">Jede Anforderung, die versucht, auf eine geschützte Ressource ohne korrekte Anmelde Informationen zuzugreifen, schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="d60ac-170">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="d60ac-171">Bedienungs. Identity. Benennen</span><span class="sxs-lookup"><span data-stu-id="d60ac-171">User.Identity.Name</span></span>

<span data-ttu-id="d60ac-172">Standardmäßig füllt die Server-App-API `User.Identity.Name` mit dem Wert aus dem `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` Anspruchstyp (z. b `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`.).</span><span class="sxs-lookup"><span data-stu-id="d60ac-172">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="d60ac-173">Um die APP so zu konfigurieren, dass Sie den `name` Wert aus dem Anspruchstyp empfängt, konfigurieren Sie [tokenvalidationparameters. nameclaimtype](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) von <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d60ac-173">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="d60ac-174">App-Einstellungen</span><span class="sxs-lookup"><span data-stu-id="d60ac-174">App settings</span></span>

<span data-ttu-id="d60ac-175">Die Datei *appSettings. JSON* enthält die Optionen zum Konfigurieren des JWT-bearerhandlers, der zum Überprüfen von Zugriffs Token verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="d60ac-175">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
  }
}
```

<span data-ttu-id="d60ac-176">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d60ac-176">Example:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="d60ac-177">Weatherforecast-Controller</span><span class="sxs-lookup"><span data-stu-id="d60ac-177">WeatherForecast controller</span></span>

<span data-ttu-id="d60ac-178">Der weatherforecast-Controller (*Controllers/weatherforecastcontroller. cs*) macht eine geschützte API verfügbar `[Authorize]` , wobei das-Attribut auf den Controller angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="d60ac-178">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="d60ac-179">Es ist **wichtig** , Folgendes zu verstehen:</span><span class="sxs-lookup"><span data-stu-id="d60ac-179">It's **important** to understand that:</span></span>

* <span data-ttu-id="d60ac-180">Das `[Authorize]` Attribut in diesem API-Controller ist die einzige Möglichkeit, diese API vor nicht autorisiertem Zugriff zu schützen.</span><span class="sxs-lookup"><span data-stu-id="d60ac-180">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="d60ac-181">Das `[Authorize]` in der Blazor Webassembly-App verwendete Attribut dient nur als Hinweis für die APP, dass der Benutzer autorisiert werden muss, damit die APP ordnungsgemäß funktioniert.</span><span class="sxs-lookup"><span data-stu-id="d60ac-181">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="d60ac-182">Client-App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="d60ac-182">Client app configuration</span></span>

<span data-ttu-id="d60ac-183">*Dieser Abschnitt bezieht sich auf die **Client** -App der Lösung.*</span><span class="sxs-lookup"><span data-stu-id="d60ac-183">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="d60ac-184">Authentifizierungs Paket</span><span class="sxs-lookup"><span data-stu-id="d60ac-184">Authentication package</span></span>

<span data-ttu-id="d60ac-185">Wenn eine APP für die Verwendung von Geschäfts-, Schul-`SingleOrg`oder unikonten () erstellt wird, erhält die APP automatisch einen Paket Verweis`Microsoft.Authentication.WebAssembly.Msal`für die [Microsoft-Authentifizierungs Bibliothek](/azure/active-directory/develop/msal-overview) ().</span><span class="sxs-lookup"><span data-stu-id="d60ac-185">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="d60ac-186">Das Paket enthält eine Reihe von primitiven, die der APP helfen, Benutzer zu authentifizieren und Token abzurufen, um geschützte APIs aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="d60ac-186">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="d60ac-187">Wenn Sie eine Authentifizierung zu einer APP hinzufügen, fügen Sie das Paket manuell zur Projektdatei der APP hinzu:</span><span class="sxs-lookup"><span data-stu-id="d60ac-187">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="d60ac-188">Ersetzen `{VERSION}` Sie im vorherigen Paket Verweis durch die Version des `Microsoft.AspNetCore.Blazor.Templates` Pakets, das im <xref:blazor/get-started> Artikel angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="d60ac-188">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="d60ac-189">Das `Microsoft.Authentication.WebAssembly.Msal` Paket fügt das `Microsoft.AspNetCore.Components.WebAssembly.Authentication` Paket transitiv der APP hinzu.</span><span class="sxs-lookup"><span data-stu-id="d60ac-189">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="d60ac-190">Unterstützung für Authentifizierungsdienst</span><span class="sxs-lookup"><span data-stu-id="d60ac-190">Authentication service support</span></span>

<span data-ttu-id="d60ac-191">Es wird `HttpClient` Unterstützung für-Instanzen hinzugefügt, die Zugriffs Token enthalten, wenn Anforderungen an das Server Projekt gestellt werden.</span><span class="sxs-lookup"><span data-stu-id="d60ac-191">Support for `HttpClient` instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="d60ac-192">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="d60ac-192">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="d60ac-193">Die Unterstützung für das Authentifizieren von Benutzern wird im Dienst Container `AddMsalAuthentication` mit der vom `Microsoft.Authentication.WebAssembly.Msal` Paket bereitgestellten Erweiterungsmethode registriert.</span><span class="sxs-lookup"><span data-stu-id="d60ac-193">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="d60ac-194">Diese Methode richtet alle Dienste ein, die für die Interaktion der APP mit dem Identity Anbieter (IP) erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="d60ac-194">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="d60ac-195">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="d60ac-195">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="d60ac-196">Die `AddMsalAuthentication` -Methode akzeptiert einen Rückruf, um die Parameter zu konfigurieren, die zum Authentifizieren einer APP erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="d60ac-196">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="d60ac-197">Die für die Konfiguration der APP erforderlichen Werte können aus der Aad-Konfiguration des Azure-Portals abgerufen werden, wenn Sie die APP registrieren.</span><span class="sxs-lookup"><span data-stu-id="d60ac-197">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="d60ac-198">Die Konfiguration wird von der Datei " *wwwroot/appSettings. JSON* " bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="d60ac-198">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="d60ac-199">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d60ac-199">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="d60ac-200">Zugriffs Token-Bereiche</span><span class="sxs-lookup"><span data-stu-id="d60ac-200">Access token scopes</span></span>

<span data-ttu-id="d60ac-201">Die standardzugriffstokenbereiche stellen die Liste der zugriffstokenbereiche dar, die lauten:</span><span class="sxs-lookup"><span data-stu-id="d60ac-201">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="d60ac-202">Standardmäßig in der Anmelde Anforderung enthalten.</span><span class="sxs-lookup"><span data-stu-id="d60ac-202">Included by default in the sign in request.</span></span>
* <span data-ttu-id="d60ac-203">Wird verwendet, um unmittelbar nach der Authentifizierung ein Zugriffs Token bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="d60ac-203">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="d60ac-204">Alle Bereiche müssen pro Azure Active Directory Regeln zur gleichen App gehören.</span><span class="sxs-lookup"><span data-stu-id="d60ac-204">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="d60ac-205">Für zusätzliche API-Apps können nach Bedarf weitere Bereiche hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="d60ac-205">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="d60ac-206">Wenn die Azure-Portal einen Bereichs-URI bereitstellt und **die APP eine nicht behandelte Ausnahme** auslöst, wenn Sie eine 401-nicht *autorisierte* Antwort von der API empfängt, verwenden Sie einen Bereichs-URI, der das Schema und den Host nicht enthält.</span><span class="sxs-lookup"><span data-stu-id="d60ac-206">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="d60ac-207">Der Azure-Portal kann z. b. eines der folgenden Bereich-URI-Formate bereitstellen:</span><span class="sxs-lookup"><span data-stu-id="d60ac-207">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="d60ac-208">Geben Sie den Bereichs-URI ohne das Schema und den Host an:</span><span class="sxs-lookup"><span data-stu-id="d60ac-208">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="d60ac-209">Weitere Informationen finden Sie in den folgenden Abschnitten des Artikels *weitere Szenarien* :</span><span class="sxs-lookup"><span data-stu-id="d60ac-209">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="d60ac-210">Anfordern zusätzlicher Zugriffstoken</span><span class="sxs-lookup"><span data-stu-id="d60ac-210">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="d60ac-211">Anfügen von Token an ausgehende Anforderungen</span><span class="sxs-lookup"><span data-stu-id="d60ac-211">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="d60ac-212">Import Datei</span><span class="sxs-lookup"><span data-stu-id="d60ac-212">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="d60ac-213">Indexseite</span><span class="sxs-lookup"><span data-stu-id="d60ac-213">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="d60ac-214">App-Komponente</span><span class="sxs-lookup"><span data-stu-id="d60ac-214">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="d60ac-215">Redirecttologin-Komponente</span><span class="sxs-lookup"><span data-stu-id="d60ac-215">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="d60ac-216">Logindisplay-Komponente</span><span class="sxs-lookup"><span data-stu-id="d60ac-216">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="d60ac-217">Authentifizierungs Komponente</span><span class="sxs-lookup"><span data-stu-id="d60ac-217">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="d60ac-218">Fetchdata-Komponente</span><span class="sxs-lookup"><span data-stu-id="d60ac-218">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="d60ac-219">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="d60ac-219">Run the app</span></span>

<span data-ttu-id="d60ac-220">Führen Sie die APP aus dem Server Projekt aus.</span><span class="sxs-lookup"><span data-stu-id="d60ac-220">Run the app from the Server project.</span></span> <span data-ttu-id="d60ac-221">Wenn Sie Visual Studio verwenden, wählen Sie das Server Projekt in **Projektmappen-Explorer** aus, und klicken Sie auf der Symbolleiste auf die Schaltfläche **Ausführen** , oder starten Sie die APP im Menü **Debuggen**</span><span class="sxs-lookup"><span data-stu-id="d60ac-221">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="d60ac-222">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="d60ac-222">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="d60ac-223">Dokumentation zur Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="d60ac-223">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)

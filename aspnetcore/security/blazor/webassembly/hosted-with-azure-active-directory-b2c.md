---
title: Sichern einer ASP.net Core Blazor Webassembly-gehosteten App mit Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 45ef1e6599777a38da7db753a8868028f3134f4b
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110979"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="a35c5-102">Sichern einer ASP.net Core Blazor Webassembly-gehosteten App mit Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="a35c5-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="a35c5-103">Von [Javier calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a35c5-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="a35c5-104">Die Anleitung in diesem Artikel gilt für ASP.net Core 3,2 Preview 4.</span><span class="sxs-lookup"><span data-stu-id="a35c5-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="a35c5-105">Dieses Thema wird aktualisiert, um Preview 5 am Freitag, dem 24. April, zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="a35c5-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="a35c5-106">In diesem Artikel wird beschrieben, wie Blazor Sie eine eigenständige Webassembly-app erstellen, die [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) für die Authentifizierung verwendet.</span><span class="sxs-lookup"><span data-stu-id="a35c5-106">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="a35c5-107">Registrieren von apps in Aad B2C und Erstellen einer Lösung</span><span class="sxs-lookup"><span data-stu-id="a35c5-107">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="a35c5-108">Erstellen eines Mandanten</span><span class="sxs-lookup"><span data-stu-id="a35c5-108">Create a tenant</span></span>

<span data-ttu-id="a35c5-109">Befolgen Sie die Anweisungen im [Tutorial: Erstellen eines Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) Mandanten, um einen Aad B2C-Mandanten zu erstellen, und notieren Sie die folgenden Informationen:</span><span class="sxs-lookup"><span data-stu-id="a35c5-109">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant and record the following information:</span></span>

* <span data-ttu-id="a35c5-110">Aad B2C Instanz (z. b `https://contoso.b2clogin.com/`., einschließlich des nachgestellten Schrägstrichs)</span><span class="sxs-lookup"><span data-stu-id="a35c5-110">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="a35c5-111">Aad B2C Mandanten Domäne (z. b `contoso.onmicrosoft.com`.)</span><span class="sxs-lookup"><span data-stu-id="a35c5-111">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="a35c5-112">Registrieren einer Server-API-APP</span><span class="sxs-lookup"><span data-stu-id="a35c5-112">Register a server API app</span></span>

<span data-ttu-id="a35c5-113">Befolgen Sie die Anweisungen im [Tutorial: Registrieren einer Anwendung in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) , um eine Aad-App für die *Server-API-APP* im **Azure Active Directory** > **App-Registrierungen** Bereich der Azure-Portal zu registrieren:</span><span class="sxs-lookup"><span data-stu-id="a35c5-113">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="a35c5-114">Wählen Sie **Neue Registrierung** aus.</span><span class="sxs-lookup"><span data-stu-id="a35c5-114">Select **New registration**.</span></span>
1. <span data-ttu-id="a35c5-115">Geben Sie einen **Namen** für die APP an (z. b. \*\* Blazor Server Aad B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="a35c5-115">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="a35c5-116">Wählen Sie für **unterstützte Konto Typen** **in einem beliebigen Organisations Verzeichnis oder einem beliebigen Identitäts Anbieter Konten aus. Zum Authentifizieren von Benutzern mit Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="a35c5-116">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="a35c5-117">(mehr Instanzen fähig).</span><span class="sxs-lookup"><span data-stu-id="a35c5-117">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="a35c5-118">Für die *Server-API-APP* ist in diesem Szenario kein **Umleitungs-URI** erforderlich. lassen Sie die Dropdown-Datei also auf **Web** festgelegt und geben Sie keinen Umleitungs-URI</span><span class="sxs-lookup"><span data-stu-id="a35c5-118">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="a35c5-119">Vergewissern Sie sich **, dass die** > Berechtigungen**admin keine Zustimmung für OpenID und offline_access Berechtigungen gewähren** aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="a35c5-119">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="a35c5-120">Wählen Sie **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="a35c5-120">Select **Register**.</span></span>

<span data-ttu-id="a35c5-121">In machen Sie **eine API**verfügbar:</span><span class="sxs-lookup"><span data-stu-id="a35c5-121">In **Expose an API**:</span></span>

1. <span data-ttu-id="a35c5-122">Wählen Sie **Bereich hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="a35c5-122">Select **Add a scope**.</span></span>
1. <span data-ttu-id="a35c5-123">Wählen Sie **Speichern und fortfahren** aus.</span><span class="sxs-lookup"><span data-stu-id="a35c5-123">Select **Save and continue**.</span></span>
1. <span data-ttu-id="a35c5-124">Geben Sie einen **Bereichs Namen** an (z `API.Access`. b.).</span><span class="sxs-lookup"><span data-stu-id="a35c5-124">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="a35c5-125">Geben Sie einen **anzeigen Amen für die Administrator Zustimmung** an `Access API`(z. b.).</span><span class="sxs-lookup"><span data-stu-id="a35c5-125">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="a35c5-126">Geben Sie eine Beschreibung für die **Administrator Zustimmung** an `Allows the app to access server app API endpoints.`(z. b.).</span><span class="sxs-lookup"><span data-stu-id="a35c5-126">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="a35c5-127">Vergewissern Sie sich, dass der **Status** auf **aktiviert**festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="a35c5-127">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="a35c5-128">Wählen Sie **Bereich hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="a35c5-128">Select **Add scope**.</span></span>

<span data-ttu-id="a35c5-129">Notieren Sie sich die folgenden Informationen:</span><span class="sxs-lookup"><span data-stu-id="a35c5-129">Record the following information:</span></span>

* <span data-ttu-id="a35c5-130">*Server-API-APP* Anwendungs-ID (Client-ID) (z `11111111-1111-1111-1111-111111111111`. b.)</span><span class="sxs-lookup"><span data-stu-id="a35c5-130">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="a35c5-131">APP-ID-URI (z `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111`. b., oder der von Ihnen angegebene benutzerdefinierte Wert)</span><span class="sxs-lookup"><span data-stu-id="a35c5-131">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="a35c5-132">Verzeichnis-ID (Mandanten-ID) (z `222222222-2222-2222-2222-222222222222`. b.)</span><span class="sxs-lookup"><span data-stu-id="a35c5-132">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="a35c5-133">*Server-API-APP* APP-ID-URI (z `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`. b. wird der Azure-Portal den Wert möglicherweise als Standardwert für die Client-ID)</span><span class="sxs-lookup"><span data-stu-id="a35c5-133">*Server API app* App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, the Azure portal might default the value to the Client ID)</span></span>
* <span data-ttu-id="a35c5-134">Standardbereich (z. b `API.Access`.)</span><span class="sxs-lookup"><span data-stu-id="a35c5-134">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="a35c5-135">Registrieren einer Client-App</span><span class="sxs-lookup"><span data-stu-id="a35c5-135">Register a client app</span></span>

<span data-ttu-id="a35c5-136">Befolgen Sie die Anweisungen [im Tutorial: Registrieren einer Anwendung in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) erneut, um eine Aad-App für die *Client-App* in der **Azure Active Directory** > **App-Registrierungen** Bereich der Azure-Portal zu registrieren:</span><span class="sxs-lookup"><span data-stu-id="a35c5-136">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="a35c5-137">Wählen Sie **Neue Registrierung** aus.</span><span class="sxs-lookup"><span data-stu-id="a35c5-137">Select **New registration**.</span></span>
1. <span data-ttu-id="a35c5-138">Geben Sie einen **Namen** für die APP an (z. b. \*\* Blazor Client Aad B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="a35c5-138">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="a35c5-139">Wählen Sie für **unterstützte Konto Typen** **in einem beliebigen Organisations Verzeichnis oder einem beliebigen Identitäts Anbieter Konten aus. Zum Authentifizieren von Benutzern mit Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="a35c5-139">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="a35c5-140">(mehr Instanzen fähig).</span><span class="sxs-lookup"><span data-stu-id="a35c5-140">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="a35c5-141">Belassen Sie den **Umleitungs-URI** -Dropdown auf **Web**, und geben Sie `https://localhost:5001/authentication/login-callback`einen Umleitungs-URI von an.</span><span class="sxs-lookup"><span data-stu-id="a35c5-141">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="a35c5-142">Vergewissern Sie sich **, dass die** > Berechtigungen**admin keine Zustimmung für OpenID und offline_access Berechtigungen gewähren** aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="a35c5-142">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="a35c5-143">Wählen Sie **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="a35c5-143">Select **Register**.</span></span>

<span data-ttu-id="a35c5-144">Im**Web**der **Authentifizierungs** > **Platt Form Konfigurationen** > :</span><span class="sxs-lookup"><span data-stu-id="a35c5-144">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="a35c5-145">Bestätigen Sie, dass der `https://localhost:5001/authentication/login-callback` **Umleitungs-URI** von vorhanden ist</span><span class="sxs-lookup"><span data-stu-id="a35c5-145">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="a35c5-146">Aktivieren Sie für **implizite Gewährung**die Kontrollkästchen für **Zugriffs Token** und **ID-Token**.</span><span class="sxs-lookup"><span data-stu-id="a35c5-146">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="a35c5-147">Die verbleibenden Standardwerte für die APP sind für diese Art von akzeptabel.</span><span class="sxs-lookup"><span data-stu-id="a35c5-147">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="a35c5-148">Wählen Sie die Schaltfläche **Speichern** aus.</span><span class="sxs-lookup"><span data-stu-id="a35c5-148">Select the **Save** button.</span></span>

<span data-ttu-id="a35c5-149">In **API-Berechtigungen**:</span><span class="sxs-lookup"><span data-stu-id="a35c5-149">In **API permissions**:</span></span>

1. <span data-ttu-id="a35c5-150">Vergewissern Sie sich, dass die APP über **Microsoft Graph** > **User. Read** -Berechtigung verfügt.</span><span class="sxs-lookup"><span data-stu-id="a35c5-150">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="a35c5-151">Wählen Sie **Berechtigung hinzufügen** und dann **meine APIs**aus.</span><span class="sxs-lookup"><span data-stu-id="a35c5-151">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="a35c5-152">Wählen Sie die *Server-API-APP* aus der Spalte **Name** (z. b. \*\* Blazor Server Aad B2C\*\*) aus.</span><span class="sxs-lookup"><span data-stu-id="a35c5-152">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="a35c5-153">Öffnen Sie die **API** -Liste.</span><span class="sxs-lookup"><span data-stu-id="a35c5-153">Open the **API** list.</span></span>
1. <span data-ttu-id="a35c5-154">Aktivieren Sie den Zugriff auf die API (z `API.Access`. b.).</span><span class="sxs-lookup"><span data-stu-id="a35c5-154">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="a35c5-155">Wählen Sie **Berechtigungen hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="a35c5-155">Select **Add permissions**.</span></span>
1. <span data-ttu-id="a35c5-156">Wählen Sie die Schaltfläche **Administrator Inhalt für {TENANT Name} erteilen aus** .</span><span class="sxs-lookup"><span data-stu-id="a35c5-156">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="a35c5-157">Klicken Sie auf **Ja**, um zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="a35c5-157">Select **Yes** to confirm.</span></span>

<span data-ttu-id="a35c5-158">In der **Start** > **Azure AD B2C** > **benutzerflows**:</span><span class="sxs-lookup"><span data-stu-id="a35c5-158">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="a35c5-159">Erstellen eines Benutzerflows für Registrierung und Anmeldung</span><span class="sxs-lookup"><span data-stu-id="a35c5-159">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="a35c5-160">Wählen Sie mindestens das Benutzer Attribut **Anwendungs Anspruchs** > **Anzeige Name** aus, um das `context.User.Identity.Name` in der `LoginDisplay` Komponente (*Shared/logindisplay. Razor*) aufzufüllen.</span><span class="sxs-lookup"><span data-stu-id="a35c5-160">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="a35c5-161">Notieren Sie sich die folgenden Informationen:</span><span class="sxs-lookup"><span data-stu-id="a35c5-161">Record the following information:</span></span>

* <span data-ttu-id="a35c5-162">Notieren Sie *Client app* sich `33333333-3333-3333-3333-333333333333`die Anwendungs-ID der Client-app (Client-ID) (z. b.).</span><span class="sxs-lookup"><span data-stu-id="a35c5-162">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>
* <span data-ttu-id="a35c5-163">Zeichnen Sie den für die APP erstellten Registrierungs-und Anmeldedaten Flussnamen auf (z. b. `B2C_1_signupsignin`).</span><span class="sxs-lookup"><span data-stu-id="a35c5-163">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="a35c5-164">Erstellen der App</span><span class="sxs-lookup"><span data-stu-id="a35c5-164">Create the app</span></span>

<span data-ttu-id="a35c5-165">Ersetzen Sie die Platzhalter im folgenden Befehl durch die zuvor aufgezeichneten Informationen, und führen Sie den Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="a35c5-165">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="a35c5-166">Um den Ausgabe Speicherort anzugeben, der einen Projektordner erstellt, wenn er nicht vorhanden ist, schließen Sie die Output-Option in den Befehl mit einem `-o BlazorSample`Pfad (z. b.) ein.</span><span class="sxs-lookup"><span data-stu-id="a35c5-166">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="a35c5-167">Der Name des Ordners wird auch Teil des Projekt namens.</span><span class="sxs-lookup"><span data-stu-id="a35c5-167">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="a35c5-168">Übergeben Sie den APP-ID- `app-id-uri` URI an die-Option, aber beachten Sie, dass in der Client-App möglicherweise eine Konfigurationsänderung erforderlich ist, die im Abschnitt [Zugriffs Token-Bereiche](#access-token-scopes) beschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="a35c5-168">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="a35c5-169">Server-App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="a35c5-169">Server app configuration</span></span>

<span data-ttu-id="a35c5-170">*Dieser Abschnitt bezieht sich auf die **Server** -App der Lösung.*</span><span class="sxs-lookup"><span data-stu-id="a35c5-170">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="a35c5-171">Authentifizierungs Paket</span><span class="sxs-lookup"><span data-stu-id="a35c5-171">Authentication package</span></span>

<span data-ttu-id="a35c5-172">Die Unterstützung für das Authentifizieren und autorialisieren von Aufrufen von ASP.net Core Web-APIs `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`wird von bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="a35c5-172">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="a35c5-173">Unterstützung für Authentifizierungsdienst</span><span class="sxs-lookup"><span data-stu-id="a35c5-173">Authentication service support</span></span>

<span data-ttu-id="a35c5-174">Die `AddAuthentication` -Methode richtet Authentifizierungsdienste innerhalb der APP ein und konfiguriert den JWT-bearerhandler als Standard Authentifizierungsmethode.</span><span class="sxs-lookup"><span data-stu-id="a35c5-174">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="a35c5-175">Die `AddAzureADB2CBearer` -Methode richtet die spezifischen Parameter im JWT-bearerhandler ein, der zum Überprüfen der vom Azure Active Directory B2C ausgegebenen Token erforderlich ist:</span><span class="sxs-lookup"><span data-stu-id="a35c5-175">The `AddAzureADB2CBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="a35c5-176">`UseAuthentication`und `UseAuthorization` stellen Sie Folgendes sicher:</span><span class="sxs-lookup"><span data-stu-id="a35c5-176">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="a35c5-177">Die APP versucht, Token bei eingehenden Anforderungen zu analysieren und zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="a35c5-177">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="a35c5-178">Jede Anforderung, die versucht, auf eine geschützte Ressource ohne korrekte Anmelde Informationen zuzugreifen, schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="a35c5-178">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="a35c5-179">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="a35c5-179">User.Identity.Name</span></span>

<span data-ttu-id="a35c5-180">Standardmäßig wird `User.Identity.Name` nicht aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="a35c5-180">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="a35c5-181">Um die APP so zu konfigurieren, dass Sie den `name` Wert aus dem Anspruchstyp empfängt, konfigurieren Sie [tokenvalidationparameters. nameclaimtype](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) von <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a35c5-181">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="a35c5-182">App-Einstellungen</span><span class="sxs-lookup"><span data-stu-id="a35c5-182">App settings</span></span>

<span data-ttu-id="a35c5-183">Die Datei *appSettings. JSON* enthält die Optionen zum Konfigurieren des JWT-bearerhandlers, der zum Überprüfen von Zugriffs Token verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="a35c5-183">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="a35c5-184">Weatherforecast-Controller</span><span class="sxs-lookup"><span data-stu-id="a35c5-184">WeatherForecast controller</span></span>

<span data-ttu-id="a35c5-185">Der weatherforecast-Controller (*Controllers/weatherforecastcontroller. cs*) macht eine geschützte API verfügbar `[Authorize]` , wobei das-Attribut auf den Controller angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="a35c5-185">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="a35c5-186">Es ist **wichtig** , Folgendes zu verstehen:</span><span class="sxs-lookup"><span data-stu-id="a35c5-186">It's **important** to understand that:</span></span>

* <span data-ttu-id="a35c5-187">Das `[Authorize]` Attribut in diesem API-Controller ist die einzige Möglichkeit, diese API vor nicht autorisiertem Zugriff zu schützen.</span><span class="sxs-lookup"><span data-stu-id="a35c5-187">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="a35c5-188">Das `[Authorize]` in der Blazor Webassembly-App verwendete Attribut dient nur als Hinweis für die APP, dass der Benutzer autorisiert werden muss, damit die APP ordnungsgemäß funktioniert.</span><span class="sxs-lookup"><span data-stu-id="a35c5-188">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="a35c5-189">Client-App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="a35c5-189">Client app configuration</span></span>

<span data-ttu-id="a35c5-190">*Dieser Abschnitt bezieht sich auf die **Client** -App der Lösung.*</span><span class="sxs-lookup"><span data-stu-id="a35c5-190">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="a35c5-191">Authentifizierungs Paket</span><span class="sxs-lookup"><span data-stu-id="a35c5-191">Authentication package</span></span>

<span data-ttu-id="a35c5-192">Wenn eine APP für die Verwendung eines einzelnen B2C-Kontos (`IndividualB2C`) erstellt wird, erhält die APP automatisch einen Paket Verweis für die Microsoft-`Microsoft.Authentication.WebAssembly.Msal` [Authentifizierungs Bibliothek](/azure/active-directory/develop/msal-overview) ().</span><span class="sxs-lookup"><span data-stu-id="a35c5-192">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="a35c5-193">Das Paket enthält eine Reihe von primitiven, die der APP helfen, Benutzer zu authentifizieren und Token abzurufen, um geschützte APIs aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="a35c5-193">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="a35c5-194">Wenn Sie eine Authentifizierung zu einer APP hinzufügen, fügen Sie das Paket manuell zur Projektdatei der APP hinzu:</span><span class="sxs-lookup"><span data-stu-id="a35c5-194">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="a35c5-195">Ersetzen `{VERSION}` Sie im vorherigen Paket Verweis durch die Version des `Microsoft.AspNetCore.Blazor.Templates` Pakets, das im <xref:blazor/get-started> Artikel angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="a35c5-195">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="a35c5-196">Das `Microsoft.Authentication.WebAssembly.Msal` Paket fügt das `Microsoft.AspNetCore.Components.WebAssembly.Authentication` Paket transitiv der APP hinzu.</span><span class="sxs-lookup"><span data-stu-id="a35c5-196">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="a35c5-197">Unterstützung für Authentifizierungsdienst</span><span class="sxs-lookup"><span data-stu-id="a35c5-197">Authentication service support</span></span>

<span data-ttu-id="a35c5-198">Die Unterstützung für das Authentifizieren von Benutzern wird im Dienst Container `AddMsalAuthentication` mit der vom `Microsoft.Authentication.WebAssembly.Msal` Paket bereitgestellten Erweiterungsmethode registriert.</span><span class="sxs-lookup"><span data-stu-id="a35c5-198">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="a35c5-199">Diese Methode richtet alle Dienste ein, die erforderlich sind, damit die APP mit dem Identitäts Anbieter (IP) interagieren muss.</span><span class="sxs-lookup"><span data-stu-id="a35c5-199">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="a35c5-200">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="a35c5-200">*Program.cs*:</span></span>

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

<span data-ttu-id="a35c5-201">Die `AddMsalAuthentication` -Methode akzeptiert einen Rückruf, um die Parameter zu konfigurieren, die zum Authentifizieren einer APP erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="a35c5-201">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="a35c5-202">Die für die Konfiguration der APP erforderlichen Werte können aus der Aad-Konfiguration des Azure-Portals abgerufen werden, wenn Sie die APP registrieren.</span><span class="sxs-lookup"><span data-stu-id="a35c5-202">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

### <a name="access-token-scopes"></a><span data-ttu-id="a35c5-203">Zugriffs Token-Bereiche</span><span class="sxs-lookup"><span data-stu-id="a35c5-203">Access token scopes</span></span>

<span data-ttu-id="a35c5-204">Die standardzugriffstokenbereiche stellen die Liste der zugriffstokenbereiche dar, die lauten:</span><span class="sxs-lookup"><span data-stu-id="a35c5-204">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="a35c5-205">Standardmäßig in der Anmelde Anforderung enthalten.</span><span class="sxs-lookup"><span data-stu-id="a35c5-205">Included by default in the sign in request.</span></span>
* <span data-ttu-id="a35c5-206">Wird verwendet, um unmittelbar nach der Authentifizierung ein Zugriffs Token bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="a35c5-206">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="a35c5-207">Alle Bereiche müssen pro Azure Active Directory Regeln zur gleichen App gehören.</span><span class="sxs-lookup"><span data-stu-id="a35c5-207">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="a35c5-208">Für zusätzliche API-Apps können nach Bedarf weitere Bereiche hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="a35c5-208">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="a35c5-209">Wenn die Azure-Portal einen Bereichs-URI bereitstellt und **die APP eine nicht behandelte Ausnahme** auslöst, wenn Sie eine 401-nicht *autorisierte* Antwort von der API empfängt, verwenden Sie einen Bereichs-URI, der das Schema und den Host nicht enthält.</span><span class="sxs-lookup"><span data-stu-id="a35c5-209">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="a35c5-210">Der Azure-Portal kann z. b. eines der folgenden Bereich-URI-Formate bereitstellen:</span><span class="sxs-lookup"><span data-stu-id="a35c5-210">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="a35c5-211">Geben Sie den Bereichs-URI ohne das Schema und den Host an:</span><span class="sxs-lookup"><span data-stu-id="a35c5-211">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="a35c5-212">Weitere Informationen finden Sie unter <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="a35c5-212">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

### <a name="imports-file"></a><span data-ttu-id="a35c5-213">Import Datei</span><span class="sxs-lookup"><span data-stu-id="a35c5-213">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="a35c5-214">Indexseite</span><span class="sxs-lookup"><span data-stu-id="a35c5-214">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="a35c5-215">App-Komponente</span><span class="sxs-lookup"><span data-stu-id="a35c5-215">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="a35c5-216">Redirecttologin-Komponente</span><span class="sxs-lookup"><span data-stu-id="a35c5-216">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="a35c5-217">Logindisplay-Komponente</span><span class="sxs-lookup"><span data-stu-id="a35c5-217">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="a35c5-218">Authentifizierungs Komponente</span><span class="sxs-lookup"><span data-stu-id="a35c5-218">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="a35c5-219">Fetchdata-Komponente</span><span class="sxs-lookup"><span data-stu-id="a35c5-219">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="a35c5-220">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="a35c5-220">Run the app</span></span>

<span data-ttu-id="a35c5-221">Führen Sie die APP aus dem Server Projekt aus.</span><span class="sxs-lookup"><span data-stu-id="a35c5-221">Run the app from the Server project.</span></span> <span data-ttu-id="a35c5-222">Wenn Sie Visual Studio verwenden, wählen Sie das Server Projekt in **Projektmappen-Explorer** aus, und klicken Sie auf der Symbolleiste auf die Schaltfläche **Ausführen** , oder starten Sie die APP im Menü **Debuggen**</span><span class="sxs-lookup"><span data-stu-id="a35c5-222">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="a35c5-223">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="a35c5-223">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="a35c5-224">Tutorial zum Erstellen eines Azure Active Directory B2C-Mandanten</span><span class="sxs-lookup"><span data-stu-id="a35c5-224">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="a35c5-225">Dokumentation zur Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="a35c5-225">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)

---
title: Sichern einer ASP.net Core Blazor Webassembly-gehosteten App mit Azure Active Directory B2C
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
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 9a63d6ca0ab6b71875212d54035dfb5cf94a8cad
ms.sourcegitcommit: d243fadeda20ad4f142ea60301ae5f5e0d41ed60
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/12/2020
ms.locfileid: "84724301"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="cecdd-102">Sichern einer ASP.net Core Blazor Webassembly-gehosteten App mit Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="cecdd-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="cecdd-103">Von [Javier calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="cecdd-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="cecdd-104">In diesem Artikel wird beschrieben, wie Sie eine Blazor eigenständige Webassembly-app erstellen, die [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) für die Authentifizierung verwendet.</span><span class="sxs-lookup"><span data-stu-id="cecdd-104">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="cecdd-105">Registrieren von apps in Aad B2C und Erstellen einer Lösung</span><span class="sxs-lookup"><span data-stu-id="cecdd-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="cecdd-106">Erstellen eines Mandanten</span><span class="sxs-lookup"><span data-stu-id="cecdd-106">Create a tenant</span></span>

<span data-ttu-id="cecdd-107">Befolgen Sie die Anweisungen im [Tutorial: Erstellen eines Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) Mandanten, um einen Aad B2C-Mandanten zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="cecdd-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant.</span></span>

<span data-ttu-id="cecdd-108">Notieren Sie sich die folgenden Informationen:</span><span class="sxs-lookup"><span data-stu-id="cecdd-108">Record the following information:</span></span>

* <span data-ttu-id="cecdd-109">Aad B2C Instanz (z. b. `https://contoso.b2clogin.com/` , einschließlich des nachgestellten Schrägstrichs)</span><span class="sxs-lookup"><span data-stu-id="cecdd-109">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="cecdd-110">Aad B2C Mandanten Domäne (z. b. `contoso.onmicrosoft.com` )</span><span class="sxs-lookup"><span data-stu-id="cecdd-110">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="cecdd-111">Registrieren einer Server-API-APP</span><span class="sxs-lookup"><span data-stu-id="cecdd-111">Register a server API app</span></span>

<span data-ttu-id="cecdd-112">Befolgen Sie die Anweisungen im [Tutorial: Registrieren einer Anwendung in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) zum Registrieren einer Aad-App für die *Server-API-APP* und führen Sie dann die folgenden Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="cecdd-112">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="cecdd-113">Wählen Sie in **Azure Active Directory**  >  **App-Registrierungen**die Option **neue Registrierung**aus.</span><span class="sxs-lookup"><span data-stu-id="cecdd-113">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="cecdd-114">Geben Sie einen **Namen** für die APP an (z. b. \*\* Blazor Server Aad B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="cecdd-114">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="cecdd-115">Wählen Sie für **unterstützte Konto Typen**die Option für mehrere Mandanten aus: **Konten in einem beliebigen Organisations Verzeichnis oder einem beliebigen Identitäts Anbieter. Zum Authentifizieren von Benutzern mit Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="cecdd-115">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="cecdd-116">Für die *Server-API-APP* ist in diesem Szenario kein **Umleitungs-URI** erforderlich. lassen Sie die Dropdown-Datei also auf **Web** festgelegt und geben Sie keinen Umleitungs-URI</span><span class="sxs-lookup"><span data-stu-id="cecdd-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="cecdd-117">Vergewissern Sie **sich, dass Berechtigungen**  >  **für OpenID und offline_access Berechtigungen Administrator Zustimmung erteilen** aktiviert sind.</span><span class="sxs-lookup"><span data-stu-id="cecdd-117">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="cecdd-118">Wählen Sie **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="cecdd-118">Select **Register**.</span></span>

<span data-ttu-id="cecdd-119">Notieren Sie sich die folgenden Informationen:</span><span class="sxs-lookup"><span data-stu-id="cecdd-119">Record the following information:</span></span>

* <span data-ttu-id="cecdd-120">*Server-API-APP* Anwendungs-ID (Client-ID) (z. b. `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="cecdd-120">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="cecdd-121">Verzeichnis-ID (Mandanten-ID) (z. b. `222222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="cecdd-121">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="cecdd-122">Aad-Mandanten Domäne (z. b. `contoso.onmicrosoft.com` ): die Domäne ist als **Verleger Domäne** auf dem Blatt **Branding** der Azure-Portal für die registrierte app verfügbar.</span><span class="sxs-lookup"><span data-stu-id="cecdd-122">AAD Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="cecdd-123">In machen Sie **eine API**verfügbar:</span><span class="sxs-lookup"><span data-stu-id="cecdd-123">In **Expose an API**:</span></span>

1. <span data-ttu-id="cecdd-124">Wählen Sie **Bereich hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="cecdd-124">Select **Add a scope**.</span></span>
1. <span data-ttu-id="cecdd-125">Wählen Sie **Speichern und fortfahren** aus.</span><span class="sxs-lookup"><span data-stu-id="cecdd-125">Select **Save and continue**.</span></span>
1. <span data-ttu-id="cecdd-126">Geben Sie einen **Bereichs Namen** an (z `API.Access` . b.).</span><span class="sxs-lookup"><span data-stu-id="cecdd-126">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="cecdd-127">Geben Sie einen **anzeigen Amen für die Administrator Zustimmung** an (z. b `Access API` .).</span><span class="sxs-lookup"><span data-stu-id="cecdd-127">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="cecdd-128">Geben Sie eine Beschreibung für die **Administrator Zustimmung** an (z `Allows the app to access server app API endpoints.` . b.).</span><span class="sxs-lookup"><span data-stu-id="cecdd-128">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="cecdd-129">Vergewissern Sie sich, dass der **Status** auf **aktiviert**festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="cecdd-129">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="cecdd-130">Wählen Sie **Bereich hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="cecdd-130">Select **Add scope**.</span></span>

<span data-ttu-id="cecdd-131">Notieren Sie sich die folgenden Informationen:</span><span class="sxs-lookup"><span data-stu-id="cecdd-131">Record the following information:</span></span>

* <span data-ttu-id="cecdd-132">APP-ID-URI (z. b., `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111` oder der von Ihnen angegebene benutzerdefinierte Wert)</span><span class="sxs-lookup"><span data-stu-id="cecdd-132">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="cecdd-133">Standardbereich (z. b. `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="cecdd-133">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="cecdd-134">Registrieren einer Client-App</span><span class="sxs-lookup"><span data-stu-id="cecdd-134">Register a client app</span></span>

<span data-ttu-id="cecdd-135">Befolgen Sie die Anweisungen im [Tutorial: Registrieren einer Anwendung in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) erneut, um eine Aad-App für die *Client-App* zu registrieren, und gehen Sie dann wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="cecdd-135">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* and then do the following:</span></span>

1. <span data-ttu-id="cecdd-136">Wählen Sie in **Azure Active Directory**  >  **App-Registrierungen**die Option **neue Registrierung**aus.</span><span class="sxs-lookup"><span data-stu-id="cecdd-136">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="cecdd-137">Geben Sie einen **Namen** für die APP an (z. b. \*\* Blazor Client Aad B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="cecdd-137">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="cecdd-138">Wählen Sie für **unterstützte Konto Typen**die Option für mehrere Mandanten aus: **Konten in einem beliebigen Organisations Verzeichnis oder einem beliebigen Identitäts Anbieter. Zum Authentifizieren von Benutzern mit Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="cecdd-138">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="cecdd-139">Lassen Sie die Dropdown-Einstellung für **Umleitungs-URI** auf **Web** fest, und geben Sie folgenden Umleitungs-URI `https://localhost:{PORT}/authentication/login-callback`</span><span class="sxs-lookup"><span data-stu-id="cecdd-139">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="cecdd-140">Der Standardport für eine APP, die auf Kestrel ausgeführt wird, ist 5001.</span><span class="sxs-lookup"><span data-stu-id="cecdd-140">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="cecdd-141">Wenn die APP auf einem anderen Kestrel-Port ausgeführt wird, verwenden Sie den Port der app.</span><span class="sxs-lookup"><span data-stu-id="cecdd-141">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="cecdd-142">Für IIS Express befindet sich der zufällig generierte Port für die app in den Eigenschaften der Server-App im **Debug** -Panel.</span><span class="sxs-lookup"><span data-stu-id="cecdd-142">For IIS Express, the randomly generated port for the app can be found in the Server app's properties in the **Debug** panel.</span></span> <span data-ttu-id="cecdd-143">Da die APP zu diesem Zeitpunkt noch nicht vorhanden ist und der IIS Express Port nicht bekannt ist, kehren Sie zu diesem Schritt zurück, nachdem die App erstellt wurde, und aktualisieren Sie den Umleitungs-URI.</span><span class="sxs-lookup"><span data-stu-id="cecdd-143">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="cecdd-144">Eine Anmerkung wird im Abschnitt [Erstellen der APP](#create-the-app) angezeigt, um IIS Express Benutzer daran zu erinnern, den Umleitungs-URI zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="cecdd-144">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="cecdd-145">Vergewissern Sie **sich, dass Berechtigungen**  >  **für OpenID und offline_access Berechtigungen Administrator Zustimmung erteilen** aktiviert sind.</span><span class="sxs-lookup"><span data-stu-id="cecdd-145">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="cecdd-146">Wählen Sie **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="cecdd-146">Select **Register**.</span></span>

<span data-ttu-id="cecdd-147">Notieren Sie sich die Anwendungs-ID (Client-ID) (z `11111111-1111-1111-1111-111111111111` . b.).</span><span class="sxs-lookup"><span data-stu-id="cecdd-147">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="cecdd-148">Im Web der **Authentifizierungs**  >  **Platt Form Konfigurationen**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="cecdd-148">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="cecdd-149">Bestätigen Sie, dass der **Umleitungs-URI** von `https://localhost:{PORT}/authentication/login-callback` vorhanden ist</span><span class="sxs-lookup"><span data-stu-id="cecdd-149">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="cecdd-150">Aktivieren Sie für **implizite Gewährung**die Kontrollkästchen für **Zugriffs Token** und **ID-Token**.</span><span class="sxs-lookup"><span data-stu-id="cecdd-150">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="cecdd-151">Die verbleibenden Standardwerte für die APP sind für diese Art von akzeptabel.</span><span class="sxs-lookup"><span data-stu-id="cecdd-151">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="cecdd-152">Wählen Sie die Schaltfläche **Speichern** aus.</span><span class="sxs-lookup"><span data-stu-id="cecdd-152">Select the **Save** button.</span></span>

<span data-ttu-id="cecdd-153">In **API-Berechtigungen**:</span><span class="sxs-lookup"><span data-stu-id="cecdd-153">In **API permissions**:</span></span>

1. <span data-ttu-id="cecdd-154">Wählen Sie **Berechtigung hinzufügen** und dann **meine APIs**aus.</span><span class="sxs-lookup"><span data-stu-id="cecdd-154">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="cecdd-155">Wählen Sie die *Server-API-APP* aus der Spalte **Name** (z. b. \*\* Blazor Server Aad B2C\*\*) aus.</span><span class="sxs-lookup"><span data-stu-id="cecdd-155">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="cecdd-156">Öffnen Sie die **API** -Liste.</span><span class="sxs-lookup"><span data-stu-id="cecdd-156">Open the **API** list.</span></span>
1. <span data-ttu-id="cecdd-157">Aktivieren Sie den Zugriff auf die API (z `API.Access` . b.).</span><span class="sxs-lookup"><span data-stu-id="cecdd-157">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="cecdd-158">Wählen Sie **Berechtigungen hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="cecdd-158">Select **Add permissions**.</span></span>
1. <span data-ttu-id="cecdd-159">Wählen Sie die Schaltfläche **Administrator Inhalt für {TENANT Name} erteilen aus** .</span><span class="sxs-lookup"><span data-stu-id="cecdd-159">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="cecdd-160">Klicken Sie auf **Ja**, um zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="cecdd-160">Select **Yes** to confirm.</span></span>

<span data-ttu-id="cecdd-161">In der **Start**  >  **Azure AD B2C**  >  **benutzerflows**:</span><span class="sxs-lookup"><span data-stu-id="cecdd-161">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="cecdd-162">Erstellen eines Benutzerflows für Registrierung und Anmeldung</span><span class="sxs-lookup"><span data-stu-id="cecdd-162">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="cecdd-163">Wählen Sie mindestens das Benutzer Attribut **Anwendungs Anspruchs**  >  **Anzeige Name** aus, um das `context.User.Identity.Name` in der `LoginDisplay` Komponente (*Shared/logindisplay. Razor*) aufzufüllen.</span><span class="sxs-lookup"><span data-stu-id="cecdd-163">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="cecdd-164">Zeichnen Sie den für die APP erstellten Registrierungs-und Anmeldedaten Flussnamen auf (z `B2C_1_signupsignin` . b.).</span><span class="sxs-lookup"><span data-stu-id="cecdd-164">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="cecdd-165">Erstellen der App</span><span class="sxs-lookup"><span data-stu-id="cecdd-165">Create the app</span></span>

<span data-ttu-id="cecdd-166">Ersetzen Sie die Platzhalter im folgenden Befehl durch die zuvor aufgezeichneten Informationen, und führen Sie den Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="cecdd-166">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="cecdd-167">Um den Ausgabe Speicherort anzugeben, der einen Projektordner erstellt, wenn er nicht vorhanden ist, schließen Sie die Output-Option in den Befehl mit einem Pfad (z `-o BlazorSample` . b.) ein.</span><span class="sxs-lookup"><span data-stu-id="cecdd-167">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="cecdd-168">Der Name des Ordners wird auch Teil des Projekt namens.</span><span class="sxs-lookup"><span data-stu-id="cecdd-168">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="cecdd-169">Übergeben Sie den APP-ID-URI an die- `app-id-uri` Option, aber beachten Sie, dass in der Client-App möglicherweise eine Konfigurationsänderung erforderlich ist, die im Abschnitt [Zugriffs Token-Bereiche](#access-token-scopes) beschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="cecdd-169">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>
>
> <span data-ttu-id="cecdd-170">Darüber hinaus wird für den von der gehosteten Blazor Vorlage eingerichteten Bereich möglicherweise der APP-ID-URI-Host wiederholt.</span><span class="sxs-lookup"><span data-stu-id="cecdd-170">Additionally, the scope set up by the Hosted Blazor template might have the App ID URI host repeated.</span></span> <span data-ttu-id="cecdd-171">Vergewissern Sie sich, dass der für die Sammlung konfigurierte Bereich `DefaultAccessTokenScopes` in `Program.Main` (*Program.cs*) der *Client-App*richtig ist.</span><span class="sxs-lookup"><span data-stu-id="cecdd-171">Confirm that the scope configured for the `DefaultAccessTokenScopes` collection is correct in `Program.Main` (*Program.cs*) of the *Client app*.</span></span>

> [!NOTE]
> <span data-ttu-id="cecdd-172">Im Azure-Portal wird der **Authentication**webumleitungs-URI der Authentifizierung der *Client-App*  >  **Platform configurations**  >  **Web**  >  **Redirect URI** für apps, die auf dem Kestrel-Server mit Standardeinstellungen ausgeführt werden, für Port 5001 konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="cecdd-172">In the Azure portal, the *Client app's* **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="cecdd-173">Wenn die *Client-App* auf einem zufälligen IIS Express Port ausgeführt wird, finden Sie den Port für die app in den Eigenschaften der *Server-App* im **Debug** -Panel.</span><span class="sxs-lookup"><span data-stu-id="cecdd-173">If the *Client app* is run on a random IIS Express port, the port for the app can be found in the *Server app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="cecdd-174">Wenn der Port nicht zuvor mit dem bekannten Port der *Client-App* konfiguriert wurde, kehren Sie zur Registrierung der *Client-App* in der Azure-Portal zurück, und aktualisieren Sie den Umleitungs-URI mit dem richtigen Port.</span><span class="sxs-lookup"><span data-stu-id="cecdd-174">If the port wasn't configured earlier with the *Client app's* known port, return to the *Client app's* registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="cecdd-175">Server-App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="cecdd-175">Server app configuration</span></span>

<span data-ttu-id="cecdd-176">*Dieser Abschnitt bezieht sich auf die **Server** -App der Lösung.*</span><span class="sxs-lookup"><span data-stu-id="cecdd-176">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="cecdd-177">Authentifizierungs Paket</span><span class="sxs-lookup"><span data-stu-id="cecdd-177">Authentication package</span></span>

<span data-ttu-id="cecdd-178">Die Unterstützung für die Authentifizierung und Autorisierung von Aufrufen von ASP.net Core Web-APIs wird durch das [Microsoft. aspnetcore. Authentication. AzureADB2C. UI](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI/) -Paket bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="cecdd-178">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [Microsoft.AspNetCore.Authentication.AzureADB2C.UI](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI/) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="3.1.4" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="cecdd-179">Unterstützung für Authentifizierungsdienst</span><span class="sxs-lookup"><span data-stu-id="cecdd-179">Authentication service support</span></span>

<span data-ttu-id="cecdd-180">Die `AddAuthentication` -Methode richtet Authentifizierungsdienste innerhalb der APP ein und konfiguriert den JWT-bearerhandler als Standard Authentifizierungsmethode.</span><span class="sxs-lookup"><span data-stu-id="cecdd-180">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="cecdd-181">Die- <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> Methode richtet die spezifischen Parameter im JWT-bearerhandler ein, der zum Überprüfen der vom Azure Active Directory B2C ausgegebenen Token erforderlich ist:</span><span class="sxs-lookup"><span data-stu-id="cecdd-181">The <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="cecdd-182"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>und <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> Stellen Sie Folgendes sicher:</span><span class="sxs-lookup"><span data-stu-id="cecdd-182"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="cecdd-183">Die APP versucht, Token bei eingehenden Anforderungen zu analysieren und zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="cecdd-183">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="cecdd-184">Jede Anforderung, die versucht, auf eine geschützte Ressource ohne korrekte Anmelde Informationen zuzugreifen, schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="cecdd-184">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="cecdd-185">Benutzer. Identity .. Benennen</span><span class="sxs-lookup"><span data-stu-id="cecdd-185">User.Identity.Name</span></span>

<span data-ttu-id="cecdd-186">Standardmäßig wird `User.Identity.Name` nicht aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="cecdd-186">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="cecdd-187">Um die APP so zu konfigurieren, dass Sie den Wert aus dem `name` Anspruchstyp empfängt, konfigurieren Sie [tokenvalidationparameters. nameclaimtype](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) von <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="cecdd-187">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="cecdd-188">App-Einstellungen</span><span class="sxs-lookup"><span data-stu-id="cecdd-188">App settings</span></span>

<span data-ttu-id="cecdd-189">Die *appsettings.json* -Datei enthält die Optionen zum Konfigurieren des JWT-Träger Handlers, der zum Überprüfen von Zugriffs Token verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="cecdd-189">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://{TENANT}.b2clogin.com/",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "Domain": "{TENANT DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

<span data-ttu-id="cecdd-190">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cecdd-190">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com/",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "Domain": "contoso.onmicrosoft.com",
    "SignUpSignInPolicyId": "B2C_1_signupsignin1",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="cecdd-191">Weatherforecast-Controller</span><span class="sxs-lookup"><span data-stu-id="cecdd-191">WeatherForecast controller</span></span>

<span data-ttu-id="cecdd-192">Der weatherforecast-Controller (*Controllers/weatherforecastcontroller. cs*) macht eine geschützte API verfügbar, wobei das- [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) Attribut auf den Controller angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="cecdd-192">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="cecdd-193">Es ist **wichtig** , Folgendes zu verstehen:</span><span class="sxs-lookup"><span data-stu-id="cecdd-193">It's **important** to understand that:</span></span>

* <span data-ttu-id="cecdd-194">Das [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) Attribut in diesem API-Controller ist die einzige Möglichkeit, diese API vor nicht autorisiertem Zugriff zu schützen.</span><span class="sxs-lookup"><span data-stu-id="cecdd-194">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="cecdd-195">Das [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) in der Blazor Webassembly-App verwendete Attribut dient nur als Hinweis für die APP, dass der Benutzer autorisiert werden muss, damit die APP ordnungsgemäß funktioniert.</span><span class="sxs-lookup"><span data-stu-id="cecdd-195">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="cecdd-196">Client-App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="cecdd-196">Client app configuration</span></span>

<span data-ttu-id="cecdd-197">*Dieser Abschnitt bezieht sich auf die **Client** -App der Lösung.*</span><span class="sxs-lookup"><span data-stu-id="cecdd-197">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="cecdd-198">Authentifizierungs Paket</span><span class="sxs-lookup"><span data-stu-id="cecdd-198">Authentication package</span></span>

<span data-ttu-id="cecdd-199">Wenn eine APP für die Verwendung eines einzelnen B2C-Kontos ( `IndividualB2C` ) erstellt wird, erhält die APP automatisch einen Paket Verweis für die [Microsoft-Authentifizierungs Bibliothek](/azure/active-directory/develop/msal-overview) ([Microsoft. Authentication. Webassembly. msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span><span class="sxs-lookup"><span data-stu-id="cecdd-199">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="cecdd-200">Das Paket enthält eine Reihe von primitiven, die der APP helfen, Benutzer zu authentifizieren und Token abzurufen, um geschützte APIs aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="cecdd-200">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="cecdd-201">Wenn Sie eine Authentifizierung zu einer APP hinzufügen, fügen Sie das Paket manuell zur Projektdatei der APP hinzu:</span><span class="sxs-lookup"><span data-stu-id="cecdd-201">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="cecdd-202">Das [Microsoft. Authentication. Webassembly. msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) -Paket fügt transitiv das Paket [Microsoft. aspnetcore. Components. Webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) der APP hinzu.</span><span class="sxs-lookup"><span data-stu-id="cecdd-202">The [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="cecdd-203">Unterstützung für Authentifizierungsdienst</span><span class="sxs-lookup"><span data-stu-id="cecdd-203">Authentication service support</span></span>

<span data-ttu-id="cecdd-204">Es wird Unterstützung für- <xref:System.Net.Http.HttpClient> Instanzen hinzugefügt, die Zugriffs Token enthalten, wenn Anforderungen an das Server Projekt gestellt werden.</span><span class="sxs-lookup"><span data-stu-id="cecdd-204">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="cecdd-205">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="cecdd-205">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="cecdd-206">Die Unterstützung für das Authentifizieren von Benutzern wird im Dienst Container mit der <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> vom [Microsoft. Authentication. Webassembly. msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) -Paket bereitgestellten Erweiterungsmethode registriert.</span><span class="sxs-lookup"><span data-stu-id="cecdd-206">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="cecdd-207">Diese Methode richtet die Dienste ein, die für die Interaktion der APP mit dem Identity Anbieter (IP) erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="cecdd-207">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="cecdd-208">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="cecdd-208">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="cecdd-209">Die- <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> Methode akzeptiert einen Rückruf, um die Parameter zu konfigurieren, die zum Authentifizieren einer APP erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="cecdd-209">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="cecdd-210">Die für die Konfiguration der APP erforderlichen Werte können aus der Aad-Konfiguration des Azure-Portals abgerufen werden, wenn Sie die APP registrieren.</span><span class="sxs-lookup"><span data-stu-id="cecdd-210">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="cecdd-211">Die Konfiguration wird durch die Datei *wwwroot/appsettings.jsin* der Datei bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="cecdd-211">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="cecdd-212">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cecdd-212">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="cecdd-213">Zugriffs Token-Bereiche</span><span class="sxs-lookup"><span data-stu-id="cecdd-213">Access token scopes</span></span>

<span data-ttu-id="cecdd-214">Die standardzugriffstokenbereiche stellen die Liste der zugriffstokenbereiche dar, die lauten:</span><span class="sxs-lookup"><span data-stu-id="cecdd-214">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="cecdd-215">Standardmäßig in der Anmelde Anforderung enthalten.</span><span class="sxs-lookup"><span data-stu-id="cecdd-215">Included by default in the sign in request.</span></span>
* <span data-ttu-id="cecdd-216">Wird verwendet, um unmittelbar nach der Authentifizierung ein Zugriffs Token bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="cecdd-216">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="cecdd-217">Alle Bereiche müssen pro Azure Active Directory Regeln zur gleichen App gehören.</span><span class="sxs-lookup"><span data-stu-id="cecdd-217">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="cecdd-218">Für zusätzliche API-Apps können nach Bedarf weitere Bereiche hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="cecdd-218">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="cecdd-219">Weitere Informationen finden Sie in den folgenden Abschnitten des Artikels *weitere Szenarien* :</span><span class="sxs-lookup"><span data-stu-id="cecdd-219">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="cecdd-220">Anfordern zusätzlicher Zugriffstoken</span><span class="sxs-lookup"><span data-stu-id="cecdd-220">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="cecdd-221">Anfügen von Token an ausgehende Anforderungen</span><span class="sxs-lookup"><span data-stu-id="cecdd-221">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="cecdd-222">Import Datei</span><span class="sxs-lookup"><span data-stu-id="cecdd-222">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="cecdd-223">Indexseite</span><span class="sxs-lookup"><span data-stu-id="cecdd-223">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="cecdd-224">App-Komponente</span><span class="sxs-lookup"><span data-stu-id="cecdd-224">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="cecdd-225">Redirecttologin-Komponente</span><span class="sxs-lookup"><span data-stu-id="cecdd-225">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="cecdd-226">Logindisplay-Komponente</span><span class="sxs-lookup"><span data-stu-id="cecdd-226">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="cecdd-227">Authentifizierungs Komponente</span><span class="sxs-lookup"><span data-stu-id="cecdd-227">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="cecdd-228">Fetchdata-Komponente</span><span class="sxs-lookup"><span data-stu-id="cecdd-228">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="cecdd-229">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="cecdd-229">Run the app</span></span>

<span data-ttu-id="cecdd-230">Führen Sie die APP aus dem Server Projekt aus.</span><span class="sxs-lookup"><span data-stu-id="cecdd-230">Run the app from the Server project.</span></span> <span data-ttu-id="cecdd-231">Wenn Sie Visual Studio verwenden, können Sie Folgendes tun:</span><span class="sxs-lookup"><span data-stu-id="cecdd-231">When using Visual Studio, either:</span></span>

* <span data-ttu-id="cecdd-232">Legen Sie die Dropdown Liste **Start Projekte** auf der Symbolleiste auf die *Server-API-APP* fest, und wählen Sie die Schaltfläche **Ausführen** .</span><span class="sxs-lookup"><span data-stu-id="cecdd-232">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="cecdd-233">Wählen Sie das Server Projekt in **Projektmappen-Explorer** aus, und klicken Sie auf der Symbolleiste auf die Schaltfläche **Ausführen** , oder starten Sie die APP im Menü **Debuggen**</span><span class="sxs-lookup"><span data-stu-id="cecdd-233">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="cecdd-234">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="cecdd-234">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="cecdd-235">Nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen in einer APP mit einem sicheren Standard Client</span><span class="sxs-lookup"><span data-stu-id="cecdd-235">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="cecdd-236">Tutorial zum Erstellen eines Azure Active Directory B2C-Mandanten</span><span class="sxs-lookup"><span data-stu-id="cecdd-236">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="cecdd-237">Dokumentation zur Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="cecdd-237">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)

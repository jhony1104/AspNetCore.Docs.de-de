---
<span data-ttu-id="f6ff1-101">Title: "Secure an ASP.net Core Blazor Webassembly hosted App with Azure Active Directory" Author: Description: monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="f6ff1-101">title: 'Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f6ff1-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f6ff1-102">'Blazor'</span></span>
- <span data-ttu-id="f6ff1-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f6ff1-103">'Identity'</span></span>
- <span data-ttu-id="f6ff1-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f6ff1-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="f6ff1-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f6ff1-105">'Razor'</span></span>
- <span data-ttu-id="f6ff1-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f6ff1-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="f6ff1-107">Sichern einer ASP.net Core Blazor Webassembly-gehosteten App mit Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="f6ff1-107">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="f6ff1-108">Von [Javier calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f6ff1-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f6ff1-109">In diesem Artikel wird beschrieben, wie Sie eine [ Blazor Webassembly-gehostete App](xref:blazor/hosting-models#blazor-webassembly) erstellen, die [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) für die Authentifizierung verwendet.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-109">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="f6ff1-110">Registrieren von apps in Aad und Erstellen einer Lösung</span><span class="sxs-lookup"><span data-stu-id="f6ff1-110">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="f6ff1-111">Erstellen eines Mandanten</span><span class="sxs-lookup"><span data-stu-id="f6ff1-111">Create a tenant</span></span>

<span data-ttu-id="f6ff1-112">Befolgen Sie die Anweisungen unter [Schnellstart: Einrichten eines](/azure/active-directory/develop/quickstart-create-new-tenant) Mandanten zum Erstellen eines Mandanten in Aad.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-112">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="f6ff1-113">Registrieren einer Server-API-APP</span><span class="sxs-lookup"><span data-stu-id="f6ff1-113">Register a server API app</span></span>

<span data-ttu-id="f6ff1-114">Befolgen Sie die Anleitungen unter [Schnellstart: Registrieren einer Anwendung mit der Microsoft Identity Platform](/azure/active-directory/develop/quickstart-register-app) und nachfolgende Azure Aad-Themen zum Registrieren einer Aad-App für die *Server-API-APP*:</span><span class="sxs-lookup"><span data-stu-id="f6ff1-114">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app*:</span></span>

1. <span data-ttu-id="f6ff1-115">Wählen Sie in **Azure Active Directory**  >  **App-Registrierungen**die Option **neue Registrierung**aus.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-115">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="f6ff1-116">Geben Sie einen **Namen** für die APP an (z. b. \*\* Blazor Server Aad\*\*).</span><span class="sxs-lookup"><span data-stu-id="f6ff1-116">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="f6ff1-117">Wählen Sie einen **unterstützten Kontotyp**aus.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-117">Choose a **Supported account types**.</span></span> <span data-ttu-id="f6ff1-118">Für diese Vorgehens Art können Sie **nur Konten in diesem Organisations Verzeichnis** auswählen (einzelner Mandant).</span><span class="sxs-lookup"><span data-stu-id="f6ff1-118">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="f6ff1-119">Für die *Server-API-APP* ist in diesem Szenario kein **Umleitungs-URI** erforderlich. lassen Sie die Dropdown-Datei also auf **Web** festgelegt und geben Sie keinen Umleitungs-URI</span><span class="sxs-lookup"><span data-stu-id="f6ff1-119">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="f6ff1-120">Deaktivieren Sie **das**  >  Kontrollkästchen**Administrator Berechtigungen für OpenID und offline_access Berechtigungen gewähren** .</span><span class="sxs-lookup"><span data-stu-id="f6ff1-120">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="f6ff1-121">Wählen Sie **Registrieren** aus.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-121">Select **Register**.</span></span>

<span data-ttu-id="f6ff1-122">Notieren Sie sich die folgenden Informationen:</span><span class="sxs-lookup"><span data-stu-id="f6ff1-122">Record the following information:</span></span>

* <span data-ttu-id="f6ff1-123">*Server-API-APP* Anwendungs-ID (Client-ID) (z. b. `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="f6ff1-123">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="f6ff1-124">Verzeichnis-ID (Mandanten-ID) (z. b. `222222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="f6ff1-124">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="f6ff1-125">Aad-Mandanten Domäne (z. b. `contoso.onmicrosoft.com` ) &ndash; die Domäne ist als **Verleger Domäne** auf dem Blatt **Branding** der Azure-Portal für die registrierte app verfügbar.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-125">AAD Tenant domain (for example, `contoso.onmicrosoft.com`) &ndash; The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="f6ff1-126">Entfernen Sie unter **API-Berechtigungen**die Berechtigung **Microsoft Graph**  >  **User. Read** , da die APP keinen Anmelde-oder Benutzerprofil Zugriff erfordert.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-126">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or user profile access.</span></span>

<span data-ttu-id="f6ff1-127">In machen Sie **eine API**verfügbar:</span><span class="sxs-lookup"><span data-stu-id="f6ff1-127">In **Expose an API**:</span></span>

1. <span data-ttu-id="f6ff1-128">Wählen Sie **Bereich hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-128">Select **Add a scope**.</span></span>
1. <span data-ttu-id="f6ff1-129">Wählen Sie **Speichern und fortfahren** aus.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-129">Select **Save and continue**.</span></span>
1. <span data-ttu-id="f6ff1-130">Geben Sie einen **Bereichs Namen** an (z `API.Access` . b.).</span><span class="sxs-lookup"><span data-stu-id="f6ff1-130">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="f6ff1-131">Geben Sie einen **anzeigen Amen für die Administrator Zustimmung** an (z. b `Access API` .).</span><span class="sxs-lookup"><span data-stu-id="f6ff1-131">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="f6ff1-132">Geben Sie eine Beschreibung für die **Administrator Zustimmung** an (z `Allows the app to access server app API endpoints.` . b.).</span><span class="sxs-lookup"><span data-stu-id="f6ff1-132">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="f6ff1-133">Vergewissern Sie sich, dass der **Status** auf **aktiviert**festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-133">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="f6ff1-134">Wählen Sie **Bereich hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-134">Select **Add scope**.</span></span>

<span data-ttu-id="f6ff1-135">Notieren Sie sich die folgenden Informationen:</span><span class="sxs-lookup"><span data-stu-id="f6ff1-135">Record the following information:</span></span>

* <span data-ttu-id="f6ff1-136">APP-ID-URI (z. b., `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111` oder der von Ihnen angegebene benutzerdefinierte Wert)</span><span class="sxs-lookup"><span data-stu-id="f6ff1-136">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="f6ff1-137">Standardbereich (z. b. `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="f6ff1-137">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="f6ff1-138">Registrieren einer Client-App</span><span class="sxs-lookup"><span data-stu-id="f6ff1-138">Register a client app</span></span>

<span data-ttu-id="f6ff1-139">Befolgen Sie die Anleitungen unter [Schnellstart: Registrieren einer Anwendung mit der Microsoft Identity Platform](/azure/active-directory/develop/quickstart-register-app) und nachfolgende Azure Aad-Themen zum Registrieren einer Aad-App für die *Client-App*:</span><span class="sxs-lookup"><span data-stu-id="f6ff1-139">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app*:</span></span>

1. <span data-ttu-id="f6ff1-140">Wählen Sie in **Azure Active Directory**  >  **App-Registrierungen**die Option **neue Registrierung**aus.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-140">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="f6ff1-141">Geben Sie einen **Namen** für die APP an (z. b. \*\* Blazor Client Aad\*\*).</span><span class="sxs-lookup"><span data-stu-id="f6ff1-141">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="f6ff1-142">Wählen Sie einen **unterstützten Kontotyp**aus.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-142">Choose a **Supported account types**.</span></span> <span data-ttu-id="f6ff1-143">Für diese Vorgehens Art können Sie **nur Konten in diesem Organisations Verzeichnis** auswählen (einzelner Mandant).</span><span class="sxs-lookup"><span data-stu-id="f6ff1-143">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="f6ff1-144">Lassen Sie die Dropdown-Einstellung für **Umleitungs-URI** auf **Web** fest, und geben Sie folgenden Umleitungs-URI `https://localhost:{PORT}/authentication/login-callback`</span><span class="sxs-lookup"><span data-stu-id="f6ff1-144">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="f6ff1-145">Der Standardport für eine APP, die auf Kestrel ausgeführt wird, ist 5001.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-145">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="f6ff1-146">Wenn die APP auf einem anderen Kestrel-Port ausgeführt wird, verwenden Sie den Port der app.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-146">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="f6ff1-147">Für IIS Express befindet sich der zufällig generierte Port für die app in den Eigenschaften der Server-App im **Debug** -Panel.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-147">For IIS Express, the randomly generated port for the app can be found in the Server app's properties in the **Debug** panel.</span></span> <span data-ttu-id="f6ff1-148">Da die APP zu diesem Zeitpunkt noch nicht vorhanden ist und der IIS Express Port nicht bekannt ist, kehren Sie zu diesem Schritt zurück, nachdem die App erstellt wurde, und aktualisieren Sie den Umleitungs-URI.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-148">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="f6ff1-149">Eine Anmerkung wird im Abschnitt [Erstellen der APP](#create-the-app) angezeigt, um IIS Express Benutzer daran zu erinnern, den Umleitungs-URI zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-149">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="f6ff1-150">Deaktivieren Sie **das**  >  Kontrollkästchen**Administrator Berechtigungen für OpenID und offline_access Berechtigungen gewähren** .</span><span class="sxs-lookup"><span data-stu-id="f6ff1-150">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="f6ff1-151">Wählen Sie **Registrieren** aus.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-151">Select **Register**.</span></span>

<span data-ttu-id="f6ff1-152">Notieren Sie sich die Anwendungs-ID der *Client-App* (Client-ID) (z `33333333-3333-3333-3333-333333333333` . b.).</span><span class="sxs-lookup"><span data-stu-id="f6ff1-152">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

<span data-ttu-id="f6ff1-153">Im Web der **Authentifizierungs**  >  **Platt Form Konfigurationen**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="f6ff1-153">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="f6ff1-154">Bestätigen Sie, dass der **Umleitungs-URI** von `https://localhost:{PORT}/authentication/login-callback` vorhanden ist</span><span class="sxs-lookup"><span data-stu-id="f6ff1-154">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="f6ff1-155">Aktivieren Sie für **implizite Gewährung**die Kontrollkästchen für **Zugriffs Token** und **ID-Token**.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-155">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="f6ff1-156">Die verbleibenden Standardwerte für die APP sind für diese Art von akzeptabel.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-156">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="f6ff1-157">Wählen Sie die Schaltfläche **Speichern** aus.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-157">Select the **Save** button.</span></span>

<span data-ttu-id="f6ff1-158">In **API-Berechtigungen**:</span><span class="sxs-lookup"><span data-stu-id="f6ff1-158">In **API permissions**:</span></span>

1. <span data-ttu-id="f6ff1-159">Vergewissern Sie sich, dass die APP über **Microsoft Graph**  >  **User. Read** -Berechtigung verfügt.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-159">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="f6ff1-160">Wählen Sie **Berechtigung hinzufügen** und dann **meine APIs**aus.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-160">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="f6ff1-161">Wählen Sie die *Server-API-APP* aus der Spalte **Name** (z. b. \*\* Blazor Server Aad\*\*) aus.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-161">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="f6ff1-162">Öffnen Sie die **API** -Liste.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-162">Open the **API** list.</span></span>
1. <span data-ttu-id="f6ff1-163">Aktivieren Sie den Zugriff auf die API (z `API.Access` . b.).</span><span class="sxs-lookup"><span data-stu-id="f6ff1-163">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="f6ff1-164">Wählen Sie **Berechtigungen hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-164">Select **Add permissions**.</span></span>
1. <span data-ttu-id="f6ff1-165">Wählen Sie die Schaltfläche **Administrator Inhalt für {TENANT Name} erteilen aus** .</span><span class="sxs-lookup"><span data-stu-id="f6ff1-165">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="f6ff1-166">Klicken Sie zum Bestätigen auf **Ja**.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-166">Select **Yes** to confirm.</span></span>

### <a name="create-the-app"></a><span data-ttu-id="f6ff1-167">Erstellen der App</span><span class="sxs-lookup"><span data-stu-id="f6ff1-167">Create the app</span></span>

<span data-ttu-id="f6ff1-168">Ersetzen Sie die Platzhalter im folgenden Befehl durch die zuvor aufgezeichneten Informationen, und führen Sie den Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="f6ff1-168">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="f6ff1-169">Um den Ausgabe Speicherort anzugeben, der einen Projektordner erstellt, wenn er nicht vorhanden ist, schließen Sie die Output-Option in den Befehl mit einem Pfad (z `-o BlazorSample` . b.) ein.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-169">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="f6ff1-170">Der Name des Ordners wird auch Teil des Projekt namens.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-170">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="f6ff1-171">Übergeben Sie den APP-ID-URI an die- `app-id-uri` Option, aber beachten Sie, dass in der Client-App möglicherweise eine Konfigurationsänderung erforderlich ist, die im Abschnitt [Zugriffs Token-Bereiche](#access-token-scopes) beschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-171">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

> [!NOTE]
> <span data-ttu-id="f6ff1-172">Im Azure-Portal wird der **Authentication**webumleitungs-URI der Authentifizierung der *Client-App*  >  **Platform configurations**  >  **Web**  >  **Redirect URI** für apps, die auf dem Kestrel-Server mit Standardeinstellungen ausgeführt werden, für Port 5001 konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-172">In the Azure portal, the *Client app's* **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="f6ff1-173">Wenn die *Client-App* auf einem zufälligen IIS Express Port ausgeführt wird, finden Sie den Port für die app in den Eigenschaften der *Server-App* im **Debug** -Panel.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-173">If the *Client app* is run on a random IIS Express port, the port for the app can be found in the *Server app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="f6ff1-174">Wenn der Port nicht zuvor mit dem bekannten Port der *Client-App* konfiguriert wurde, kehren Sie zur Registrierung der *Client-App* in der Azure-Portal zurück, und aktualisieren Sie den Umleitungs-URI mit dem richtigen Port.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-174">If the port wasn't configured earlier with the *Client app's* known port, return to the *Client app's* registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="f6ff1-175">Server-App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="f6ff1-175">Server app configuration</span></span>

<span data-ttu-id="f6ff1-176">*Dieser Abschnitt bezieht sich auf die **Server** -App der Lösung.*</span><span class="sxs-lookup"><span data-stu-id="f6ff1-176">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="f6ff1-177">Authentifizierungs Paket</span><span class="sxs-lookup"><span data-stu-id="f6ff1-177">Authentication package</span></span>

<span data-ttu-id="f6ff1-178">Die Unterstützung für die Authentifizierung und Autorisierung von Aufrufen von ASP.net Core Web-APIs wird durch das [Microsoft. aspnetcore. Authentication. azuread. UI](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI/) -Paket bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="f6ff1-178">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [Microsoft.AspNetCore.Authentication.AzureAD.UI](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI/) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="3.1.4" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="f6ff1-179">Unterstützung für Authentifizierungsdienst</span><span class="sxs-lookup"><span data-stu-id="f6ff1-179">Authentication service support</span></span>

<span data-ttu-id="f6ff1-180">Die `AddAuthentication` -Methode richtet Authentifizierungsdienste innerhalb der APP ein und konfiguriert den JWT-bearerhandler als Standard Authentifizierungsmethode.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-180">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="f6ff1-181">Die- <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> Methode richtet die spezifischen Parameter im JWT-bearerhandler ein, der zum Überprüfen der vom Azure Active Directory ausgegebenen Token erforderlich ist:</span><span class="sxs-lookup"><span data-stu-id="f6ff1-181">The <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="f6ff1-182"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>und <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> Stellen Sie Folgendes sicher:</span><span class="sxs-lookup"><span data-stu-id="f6ff1-182"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="f6ff1-183">Die APP versucht, Token bei eingehenden Anforderungen zu analysieren und zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-183">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="f6ff1-184">Jede Anforderung, die versucht, auf eine geschützte Ressource ohne korrekte Anmelde Informationen zuzugreifen, schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-184">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="f6ff1-185">Benutzer. Identity .. Benennen</span><span class="sxs-lookup"><span data-stu-id="f6ff1-185">User.Identity.Name</span></span>

<span data-ttu-id="f6ff1-186">Standardmäßig füllt die Server-App-API `User.Identity.Name` mit dem Wert aus dem `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` Anspruchstyp (z. b `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com` .).</span><span class="sxs-lookup"><span data-stu-id="f6ff1-186">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="f6ff1-187">Um die APP so zu konfigurieren, dass Sie den Wert aus dem `name` Anspruchstyp empfängt, konfigurieren Sie [tokenvalidationparameters. nameclaimtype](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) von <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f6ff1-187">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="f6ff1-188">App-Einstellungen</span><span class="sxs-lookup"><span data-stu-id="f6ff1-188">App settings</span></span>

<span data-ttu-id="f6ff1-189">Die Datei *appSettings. JSON* enthält die Optionen zum Konfigurieren des JWT-bearerhandlers, der zum Überprüfen von Zugriffs Token verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="f6ff1-189">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

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

<span data-ttu-id="f6ff1-190">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f6ff1-190">Example:</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="f6ff1-191">Weatherforecast-Controller</span><span class="sxs-lookup"><span data-stu-id="f6ff1-191">WeatherForecast controller</span></span>

<span data-ttu-id="f6ff1-192">Der weatherforecast-Controller (*Controllers/weatherforecastcontroller. cs*) macht eine geschützte API verfügbar, wobei das- [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) Attribut auf den Controller angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-192">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="f6ff1-193">Es ist **wichtig** , Folgendes zu verstehen:</span><span class="sxs-lookup"><span data-stu-id="f6ff1-193">It's **important** to understand that:</span></span>

* <span data-ttu-id="f6ff1-194">Das [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) Attribut in diesem API-Controller ist die einzige Möglichkeit, diese API vor nicht autorisiertem Zugriff zu schützen.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-194">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="f6ff1-195">Das [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) in der Blazor Webassembly-App verwendete Attribut dient nur als Hinweis für die APP, dass der Benutzer autorisiert werden muss, damit die APP ordnungsgemäß funktioniert.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-195">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="f6ff1-196">Client-App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="f6ff1-196">Client app configuration</span></span>

<span data-ttu-id="f6ff1-197">*Dieser Abschnitt bezieht sich auf die **Client** -App der Lösung.*</span><span class="sxs-lookup"><span data-stu-id="f6ff1-197">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="f6ff1-198">Authentifizierungs Paket</span><span class="sxs-lookup"><span data-stu-id="f6ff1-198">Authentication package</span></span>

<span data-ttu-id="f6ff1-199">Wenn eine APP für die Verwendung von Geschäfts-, Schul-oder unikonten () erstellt wird `SingleOrg` , erhält die APP automatisch einen Paket Verweis für die [Microsoft-Authentifizierungs Bibliothek](/azure/active-directory/develop/msal-overview) ([Microsoft. Authentication. Webassembly. msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span><span class="sxs-lookup"><span data-stu-id="f6ff1-199">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="f6ff1-200">Das Paket enthält eine Reihe von primitiven, die der APP helfen, Benutzer zu authentifizieren und Token abzurufen, um geschützte APIs aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-200">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="f6ff1-201">Wenn Sie eine Authentifizierung zu einer APP hinzufügen, fügen Sie das Paket manuell zur Projektdatei der APP hinzu:</span><span class="sxs-lookup"><span data-stu-id="f6ff1-201">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="f6ff1-202">Das [Microsoft. Authentication. Webassembly. msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) -Paket fügt transitiv das Paket [Microsoft. aspnetcore. Components. Webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) der APP hinzu.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-202">The [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="f6ff1-203">Unterstützung für Authentifizierungsdienst</span><span class="sxs-lookup"><span data-stu-id="f6ff1-203">Authentication service support</span></span>

<span data-ttu-id="f6ff1-204">Es wird Unterstützung für- <xref:System.Net.Http.HttpClient> Instanzen hinzugefügt, die Zugriffs Token enthalten, wenn Anforderungen an das Server Projekt gestellt werden.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-204">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="f6ff1-205">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="f6ff1-205">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="f6ff1-206">Die Unterstützung für das Authentifizieren von Benutzern wird im Dienst Container mit der <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> vom [Microsoft. Authentication. Webassembly. msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) -Paket bereitgestellten Erweiterungsmethode registriert.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-206">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="f6ff1-207">Diese Methode richtet die Dienste ein, die für die Interaktion der APP mit dem Identity Anbieter (IP) erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-207">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="f6ff1-208">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="f6ff1-208">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="f6ff1-209">Die- <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> Methode akzeptiert einen Rückruf, um die Parameter zu konfigurieren, die zum Authentifizieren einer APP erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-209">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="f6ff1-210">Die für die Konfiguration der APP erforderlichen Werte können aus der Aad-Konfiguration des Azure-Portals abgerufen werden, wenn Sie die APP registrieren.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-210">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="f6ff1-211">Die Konfiguration wird von der Datei " *wwwroot/appSettings. JSON* " bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="f6ff1-211">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="f6ff1-212">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f6ff1-212">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="f6ff1-213">Zugriffs Token-Bereiche</span><span class="sxs-lookup"><span data-stu-id="f6ff1-213">Access token scopes</span></span>

<span data-ttu-id="f6ff1-214">Die standardzugriffstokenbereiche stellen die Liste der zugriffstokenbereiche dar, die lauten:</span><span class="sxs-lookup"><span data-stu-id="f6ff1-214">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="f6ff1-215">Standardmäßig in der Anmelde Anforderung enthalten.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-215">Included by default in the sign in request.</span></span>
* <span data-ttu-id="f6ff1-216">Wird verwendet, um unmittelbar nach der Authentifizierung ein Zugriffs Token bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-216">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="f6ff1-217">Alle Bereiche müssen pro Azure Active Directory Regeln zur gleichen App gehören.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-217">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="f6ff1-218">Für zusätzliche API-Apps können nach Bedarf weitere Bereiche hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="f6ff1-218">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="f6ff1-219">Weitere Informationen finden Sie in den folgenden Abschnitten des Artikels *weitere Szenarien* :</span><span class="sxs-lookup"><span data-stu-id="f6ff1-219">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="f6ff1-220">Anfordern zusätzlicher Zugriffstoken</span><span class="sxs-lookup"><span data-stu-id="f6ff1-220">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="f6ff1-221">Anfügen von Token an ausgehende Anforderungen</span><span class="sxs-lookup"><span data-stu-id="f6ff1-221">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="f6ff1-222">Import Datei</span><span class="sxs-lookup"><span data-stu-id="f6ff1-222">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="f6ff1-223">Indexseite</span><span class="sxs-lookup"><span data-stu-id="f6ff1-223">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="f6ff1-224">App-Komponente</span><span class="sxs-lookup"><span data-stu-id="f6ff1-224">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="f6ff1-225">Redirecttologin-Komponente</span><span class="sxs-lookup"><span data-stu-id="f6ff1-225">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="f6ff1-226">Logindisplay-Komponente</span><span class="sxs-lookup"><span data-stu-id="f6ff1-226">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="f6ff1-227">Authentifizierungs Komponente</span><span class="sxs-lookup"><span data-stu-id="f6ff1-227">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="f6ff1-228">Fetchdata-Komponente</span><span class="sxs-lookup"><span data-stu-id="f6ff1-228">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="f6ff1-229">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="f6ff1-229">Run the app</span></span>

<span data-ttu-id="f6ff1-230">Führen Sie die APP aus dem Server Projekt aus.</span><span class="sxs-lookup"><span data-stu-id="f6ff1-230">Run the app from the Server project.</span></span> <span data-ttu-id="f6ff1-231">Wenn Sie Visual Studio verwenden, können Sie Folgendes tun:</span><span class="sxs-lookup"><span data-stu-id="f6ff1-231">When using Visual Studio, either:</span></span>

* <span data-ttu-id="f6ff1-232">Legen Sie die Dropdown Liste **Start Projekte** auf der Symbolleiste auf die *Server-API-APP* fest, und wählen Sie die Schaltfläche **Ausführen** .</span><span class="sxs-lookup"><span data-stu-id="f6ff1-232">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="f6ff1-233">Wählen Sie das Server Projekt in **Projektmappen-Explorer** aus, und klicken Sie auf der Symbolleiste auf die Schaltfläche **Ausführen** , oder starten Sie die APP im Menü **Debuggen**</span><span class="sxs-lookup"><span data-stu-id="f6ff1-233">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="f6ff1-234">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="f6ff1-234">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="f6ff1-235">Nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen in einer APP mit einem sicheren Standard Client</span><span class="sxs-lookup"><span data-stu-id="f6ff1-235">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="f6ff1-236">Dokumentation zur Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="f6ff1-236">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)

---
<span data-ttu-id="998f6-101">Title: "Secure an ASP.net Core Blazor eigenständige Webassembly-App mit Azure Active Directory B2C" Author: Description: monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="998f6-101">title: 'Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="998f6-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="998f6-102">'Blazor'</span></span>
- <span data-ttu-id="998f6-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="998f6-103">'Identity'</span></span>
- <span data-ttu-id="998f6-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="998f6-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="998f6-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="998f6-105">'Razor'</span></span>
- <span data-ttu-id="998f6-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="998f6-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="998f6-107">Sichern einer ASP.net Core Blazor eigenständigen Webassembly-App mit Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="998f6-107">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="998f6-108">Von [Javier calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="998f6-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="998f6-109">So erstellen Sie eine Blazor eigenständige Webassembly-APP, die [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) für die Authentifizierung verwendet:</span><span class="sxs-lookup"><span data-stu-id="998f6-109">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

<span data-ttu-id="998f6-110">Befolgen Sie die Anweisungen in den folgenden Themen, um einen Mandanten zu erstellen und eine Web-App im Azure-Portal zu registrieren:</span><span class="sxs-lookup"><span data-stu-id="998f6-110">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

[<span data-ttu-id="998f6-111">Erstellen eines Aad B2C Mandanten</span><span class="sxs-lookup"><span data-stu-id="998f6-111">Create an AAD B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)

<span data-ttu-id="998f6-112">Notieren Sie sich die folgenden Informationen:</span><span class="sxs-lookup"><span data-stu-id="998f6-112">Record the following information:</span></span>

* <span data-ttu-id="998f6-113">Aad B2C Instanz (z. b `https://contoso.b2clogin.com/` ., die den nachgestellten Schrägstrich einschließt).</span><span class="sxs-lookup"><span data-stu-id="998f6-113">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash).</span></span>
* <span data-ttu-id="998f6-114">Aad B2C Mandanten Domäne (z. b `contoso.onmicrosoft.com` .).</span><span class="sxs-lookup"><span data-stu-id="998f6-114">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="998f6-115">Befolgen Sie die Anweisungen im [Tutorial: Registrieren einer Anwendung in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) erneut, um eine Aad-App für die *Client-App*zu registrieren:</span><span class="sxs-lookup"><span data-stu-id="998f6-115">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app*:</span></span>

1. <span data-ttu-id="998f6-116">Wählen Sie in **Azure Active Directory**  >  **App-Registrierungen**die Option **neue Registrierung**aus.</span><span class="sxs-lookup"><span data-stu-id="998f6-116">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="998f6-117">Geben Sie einen **Namen** für die APP an (z. b. \*\* Blazor eigenständiges Aad B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="998f6-117">Provide a **Name** for the app (for example, **Blazor Standalone AAD B2C**).</span></span>
1. <span data-ttu-id="998f6-118">Wählen Sie für **unterstützte Konto Typen**die Option für mehrere Mandanten aus: **Konten in einem beliebigen Organisations Verzeichnis oder einem beliebigen Identitäts Anbieter. Zum Authentifizieren von Benutzern mit Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="998f6-118">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="998f6-119">Lassen Sie die Dropdown-Einstellung für **Umleitungs-URI** auf **Web** fest, und geben Sie folgenden Umleitungs-URI `https://localhost:{PORT}/authentication/login-callback`</span><span class="sxs-lookup"><span data-stu-id="998f6-119">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="998f6-120">Der Standardport für eine APP, die auf Kestrel ausgeführt wird, ist 5001.</span><span class="sxs-lookup"><span data-stu-id="998f6-120">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="998f6-121">Wenn die APP auf einem anderen Kestrel-Port ausgeführt wird, verwenden Sie den Port der app.</span><span class="sxs-lookup"><span data-stu-id="998f6-121">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="998f6-122">Für IIS Express befindet sich der zufällig generierte Port für die app in den Eigenschaften der APP im **Debug** -Panel.</span><span class="sxs-lookup"><span data-stu-id="998f6-122">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="998f6-123">Da die APP zu diesem Zeitpunkt noch nicht vorhanden ist und der IIS Express Port nicht bekannt ist, kehren Sie zu diesem Schritt zurück, nachdem die App erstellt wurde, und aktualisieren Sie den Umleitungs-URI.</span><span class="sxs-lookup"><span data-stu-id="998f6-123">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="998f6-124">Eine Anmerkung wird später in diesem Thema angezeigt, um IIS Express Benutzer daran zu erinnern, den Umleitungs-URI zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="998f6-124">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="998f6-125">Vergewissern Sie sich **, dass die Berechtigungen**  >  **admin keine Zustimmung für OpenID und offline_access Berechtigungen gewähren** aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="998f6-125">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="998f6-126">Wählen Sie **Registrieren** aus.</span><span class="sxs-lookup"><span data-stu-id="998f6-126">Select **Register**.</span></span>

<span data-ttu-id="998f6-127">Notieren Sie sich die Anwendungs-ID (Client-ID) (z `11111111-1111-1111-1111-111111111111` . b.).</span><span class="sxs-lookup"><span data-stu-id="998f6-127">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="998f6-128">Im Web der **Authentifizierungs**  >  **Platt Form Konfigurationen**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="998f6-128">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="998f6-129">Bestätigen Sie, dass der **Umleitungs-URI** von `https://localhost:{PORT}/authentication/login-callback` vorhanden ist</span><span class="sxs-lookup"><span data-stu-id="998f6-129">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="998f6-130">Aktivieren Sie für **implizite Gewährung**die Kontrollkästchen für **Zugriffs Token** und **ID-Token**.</span><span class="sxs-lookup"><span data-stu-id="998f6-130">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="998f6-131">Die verbleibenden Standardwerte für die APP sind für diese Art von akzeptabel.</span><span class="sxs-lookup"><span data-stu-id="998f6-131">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="998f6-132">Wählen Sie die Schaltfläche **Speichern** aus.</span><span class="sxs-lookup"><span data-stu-id="998f6-132">Select the **Save** button.</span></span>

<span data-ttu-id="998f6-133">In der **Start**  >  **Azure AD B2C**  >  **benutzerflows**:</span><span class="sxs-lookup"><span data-stu-id="998f6-133">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="998f6-134">Erstellen eines Benutzerflows für Registrierung und Anmeldung</span><span class="sxs-lookup"><span data-stu-id="998f6-134">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="998f6-135">Wählen Sie mindestens das Benutzer Attribut **Anwendungs Anspruchs**  >  **Anzeige Name** aus, um das `context.User.Identity.Name` in der `LoginDisplay` Komponente (*Shared/logindisplay. Razor*) aufzufüllen.</span><span class="sxs-lookup"><span data-stu-id="998f6-135">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="998f6-136">Zeichnen Sie den für die APP erstellten Registrierungs-und Anmeldedaten Flussnamen auf (z `B2C_1_signupsignin` . b.).</span><span class="sxs-lookup"><span data-stu-id="998f6-136">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

<span data-ttu-id="998f6-137">Ersetzen Sie die Platzhalter im folgenden Befehl durch die zuvor aufgezeichneten Informationen, und führen Sie den Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="998f6-137">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{TENANT DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
```

<span data-ttu-id="998f6-138">Um den Ausgabe Speicherort anzugeben, der einen Projektordner erstellt, wenn er nicht vorhanden ist, schließen Sie die Output-Option in den Befehl mit einem Pfad (z `-o BlazorSample` . b.) ein.</span><span class="sxs-lookup"><span data-stu-id="998f6-138">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="998f6-139">Der Name des Ordners wird auch Teil des Projekt namens.</span><span class="sxs-lookup"><span data-stu-id="998f6-139">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="998f6-140">Im Azure-Portal wird der **Authentication**  >  **Platform configurations**  >  **webumleitungs**-URI der Authentifizierungsplattform der APP  >  **Redirect URI** für apps, die auf dem Kestrel-Server mit Standardeinstellungen ausgeführt werden, für Port 5001 konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="998f6-140">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="998f6-141">Wenn die APP auf einem zufälligen IIS Express Port ausgeführt wird, finden Sie den Port für die app in den Eigenschaften der APP im **Debug** -Panel.</span><span class="sxs-lookup"><span data-stu-id="998f6-141">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="998f6-142">Wenn der Port nicht zuvor mit dem bekannten Port der App konfiguriert wurde, kehren Sie zur Registrierung der APP im Azure-Portal zurück, und aktualisieren Sie den Umleitungs-URI mit dem richtigen Port.</span><span class="sxs-lookup"><span data-stu-id="998f6-142">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="998f6-143">Nachdem Sie die App erstellt haben, sollten Sie folgende Aktionen ausführen können:</span><span class="sxs-lookup"><span data-stu-id="998f6-143">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="998f6-144">Melden Sie sich mit einem Aad-Benutzerkonto bei der APP an.</span><span class="sxs-lookup"><span data-stu-id="998f6-144">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="998f6-145">Anfordern von Zugriffs Token für Microsoft-APIs.</span><span class="sxs-lookup"><span data-stu-id="998f6-145">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="998f6-146">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="998f6-146">For more information, see:</span></span>
  * [<span data-ttu-id="998f6-147">Zugriffs Token-Bereiche</span><span class="sxs-lookup"><span data-stu-id="998f6-147">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="998f6-148">[Schnellstart: Konfigurieren einer Anwendung für die Bereitstellung von Web-APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="998f6-148">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="998f6-149">Authentifizierungs Paket</span><span class="sxs-lookup"><span data-stu-id="998f6-149">Authentication package</span></span>

<span data-ttu-id="998f6-150">Wenn eine APP für die Verwendung eines einzelnen B2C-Kontos ( `IndividualB2C` ) erstellt wird, erhält die APP automatisch einen Paket Verweis für die [Microsoft-Authentifizierungs Bibliothek](/azure/active-directory/develop/msal-overview) ( `Microsoft.Authentication.WebAssembly.Msal` ).</span><span class="sxs-lookup"><span data-stu-id="998f6-150">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="998f6-151">Das Paket enthält eine Reihe von primitiven, die der APP helfen, Benutzer zu authentifizieren und Token abzurufen, um geschützte APIs aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="998f6-151">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="998f6-152">Wenn Sie eine Authentifizierung zu einer APP hinzufügen, fügen Sie das Paket manuell zur Projektdatei der APP hinzu:</span><span class="sxs-lookup"><span data-stu-id="998f6-152">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="998f6-153">Das `Microsoft.Authentication.WebAssembly.Msal` Paket fügt das Paket transitiv der `Microsoft.AspNetCore.Components.WebAssembly.Authentication` app hinzu.</span><span class="sxs-lookup"><span data-stu-id="998f6-153">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="998f6-154">Unterstützung für Authentifizierungsdienst</span><span class="sxs-lookup"><span data-stu-id="998f6-154">Authentication service support</span></span>

<span data-ttu-id="998f6-155">Die Unterstützung für das Authentifizieren von Benutzern wird im Dienst Container mit der `AddMsalAuthentication` vom Paket bereitgestellten Erweiterungsmethode registriert `Microsoft.Authentication.WebAssembly.Msal` .</span><span class="sxs-lookup"><span data-stu-id="998f6-155">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="998f6-156">Diese Methode richtet alle Dienste ein, die für die Interaktion der APP mit dem Identity Anbieter (IP) erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="998f6-156">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="998f6-157">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="998f6-157">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="998f6-158">Die- `AddMsalAuthentication` Methode akzeptiert einen Rückruf, um die Parameter zu konfigurieren, die zum Authentifizieren einer APP erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="998f6-158">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="998f6-159">Die für die Konfiguration der APP erforderlichen Werte können aus der Aad-Konfiguration abgerufen werden, wenn Sie die APP registrieren.</span><span class="sxs-lookup"><span data-stu-id="998f6-159">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="998f6-160">Die Konfiguration wird von der Datei " *wwwroot/appSettings. JSON* " bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="998f6-160">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="998f6-161">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="998f6-161">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": false
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="998f6-162">Zugriffs Token-Bereiche</span><span class="sxs-lookup"><span data-stu-id="998f6-162">Access token scopes</span></span>

<span data-ttu-id="998f6-163">Die Blazor webassemblyvorlage konfiguriert die APP nicht automatisch, um ein Zugriffs Token für eine sichere API anzufordern.</span><span class="sxs-lookup"><span data-stu-id="998f6-163">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="998f6-164">Fügen Sie zum Bereitstellen eines Zugriffs Tokens als Teil des Anmelde Flusses den Gültigkeitsbereich zu den standardzugriffstokenbereichen von hinzu `MsalProviderOptions` :</span><span class="sxs-lookup"><span data-stu-id="998f6-164">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="998f6-165">Weitere Informationen finden Sie in den folgenden Abschnitten des Artikels *weitere Szenarien* :</span><span class="sxs-lookup"><span data-stu-id="998f6-165">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="998f6-166">Anfordern zusätzlicher Zugriffstoken</span><span class="sxs-lookup"><span data-stu-id="998f6-166">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="998f6-167">Anfügen von Token an ausgehende Anforderungen</span><span class="sxs-lookup"><span data-stu-id="998f6-167">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="998f6-168">Import Datei</span><span class="sxs-lookup"><span data-stu-id="998f6-168">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="998f6-169">Indexseite</span><span class="sxs-lookup"><span data-stu-id="998f6-169">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="998f6-170">App-Komponente</span><span class="sxs-lookup"><span data-stu-id="998f6-170">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="998f6-171">Redirecttologin-Komponente</span><span class="sxs-lookup"><span data-stu-id="998f6-171">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="998f6-172">Logindisplay-Komponente</span><span class="sxs-lookup"><span data-stu-id="998f6-172">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="998f6-173">Authentifizierungs Komponente</span><span class="sxs-lookup"><span data-stu-id="998f6-173">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="998f6-174">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="998f6-174">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="998f6-175">Nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen in einer APP mit einem sicheren Standard Client</span><span class="sxs-lookup"><span data-stu-id="998f6-175">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="998f6-176">Tutorial zum Erstellen eines Azure Active Directory B2C-Mandanten</span><span class="sxs-lookup"><span data-stu-id="998f6-176">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="998f6-177">Dokumentation zur Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="998f6-177">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)

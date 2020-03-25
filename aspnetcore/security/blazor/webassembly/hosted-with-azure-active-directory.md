---
title: Sichern eines ASP.net Core Blazor gehosteten Webassembly-App mit Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: fc16a7212254e73efd4cea8155975f293e5d9ebb
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/24/2020
ms.locfileid: "80219284"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="56430-102">Sichern eines ASP.net Core Blazor gehosteten Webassembly-App mit Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="56430-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="56430-103">Von [Javier calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="56430-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]



<span data-ttu-id="56430-104">In diesem Artikel wird beschrieben, wie Sie eine [Blazor Webassembly-gehostete App](xref:blazor/hosting-models#blazor-webassembly) erstellen, die [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) für die Authentifizierung verwendet.</span><span class="sxs-lookup"><span data-stu-id="56430-104">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="56430-105">Registrieren von apps in Aad B2C und Erstellen einer Lösung</span><span class="sxs-lookup"><span data-stu-id="56430-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="56430-106">Erstellen eines Mandanten</span><span class="sxs-lookup"><span data-stu-id="56430-106">Create a tenant</span></span>

<span data-ttu-id="56430-107">Befolgen Sie die Anweisungen unter [Schnellstart: Einrichten eines](/azure/active-directory/develop/quickstart-create-new-tenant) Mandanten zum Erstellen eines Mandanten in Aad.</span><span class="sxs-lookup"><span data-stu-id="56430-107">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="56430-108">Registrieren einer Server-API-APP</span><span class="sxs-lookup"><span data-stu-id="56430-108">Register a server API app</span></span>

<span data-ttu-id="56430-109">Befolgen Sie die Anleitungen unter [Schnellstart: Registrieren einer Anwendung mit der Microsoft Identity Platform](/azure/active-directory/develop/quickstart-register-app) und nachfolgende Azure Aad-Themen zum Registrieren einer Aad-App für die *Server-API-APP* im Bereich **Azure Active Directory** > **App-Registrierungen** des Azure-Portal:</span><span class="sxs-lookup"><span data-stu-id="56430-109">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="56430-110">Wählen Sie **Neue Registrierung** aus.</span><span class="sxs-lookup"><span data-stu-id="56430-110">Select **New registration**.</span></span>
1. <span data-ttu-id="56430-111">Geben Sie einen **Namen** für die APP an (z. b. **Blazor Server Aad**).</span><span class="sxs-lookup"><span data-stu-id="56430-111">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="56430-112">Wählen Sie einen **unterstützten Kontotyp**aus.</span><span class="sxs-lookup"><span data-stu-id="56430-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="56430-113">Für diese Vorgehens Art können Sie **nur Konten in diesem Organisations Verzeichnis** auswählen (einzelner Mandant).</span><span class="sxs-lookup"><span data-stu-id="56430-113">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="56430-114">Für die *Server-API-APP* ist in diesem Szenario kein **Umleitungs-URI** erforderlich. lassen Sie die Dropdown-Datei also auf **Web** festgelegt und geben Sie keinen Umleitungs-URI</span><span class="sxs-lookup"><span data-stu-id="56430-114">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="56430-115">Deaktivieren Sie das Kontrollkästchen **Berechtigungen** > **erteilen Sie Administrator Berechtigungen für OpenID und offline_access Berechtigungen** .</span><span class="sxs-lookup"><span data-stu-id="56430-115">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="56430-116">Wählen Sie **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="56430-116">Select **Register**.</span></span>

<span data-ttu-id="56430-117">Entfernen Sie unter **API-Berechtigungen**die **Microsoft Graph** > **User. Read** -Berechtigung, da die APP keinen Anmelde-oder uerprofilzugriff erfordert.</span><span class="sxs-lookup"><span data-stu-id="56430-117">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or uer profile access.</span></span>

<span data-ttu-id="56430-118">In machen Sie **eine API**verfügbar:</span><span class="sxs-lookup"><span data-stu-id="56430-118">In **Expose an API**:</span></span>

1. <span data-ttu-id="56430-119">Wählen Sie **Bereich hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="56430-119">Select **Add a scope**.</span></span>
1. <span data-ttu-id="56430-120">Wählen Sie **Speichern und fortfahren** aus.</span><span class="sxs-lookup"><span data-stu-id="56430-120">Select **Save and continue**.</span></span>
1. <span data-ttu-id="56430-121">Geben Sie einen **Bereichs Namen** an (z. b. `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="56430-121">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="56430-122">Geben Sie einen **anzeigen Amen für die Administrator Zustimmung** an (z. b. `Access API`).</span><span class="sxs-lookup"><span data-stu-id="56430-122">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="56430-123">Geben Sie eine Beschreibung für die **Administrator Zustimmung** an (z. b. `Allows the app to access server app API endpoints.`).</span><span class="sxs-lookup"><span data-stu-id="56430-123">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="56430-124">Vergewissern Sie sich, dass der **Status** auf **aktiviert**festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="56430-124">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="56430-125">Wählen Sie **Bereich hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="56430-125">Select **Add scope**.</span></span>

<span data-ttu-id="56430-126">Notieren Sie sich die folgenden Informationen:</span><span class="sxs-lookup"><span data-stu-id="56430-126">Record the following information:</span></span>

* <span data-ttu-id="56430-127">*Server-API-APP* Anwendungs-ID (Client-ID) (z. b. `11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="56430-127">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="56430-128">Verzeichnis-ID (Mandanten-ID) (z. b. `222222222-2222-2222-2222-222222222222`)</span><span class="sxs-lookup"><span data-stu-id="56430-128">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="56430-129">Aad-Mandanten Domäne (z. b. `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="56430-129">AAD Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>
* <span data-ttu-id="56430-130">Standardbereich (z. b. `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="56430-130">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="56430-131">Registrieren einer Client-App</span><span class="sxs-lookup"><span data-stu-id="56430-131">Register a client app</span></span>

<span data-ttu-id="56430-132">Befolgen Sie die Anleitungen unter [Schnellstart: Registrieren einer Anwendung mit der Microsoft Identity Platform](/azure/active-directory/develop/quickstart-register-app) und nachfolgende Azure Aad-Themen zum Registrieren einer Aad-App für die *Client-App* in der **Azure Active Directory** > **App-Registrierungen** Bereich der Azure-Portal:</span><span class="sxs-lookup"><span data-stu-id="56430-132">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="56430-133">Wählen Sie **Neue Registrierung** aus.</span><span class="sxs-lookup"><span data-stu-id="56430-133">Select **New registration**.</span></span>
1. <span data-ttu-id="56430-134">Geben Sie einen **Namen** für die APP an (z. b. **Blazor Aad-Client**).</span><span class="sxs-lookup"><span data-stu-id="56430-134">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="56430-135">Wählen Sie einen **unterstützten Kontotyp**aus.</span><span class="sxs-lookup"><span data-stu-id="56430-135">Choose a **Supported account types**.</span></span> <span data-ttu-id="56430-136">Für diese Vorgehens Art können Sie **nur Konten in diesem Organisations Verzeichnis** auswählen (einzelner Mandant).</span><span class="sxs-lookup"><span data-stu-id="56430-136">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="56430-137">Lassen Sie die Dropdown-Einstellung für **Umleitungs-URI** auf **Web**und einen Umleitungs-URI `https://localhost:5001/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="56430-137">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="56430-138">Deaktivieren Sie das Kontrollkästchen **Berechtigungen** > **erteilen Sie Administrator Berechtigungen für OpenID und offline_access Berechtigungen** .</span><span class="sxs-lookup"><span data-stu-id="56430-138">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="56430-139">Wählen Sie **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="56430-139">Select **Register**.</span></span>

<span data-ttu-id="56430-140">In der **Authentifizierung** > **Platt Form Konfigurationen** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="56430-140">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="56430-141">Vergewissern Sie sich, dass der **Umleitungs-URI** `https://localhost:5001/authentication/login-callback` vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="56430-141">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="56430-142">Aktivieren Sie für **implizite Gewährung**die Kontrollkästchen für **Zugriffs Token** und **ID-Token**.</span><span class="sxs-lookup"><span data-stu-id="56430-142">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="56430-143">Die verbleibenden Standardwerte für die APP sind für diese Art von akzeptabel.</span><span class="sxs-lookup"><span data-stu-id="56430-143">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="56430-144">Wählen Sie die Schaltfläche **Speichern** aus.</span><span class="sxs-lookup"><span data-stu-id="56430-144">Select the **Save** button.</span></span>

<span data-ttu-id="56430-145">In **API-Berechtigungen**:</span><span class="sxs-lookup"><span data-stu-id="56430-145">In **API permissions**:</span></span>

1. <span data-ttu-id="56430-146">Vergewissern Sie sich, dass die APP über **Microsoft Graph** > **User. Read** -Berechtigung verfügt.</span><span class="sxs-lookup"><span data-stu-id="56430-146">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="56430-147">Wählen Sie **Berechtigung hinzufügen** und dann **meine APIs**aus.</span><span class="sxs-lookup"><span data-stu-id="56430-147">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="56430-148">Wählen Sie die *Server-API-APP* aus der Spalte **Name** (z. b. **Blazor Server Aad**) aus.</span><span class="sxs-lookup"><span data-stu-id="56430-148">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="56430-149">Öffnen Sie die **API** -Liste.</span><span class="sxs-lookup"><span data-stu-id="56430-149">Open the **API** list.</span></span>
1. <span data-ttu-id="56430-150">Aktivieren Sie den Zugriff auf die API (z. b. `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="56430-150">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="56430-151">Wählen Sie **Berechtigungen hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="56430-151">Select **Add permissions**.</span></span>
1. <span data-ttu-id="56430-152">Wählen Sie die Schaltfläche **Administrator Inhalt für {TENANT Name} erteilen aus** .</span><span class="sxs-lookup"><span data-stu-id="56430-152">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="56430-153">Klicken Sie auf **Ja**, um zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="56430-153">Select **Yes** to confirm.</span></span>

<span data-ttu-id="56430-154">Notieren Sie die Anwendungs-ID der *Client* Anwendung (Client-ID) (z. b. `33333333-3333-3333-3333-333333333333`).</span><span class="sxs-lookup"><span data-stu-id="56430-154">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="56430-155">Erstellen der App</span><span class="sxs-lookup"><span data-stu-id="56430-155">Create the app</span></span>

<span data-ttu-id="56430-156">Ersetzen Sie die Platzhalter im folgenden Befehl durch die zuvor aufgezeichneten Informationen, und führen Sie den Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="56430-156">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP CLIENT ID}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="56430-157">Um den Ausgabe Speicherort anzugeben, der einen Projektordner erstellt, wenn er nicht vorhanden ist, fügen Sie die Output-Option im Befehl mit einem Pfad (z. b. `-o BlazorSample`) ein.</span><span class="sxs-lookup"><span data-stu-id="56430-157">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="56430-158">Der Name des Ordners wird auch Teil des Projekt namens.</span><span class="sxs-lookup"><span data-stu-id="56430-158">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="56430-159">Eine wichtige Konfigurationsänderung am Standard zugriffstokenbereich finden Sie im Abschnitt [Unterstützung des Authentifizierungs Diensts](#Authentication service support) .</span><span class="sxs-lookup"><span data-stu-id="56430-159">See the [Authentication service support](#Authentication service support) section for an important configuration change to the default access token scope.</span></span> <span data-ttu-id="56430-160">Der von der Vorlage Blazor Webassembly bereitgestellte Wert muss nach dem Erstellen der *Client-App* aus der Vorlage manuell geändert werden.</span><span class="sxs-lookup"><span data-stu-id="56430-160">The value provided by the Blazor WebAssembly template must be manually changed after the *Client app* is created from the template.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="56430-161">Server-App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="56430-161">Server app configuration</span></span>

<span data-ttu-id="56430-162">*Dieser Abschnitt bezieht sich auf die **Server** -App der Lösung.*</span><span class="sxs-lookup"><span data-stu-id="56430-162">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="56430-163">Authentifizierungs Paket</span><span class="sxs-lookup"><span data-stu-id="56430-163">Authentication package</span></span>

<span data-ttu-id="56430-164">Die Unterstützung für die Authentifizierung und Autorisierung von Aufrufen von ASP.net Core Web-APIs wird vom `Microsoft.AspNetCore.Authentication.AzureAD.UI`bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="56430-164">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="56430-165">Unterstützung für Authentifizierungsdienst</span><span class="sxs-lookup"><span data-stu-id="56430-165">Authentication service support</span></span>

<span data-ttu-id="56430-166">Die `AddAuthentication`-Methode richtet Authentifizierungsdienste innerhalb der APP ein und konfiguriert den JWT-bearerhandler als Standard Authentifizierungsmethode.</span><span class="sxs-lookup"><span data-stu-id="56430-166">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="56430-167">Die `AddAzureADBearer`-Methode richtet die spezifischen Parameter im JWT-bearerhandler ein, der zum Überprüfen der vom Azure Active Directory ausgegebenen Token erforderlich ist:</span><span class="sxs-lookup"><span data-stu-id="56430-167">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="56430-168">`UseAuthentication` und `UseAuthorization` Folgendes sicherstellen:</span><span class="sxs-lookup"><span data-stu-id="56430-168">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="56430-169">Die APP versucht, Token bei eingehenden Anforderungen zu analysieren und zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="56430-169">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="56430-170">Jede Anforderung, die versucht, auf eine geschützte Ressource ohne korrekte Anmelde Informationen zuzugreifen, schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="56430-170">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="app-settings"></a><span data-ttu-id="56430-171">App-Einstellungen</span><span class="sxs-lookup"><span data-stu-id="56430-171">App settings</span></span>

<span data-ttu-id="56430-172">Die Datei *appSettings. JSON* enthält die Optionen zum Konfigurieren des JWT-bearerhandlers, der zum Überprüfen von Zugriffs Token verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="56430-172">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="56430-173">Weatherforecast-Controller</span><span class="sxs-lookup"><span data-stu-id="56430-173">WeatherForecast controller</span></span>

<span data-ttu-id="56430-174">Der weatherforecast-Controller (*Controllers/weatherforecastcontroller. cs*) macht eine geschützte API verfügbar, wobei das `[Authorize]`-Attribut auf den Controller angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="56430-174">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="56430-175">Es ist **wichtig** , Folgendes zu verstehen:</span><span class="sxs-lookup"><span data-stu-id="56430-175">It's **important** to understand that:</span></span>

* <span data-ttu-id="56430-176">Das `[Authorize]`-Attribut in diesem API-Controller ist die einzige Möglichkeit, diese API vor nicht autorisiertem Zugriff zu schützen.</span><span class="sxs-lookup"><span data-stu-id="56430-176">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="56430-177">Das `[Authorize]`-Attribut, das in der Blazor Webassembly-App verwendet wird, dient nur als Hinweis für die APP, dass der Benutzer autorisiert werden muss, damit die APP ordnungsgemäß funktioniert.</span><span class="sxs-lookup"><span data-stu-id="56430-177">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="56430-178">Client-App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="56430-178">Client app configuration</span></span>

<span data-ttu-id="56430-179">*Dieser Abschnitt bezieht sich auf die **Client** -App der Lösung.*</span><span class="sxs-lookup"><span data-stu-id="56430-179">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="56430-180">Authentifizierungs Paket</span><span class="sxs-lookup"><span data-stu-id="56430-180">Authentication package</span></span>

<span data-ttu-id="56430-181">Wenn eine APP für die Verwendung von Geschäfts-, Schul-oder unikonten (`SingleOrg`) erstellt wird, erhält die APP automatisch einen Paket Verweis für die [Microsoft-Authentifizierungs Bibliothek](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="56430-181">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="56430-182">Das Paket enthält eine Reihe von primitiven, die der APP helfen, Benutzer zu authentifizieren und Token abzurufen, um geschützte APIs aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="56430-182">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="56430-183">Wenn Sie eine Authentifizierung zu einer APP hinzufügen, fügen Sie das Paket manuell zur Projektdatei der APP hinzu:</span><span class="sxs-lookup"><span data-stu-id="56430-183">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="56430-184">Ersetzen Sie `{VERSION}` im vorangehenden Paket Verweis durch die Version des `Microsoft.AspNetCore.Blazor.Templates` Pakets, das im <xref:blazor/get-started> Artikel angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="56430-184">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="56430-185">Das `Microsoft.Authentication.WebAssembly.Msal` Paket fügt das `Microsoft.AspNetCore.Components.WebAssembly.Authentication` Paket transitiv der APP hinzu.</span><span class="sxs-lookup"><span data-stu-id="56430-185">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="56430-186">Unterstützung für Authentifizierungsdienst</span><span class="sxs-lookup"><span data-stu-id="56430-186">Authentication service support</span></span>

<span data-ttu-id="56430-187">Die Unterstützung für das Authentifizieren von Benutzern wird im Dienst Container mit der `AddMsalAuthentication`-Erweiterungsmethode registriert, die vom `Microsoft.Authentication.WebAssembly.Msal`-Paket bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="56430-187">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="56430-188">Diese Methode richtet alle Dienste ein, die erforderlich sind, damit die APP mit dem Identitäts Anbieter (IP) interagieren muss.</span><span class="sxs-lookup"><span data-stu-id="56430-188">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="56430-189">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="56430-189">*Program.cs*:</span></span>

<span data-ttu-id="56430-190">Wenn die *Client-App* generiert wird, hat der standardzugriffstokenbereich das Format `api://{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}`.</span><span class="sxs-lookup"><span data-stu-id="56430-190">When the *Client app* is generated, the default access token scope is of the format `api://{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}`.</span></span> <span data-ttu-id="56430-191">**Entfernen Sie den `api://` Teil des Bereichs Werts.**</span><span class="sxs-lookup"><span data-stu-id="56430-191">**Remove the `api://` portion of the scope value.**</span></span> <span data-ttu-id="56430-192">Dieses Problem wird in einer zukünftigen Vorschauversion behandelt.</span><span class="sxs-lookup"><span data-stu-id="56430-192">This issue will be addressed in a future preview release.</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}");
});
```

> [!NOTE]
> <span data-ttu-id="56430-193">Der standardzugriffstokenbereich muss im Format `{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}` sein (z. b. `11111111-1111-1111-1111-111111111111/API.Access`).</span><span class="sxs-lookup"><span data-stu-id="56430-193">The default access token scope must be in the format `{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}` (for example, `11111111-1111-1111-1111-111111111111/API.Access`).</span></span> <span data-ttu-id="56430-194">Wenn für die Bereichseinstellung ein Schema oder ein Schema und ein Host bereitgestellt wird (wie im Azure-Portal gezeigt), löst die *Client-App* eine nicht behandelte Ausnahme aus, wenn Sie eine 401-nicht *autorisierte* Antwort von der Server- *API-APP*empfängt.</span><span class="sxs-lookup"><span data-stu-id="56430-194">If a scheme or scheme and host is provided to the scope setting (as shown in the Azure Portal), the *Client app* throws an unhandled exception when it receives a *401 Unauthorized* response from the *Server API app*.</span></span>

<span data-ttu-id="56430-195">Die `AddMsalAuthentication`-Methode akzeptiert einen Rückruf, um die Parameter zu konfigurieren, die zum Authentifizieren einer APP erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="56430-195">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="56430-196">Die für die Konfiguration der APP erforderlichen Werte können aus der Aad-Konfiguration des Azure-Portals abgerufen werden, wenn Sie die APP registrieren.</span><span class="sxs-lookup"><span data-stu-id="56430-196">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="56430-197">Die standardzugriffstokenbereiche stellen die Liste der zugriffstokenbereiche dar, die lauten:</span><span class="sxs-lookup"><span data-stu-id="56430-197">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="56430-198">Standardmäßig in der Anmelde Anforderung enthalten.</span><span class="sxs-lookup"><span data-stu-id="56430-198">Included by default in the sign in request.</span></span>
* <span data-ttu-id="56430-199">Wird verwendet, um unmittelbar nach der Authentifizierung ein Zugriffs Token bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="56430-199">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="56430-200">Alle Bereiche müssen pro Azure Active Directory Regeln zur gleichen App gehören.</span><span class="sxs-lookup"><span data-stu-id="56430-200">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="56430-201">Für zusätzliche API-Apps können nach Bedarf weitere Bereiche hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="56430-201">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{SERVER API APP CLIENT ID}/{SCOPE}");
});
```

### <a name="index-page"></a><span data-ttu-id="56430-202">Indexseite</span><span class="sxs-lookup"><span data-stu-id="56430-202">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="56430-203">App-Komponente</span><span class="sxs-lookup"><span data-stu-id="56430-203">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="56430-204">Redirecttologin-Komponente</span><span class="sxs-lookup"><span data-stu-id="56430-204">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="56430-205">Logindisplay-Komponente</span><span class="sxs-lookup"><span data-stu-id="56430-205">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="56430-206">Authentifizierungs Komponente</span><span class="sxs-lookup"><span data-stu-id="56430-206">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="56430-207">Fetchdata-Komponente</span><span class="sxs-lookup"><span data-stu-id="56430-207">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="56430-208">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="56430-208">Run the app</span></span>

<span data-ttu-id="56430-209">Führen Sie die APP aus dem Server Projekt aus.</span><span class="sxs-lookup"><span data-stu-id="56430-209">Run the app from the Server project.</span></span> <span data-ttu-id="56430-210">Wenn Sie Visual Studio verwenden, wählen Sie das Server Projekt in **Projektmappen-Explorer** aus, und klicken Sie auf der Symbolleiste auf die Schaltfläche **Ausführen** , oder starten Sie die APP im Menü **Debuggen**</span><span class="sxs-lookup"><span data-stu-id="56430-210">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="56430-211">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="56430-211">Additional resources</span></span>

* <xref:security/authentication/azure-active-directory/index>

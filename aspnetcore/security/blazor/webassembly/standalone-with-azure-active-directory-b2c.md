---
title: Sichern einer ASP.net Core Blazor eigenständigen Webassembly-App mit Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 7d1031d3eac0e1d6790ca946809038127eb59a73
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111161"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="eb360-102">Sichern einer ASP.net Core Blazor eigenständigen Webassembly-App mit Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="eb360-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="eb360-103">Von [Javier calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="eb360-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="eb360-104">Die Anleitung in diesem Artikel gilt für ASP.net Core 3,2 Preview 4.</span><span class="sxs-lookup"><span data-stu-id="eb360-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="eb360-105">Dieses Thema wird aktualisiert, um Preview 5 am Freitag, dem 24. April, zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="eb360-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="eb360-106">So erstellen Sie Blazor eine eigenständige Webassembly-APP, die [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) für die Authentifizierung verwendet:</span><span class="sxs-lookup"><span data-stu-id="eb360-106">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

1. <span data-ttu-id="eb360-107">Befolgen Sie die Anweisungen in den folgenden Themen, um einen Mandanten zu erstellen und eine Web-App im Azure-Portal zu registrieren:</span><span class="sxs-lookup"><span data-stu-id="eb360-107">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

   * <span data-ttu-id="eb360-108">[Erstellen Sie einen Aad B2C](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; Mandanten notieren Sie die folgenden Informationen:</span><span class="sxs-lookup"><span data-stu-id="eb360-108">[Create an AAD B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; Record the following information:</span></span>

     <span data-ttu-id="eb360-109">1 \.</span><span class="sxs-lookup"><span data-stu-id="eb360-109">1\.</span></span> <span data-ttu-id="eb360-110">Aad B2C Instanz (z. b `https://contoso.b2clogin.com/`., einschließlich des nachgestellten Schrägstrichs)</span><span class="sxs-lookup"><span data-stu-id="eb360-110">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span><br>
     <span data-ttu-id="eb360-111">2 \.</span><span class="sxs-lookup"><span data-stu-id="eb360-111">2\.</span></span> <span data-ttu-id="eb360-112">Aad B2C Mandanten Domäne (z. b `contoso.onmicrosoft.com`.)</span><span class="sxs-lookup"><span data-stu-id="eb360-112">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

   * <span data-ttu-id="eb360-113">[Registrieren einer Webanwendung](/azure/active-directory-b2c/tutorial-register-applications) &ndash; nehmen Sie bei der APP-Registrierung folgende Auswahl vor:</span><span class="sxs-lookup"><span data-stu-id="eb360-113">[Register a web application](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Make the following selections during app registration:</span></span>

     <span data-ttu-id="eb360-114">1 \.</span><span class="sxs-lookup"><span data-stu-id="eb360-114">1\.</span></span> <span data-ttu-id="eb360-115">Legen Sie **Web-App/Web-API** auf **Ja**fest.</span><span class="sxs-lookup"><span data-stu-id="eb360-115">Set **Web App / Web API** to **Yes**.</span></span><br>
     <span data-ttu-id="eb360-116">2 \.</span><span class="sxs-lookup"><span data-stu-id="eb360-116">2\.</span></span> <span data-ttu-id="eb360-117">Legen Sie **implizites Flow zulassen** auf **Ja**fest.</span><span class="sxs-lookup"><span data-stu-id="eb360-117">Set **Allow implicit flow** to **Yes**.</span></span><br>
     <span data-ttu-id="eb360-118">3 \.</span><span class="sxs-lookup"><span data-stu-id="eb360-118">3\.</span></span> <span data-ttu-id="eb360-119">Fügen Sie eine **Antwort** - `https://localhost:5001/authentication/login-callback`URL hinzu.</span><span class="sxs-lookup"><span data-stu-id="eb360-119">Add a **Reply URL** of `https://localhost:5001/authentication/login-callback`.</span></span>

     <span data-ttu-id="eb360-120">Notieren Sie sich `11111111-1111-1111-1111-111111111111`die Anwendungs-ID (Client-ID) (z. b.).</span><span class="sxs-lookup"><span data-stu-id="eb360-120">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

   * <span data-ttu-id="eb360-121">[Benutzerflows](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; erstellen erstellen einen Registrierungs-und Anmelde Benutzer Fluss.</span><span class="sxs-lookup"><span data-stu-id="eb360-121">[Create user flows](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; Create a sign-up and sign-in user flow.</span></span>

     <span data-ttu-id="eb360-122">Wählen Sie mindestens das Benutzer Attribut **Anwendungs Anspruchs** > **Anzeige Name** aus, um das `context.User.Identity.Name` in der `LoginDisplay` Komponente (*Shared/logindisplay. Razor*) aufzufüllen.</span><span class="sxs-lookup"><span data-stu-id="eb360-122">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

     <span data-ttu-id="eb360-123">Zeichnen Sie den für die APP erstellten Registrierungs-und Anmeldedaten Flussnamen auf (z. b. `B2C_1_signupsignin`).</span><span class="sxs-lookup"><span data-stu-id="eb360-123">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

1. <span data-ttu-id="eb360-124">Ersetzen Sie die Platzhalter im folgenden Befehl durch die zuvor aufgezeichneten Informationen, und führen Sie den Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="eb360-124">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   <span data-ttu-id="eb360-125">Um den Ausgabe Speicherort anzugeben, der einen Projektordner erstellt, wenn er nicht vorhanden ist, schließen Sie die Output-Option in den Befehl mit einem `-o BlazorSample`Pfad (z. b.) ein.</span><span class="sxs-lookup"><span data-stu-id="eb360-125">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="eb360-126">Der Name des Ordners wird auch Teil des Projekt namens.</span><span class="sxs-lookup"><span data-stu-id="eb360-126">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="eb360-127">Authentifizierungs Paket</span><span class="sxs-lookup"><span data-stu-id="eb360-127">Authentication package</span></span>

<span data-ttu-id="eb360-128">Wenn eine APP für die Verwendung eines einzelnen B2C-Kontos (`IndividualB2C`) erstellt wird, erhält die APP automatisch einen Paket Verweis für die Microsoft-`Microsoft.Authentication.WebAssembly.Msal` [Authentifizierungs Bibliothek](/azure/active-directory/develop/msal-overview) ().</span><span class="sxs-lookup"><span data-stu-id="eb360-128">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="eb360-129">Das Paket enthält eine Reihe von primitiven, die der APP helfen, Benutzer zu authentifizieren und Token abzurufen, um geschützte APIs aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="eb360-129">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="eb360-130">Wenn Sie eine Authentifizierung zu einer APP hinzufügen, fügen Sie das Paket manuell zur Projektdatei der APP hinzu:</span><span class="sxs-lookup"><span data-stu-id="eb360-130">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="eb360-131">Ersetzen `{VERSION}` Sie im vorherigen Paket Verweis durch die Version des `Microsoft.AspNetCore.Blazor.Templates` Pakets, das im <xref:blazor/get-started> Artikel angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="eb360-131">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="eb360-132">Das `Microsoft.Authentication.WebAssembly.Msal` Paket fügt das `Microsoft.AspNetCore.Components.WebAssembly.Authentication` Paket transitiv der APP hinzu.</span><span class="sxs-lookup"><span data-stu-id="eb360-132">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="eb360-133">Unterstützung für Authentifizierungsdienst</span><span class="sxs-lookup"><span data-stu-id="eb360-133">Authentication service support</span></span>

<span data-ttu-id="eb360-134">Die Unterstützung für das Authentifizieren von Benutzern wird im Dienst Container `AddMsalAuthentication` mit der vom `Microsoft.Authentication.WebAssembly.Msal` Paket bereitgestellten Erweiterungsmethode registriert.</span><span class="sxs-lookup"><span data-stu-id="eb360-134">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="eb360-135">Diese Methode richtet alle Dienste ein, die erforderlich sind, damit die APP mit dem Identitäts Anbieter (IP) interagieren muss.</span><span class="sxs-lookup"><span data-stu-id="eb360-135">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="eb360-136">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="eb360-136">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
});
```

<span data-ttu-id="eb360-137">Die `AddMsalAuthentication` -Methode akzeptiert einen Rückruf, um die Parameter zu konfigurieren, die zum Authentifizieren einer APP erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="eb360-137">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="eb360-138">Die für die Konfiguration der APP erforderlichen Werte können aus der Aad-Konfiguration des Azure-Portals abgerufen werden, wenn Sie die APP registrieren.</span><span class="sxs-lookup"><span data-stu-id="eb360-138">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="eb360-139">Zugriffs Token-Bereiche</span><span class="sxs-lookup"><span data-stu-id="eb360-139">Access token scopes</span></span>

<span data-ttu-id="eb360-140">Die Blazor webassemblyvorlage konfiguriert die APP nicht automatisch, um ein Zugriffs Token für eine sichere API anzufordern.</span><span class="sxs-lookup"><span data-stu-id="eb360-140">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="eb360-141">Fügen Sie zum Bereitstellen eines Tokens als Teil des Anmelde Flusses den Gültigkeitsbereich zu den standardmäßigen zugriffstokenbereichen von hinzu `MsalProviderOptions`:</span><span class="sxs-lookup"><span data-stu-id="eb360-141">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="eb360-142">Wenn die Azure-Portal einen Bereichs-URI bereitstellt und **die APP eine nicht behandelte Ausnahme** auslöst, wenn Sie eine 401-nicht *autorisierte* Antwort von der API empfängt, verwenden Sie einen Bereichs-URI, der das Schema und den Host nicht enthält.</span><span class="sxs-lookup"><span data-stu-id="eb360-142">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="eb360-143">Der Azure-Portal kann z. b. eines der folgenden Bereich-URI-Formate bereitstellen:</span><span class="sxs-lookup"><span data-stu-id="eb360-143">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="eb360-144">Geben Sie den Bereichs-URI ohne das Schema und den Host an:</span><span class="sxs-lookup"><span data-stu-id="eb360-144">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="eb360-145">Weitere Informationen finden Sie unter <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="eb360-145">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

## <a name="imports-file"></a><span data-ttu-id="eb360-146">Import Datei</span><span class="sxs-lookup"><span data-stu-id="eb360-146">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="eb360-147">Indexseite</span><span class="sxs-lookup"><span data-stu-id="eb360-147">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="eb360-148">App-Komponente</span><span class="sxs-lookup"><span data-stu-id="eb360-148">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="eb360-149">Redirecttologin-Komponente</span><span class="sxs-lookup"><span data-stu-id="eb360-149">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="eb360-150">Logindisplay-Komponente</span><span class="sxs-lookup"><span data-stu-id="eb360-150">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="eb360-151">Authentifizierungs Komponente</span><span class="sxs-lookup"><span data-stu-id="eb360-151">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="eb360-152">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="eb360-152">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="eb360-153">Tutorial zum Erstellen eines Azure Active Directory B2C-Mandanten</span><span class="sxs-lookup"><span data-stu-id="eb360-153">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="eb360-154">Dokumentation zur Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="eb360-154">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)

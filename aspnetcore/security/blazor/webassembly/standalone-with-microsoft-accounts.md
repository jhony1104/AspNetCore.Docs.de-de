---
title: Sichern einer ASP.net Core Blazor eigenständigen Webassembly-App mit Microsoft-Konten
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: a12cc8f94a97882e4a0ac3a6553628df4da2e82c
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111187"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="f5338-102">Sichern einer ASP.net Core Blazor eigenständigen Webassembly-App mit Microsoft-Konten</span><span class="sxs-lookup"><span data-stu-id="f5338-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="f5338-103">Von [Javier calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f5338-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="f5338-104">Die Anleitung in diesem Artikel gilt für ASP.net Core 3,2 Preview 4.</span><span class="sxs-lookup"><span data-stu-id="f5338-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="f5338-105">Dieses Thema wird aktualisiert, um Preview 5 am Freitag, dem 24. April, zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="f5338-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="f5338-106">So erstellen Sie Blazor eine eigenständige Webassembly-APP, die [Microsoft-Konten mit Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) für die Authentifizierung verwendet:</span><span class="sxs-lookup"><span data-stu-id="f5338-106">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

1. [<span data-ttu-id="f5338-107">Erstellen eines Aad-Mandanten und einer Webanwendung</span><span class="sxs-lookup"><span data-stu-id="f5338-107">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

   <span data-ttu-id="f5338-108">Registrieren Sie eine Aad-App im **Azure Active Directory** > **App-Registrierungen** Bereich der Azure-Portal:</span><span class="sxs-lookup"><span data-stu-id="f5338-108">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

   <span data-ttu-id="f5338-109">1 \.</span><span class="sxs-lookup"><span data-stu-id="f5338-109">1\.</span></span> <span data-ttu-id="f5338-110">Geben Sie einen **Namen** für die APP an (z. b. \*\* Blazor Client Aad\*\*).</span><span class="sxs-lookup"><span data-stu-id="f5338-110">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span><br>
   <span data-ttu-id="f5338-111">2 \.</span><span class="sxs-lookup"><span data-stu-id="f5338-111">2\.</span></span> <span data-ttu-id="f5338-112">Wählen Sie **unter Unterstützte Konto Typen**die Option **Konten in einem beliebigen Organisations Verzeichnis**aus.</span><span class="sxs-lookup"><span data-stu-id="f5338-112">In **Supported account types**, select **Accounts in any organizational directory**.</span></span><br>
   <span data-ttu-id="f5338-113">3 \.</span><span class="sxs-lookup"><span data-stu-id="f5338-113">3\.</span></span> <span data-ttu-id="f5338-114">Belassen Sie den **Umleitungs-URI** -Dropdown auf **Web**, und geben Sie `https://localhost:5001/authentication/login-callback`einen Umleitungs-URI von an.</span><span class="sxs-lookup"><span data-stu-id="f5338-114">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span><br>
   <span data-ttu-id="f5338-115">4 \.</span><span class="sxs-lookup"><span data-stu-id="f5338-115">4\.</span></span> <span data-ttu-id="f5338-116">Deaktivieren Sie **das** > Kontrollkästchen**Administrator Berechtigungen für OpenID und offline_access Berechtigungen gewähren** .</span><span class="sxs-lookup"><span data-stu-id="f5338-116">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span><br>
   <span data-ttu-id="f5338-117">5 \.</span><span class="sxs-lookup"><span data-stu-id="f5338-117">5\.</span></span> <span data-ttu-id="f5338-118">Wählen Sie **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="f5338-118">Select **Register**.</span></span>

   <span data-ttu-id="f5338-119">Im**Web**der **Authentifizierungs** > **Platt Form Konfigurationen** > :</span><span class="sxs-lookup"><span data-stu-id="f5338-119">In **Authentication** > **Platform configurations** > **Web**:</span></span>

   <span data-ttu-id="f5338-120">1 \.</span><span class="sxs-lookup"><span data-stu-id="f5338-120">1\.</span></span> <span data-ttu-id="f5338-121">Bestätigen Sie, dass der `https://localhost:5001/authentication/login-callback` **Umleitungs-URI** von vorhanden ist</span><span class="sxs-lookup"><span data-stu-id="f5338-121">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span><br>
   <span data-ttu-id="f5338-122">2 \.</span><span class="sxs-lookup"><span data-stu-id="f5338-122">2\.</span></span> <span data-ttu-id="f5338-123">Aktivieren Sie für **implizite Gewährung**die Kontrollkästchen für **Zugriffs Token** und **ID-Token**.</span><span class="sxs-lookup"><span data-stu-id="f5338-123">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span><br>
   <span data-ttu-id="f5338-124">3 \.</span><span class="sxs-lookup"><span data-stu-id="f5338-124">3\.</span></span> <span data-ttu-id="f5338-125">Die verbleibenden Standardwerte für die APP sind für diese Art von akzeptabel.</span><span class="sxs-lookup"><span data-stu-id="f5338-125">The remaining defaults for the app are acceptable for this experience.</span></span><br>
   <span data-ttu-id="f5338-126">4 \.</span><span class="sxs-lookup"><span data-stu-id="f5338-126">4\.</span></span> <span data-ttu-id="f5338-127">Wählen Sie die Schaltfläche **Speichern** aus.</span><span class="sxs-lookup"><span data-stu-id="f5338-127">Select the **Save** button.</span></span>

   <span data-ttu-id="f5338-128">Notieren Sie sich `11111111-1111-1111-1111-111111111111`die Anwendungs-ID (Client-ID) (z. b.).</span><span class="sxs-lookup"><span data-stu-id="f5338-128">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

1. <span data-ttu-id="f5338-129">Ersetzen Sie die Platzhalter im folgenden Befehl durch die zuvor aufgezeichneten Informationen, und führen Sie den Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="f5338-129">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   <span data-ttu-id="f5338-130">Um den Ausgabe Speicherort anzugeben, der einen Projektordner erstellt, wenn er nicht vorhanden ist, schließen Sie die Output-Option in den Befehl mit einem `-o BlazorSample`Pfad (z. b.) ein.</span><span class="sxs-lookup"><span data-stu-id="f5338-130">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="f5338-131">Der Name des Ordners wird auch Teil des Projekt namens.</span><span class="sxs-lookup"><span data-stu-id="f5338-131">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="f5338-132">Nachdem Sie die App erstellt haben, sollten Sie folgende Aktionen ausführen können:</span><span class="sxs-lookup"><span data-stu-id="f5338-132">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="f5338-133">Melden Sie sich mit einem Microsoft-Konto bei der APP an.</span><span class="sxs-lookup"><span data-stu-id="f5338-133">Log into the app using a Microsoft Account.</span></span>
* <span data-ttu-id="f5338-134">Anfordern von Zugriffs Token für Microsoft-APIs mit dem gleichen Ansatz wie Blazor für eigenständige apps, vorausgesetzt, dass Sie die APP ordnungsgemäß konfiguriert haben.</span><span class="sxs-lookup"><span data-stu-id="f5338-134">Request access tokens for Microsoft APIs using the same approach as for standalone Blazor apps provided that you have configured the app correctly.</span></span> <span data-ttu-id="f5338-135">Weitere Informationen finden Sie unter [Schnellstart: Konfigurieren einer Anwendung für die](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)Bereitstellung von Web-APIs.</span><span class="sxs-lookup"><span data-stu-id="f5338-135">For more information, see [Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="f5338-136">Authentifizierungs Paket</span><span class="sxs-lookup"><span data-stu-id="f5338-136">Authentication package</span></span>

<span data-ttu-id="f5338-137">Wenn eine APP für die Verwendung von Geschäfts-, Schul-`SingleOrg`oder unikonten () erstellt wird, erhält die APP automatisch einen Paket Verweis`Microsoft.Authentication.WebAssembly.Msal`für die [Microsoft-Authentifizierungs Bibliothek](/azure/active-directory/develop/msal-overview) ().</span><span class="sxs-lookup"><span data-stu-id="f5338-137">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="f5338-138">Das Paket enthält eine Reihe von primitiven, die der APP helfen, Benutzer zu authentifizieren und Token abzurufen, um geschützte APIs aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="f5338-138">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="f5338-139">Wenn Sie eine Authentifizierung zu einer APP hinzufügen, fügen Sie das Paket manuell zur Projektdatei der APP hinzu:</span><span class="sxs-lookup"><span data-stu-id="f5338-139">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="f5338-140">Ersetzen `{VERSION}` Sie im vorherigen Paket Verweis durch die Version des `Microsoft.AspNetCore.Blazor.Templates` Pakets, das im <xref:blazor/get-started> Artikel angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="f5338-140">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="f5338-141">Das `Microsoft.Authentication.WebAssembly.Msal` Paket fügt das `Microsoft.AspNetCore.Components.WebAssembly.Authentication` Paket transitiv der APP hinzu.</span><span class="sxs-lookup"><span data-stu-id="f5338-141">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="f5338-142">Unterstützung für Authentifizierungsdienst</span><span class="sxs-lookup"><span data-stu-id="f5338-142">Authentication service support</span></span>

<span data-ttu-id="f5338-143">Die Unterstützung für das Authentifizieren von Benutzern wird im Dienst Container `AddMsalAuthentication` mit der vom `Microsoft.Authentication.WebAssembly.Msal` Paket bereitgestellten Erweiterungsmethode registriert.</span><span class="sxs-lookup"><span data-stu-id="f5338-143">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="f5338-144">Diese Methode richtet alle Dienste ein, die erforderlich sind, damit die APP mit dem Identitäts Anbieter (IP) interagieren muss.</span><span class="sxs-lookup"><span data-stu-id="f5338-144">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="f5338-145">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="f5338-145">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "{AUTHORITY}";
    authentication.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="f5338-146">Die `AddMsalAuthentication` -Methode akzeptiert einen Rückruf, um die Parameter zu konfigurieren, die zum Authentifizieren einer APP erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="f5338-146">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="f5338-147">Die für die Konfiguration der APP erforderlichen Werte können bei der Registrierung der APP aus der Microsoft-Konto Konfiguration abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="f5338-147">The values required for configuring the app can be obtained from the Microsoft Accounts configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="f5338-148">Zugriffs Token-Bereiche</span><span class="sxs-lookup"><span data-stu-id="f5338-148">Access token scopes</span></span>

<span data-ttu-id="f5338-149">Die Blazor webassemblyvorlage konfiguriert die APP nicht automatisch, um ein Zugriffs Token für eine sichere API anzufordern.</span><span class="sxs-lookup"><span data-stu-id="f5338-149">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="f5338-150">Fügen Sie zum Bereitstellen eines Tokens als Teil des Anmelde Flusses den Gültigkeitsbereich zu den standardmäßigen zugriffstokenbereichen von hinzu `MsalProviderOptions`:</span><span class="sxs-lookup"><span data-stu-id="f5338-150">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="f5338-151">Wenn die Azure-Portal einen Bereichs-URI bereitstellt und **die APP eine nicht behandelte Ausnahme** auslöst, wenn Sie eine 401-nicht *autorisierte* Antwort von der API empfängt, verwenden Sie einen Bereichs-URI, der das Schema und den Host nicht enthält.</span><span class="sxs-lookup"><span data-stu-id="f5338-151">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="f5338-152">Der Azure-Portal kann z. b. eines der folgenden Bereich-URI-Formate bereitstellen:</span><span class="sxs-lookup"><span data-stu-id="f5338-152">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="f5338-153">Geben Sie den Bereichs-URI ohne das Schema und den Host an:</span><span class="sxs-lookup"><span data-stu-id="f5338-153">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="f5338-154">Weitere Informationen finden Sie unter <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="f5338-154">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

## <a name="imports-file"></a><span data-ttu-id="f5338-155">Import Datei</span><span class="sxs-lookup"><span data-stu-id="f5338-155">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="f5338-156">Indexseite</span><span class="sxs-lookup"><span data-stu-id="f5338-156">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="f5338-157">App-Komponente</span><span class="sxs-lookup"><span data-stu-id="f5338-157">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="f5338-158">Redirecttologin-Komponente</span><span class="sxs-lookup"><span data-stu-id="f5338-158">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="f5338-159">Logindisplay-Komponente</span><span class="sxs-lookup"><span data-stu-id="f5338-159">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="f5338-160">Authentifizierungs Komponente</span><span class="sxs-lookup"><span data-stu-id="f5338-160">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="f5338-161">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="f5338-161">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="f5338-162">Schnellstart: Registrieren einer Anwendung bei Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="f5338-162">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="f5338-163">Schnellstart: Konfigurieren einer Anwendung für die Bereitstellung von Web-APIs</span><span class="sxs-lookup"><span data-stu-id="f5338-163">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)

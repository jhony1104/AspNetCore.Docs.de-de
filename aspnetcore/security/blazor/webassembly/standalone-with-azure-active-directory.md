---
title: Sichern einer ASP.net Core Blazor eigenständigen Webassembly-App mit Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 71229f41f3f1021aa9ad02402af21de51d7eeee4
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111200"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="1acc3-102">Sichern einer ASP.net Core Blazor eigenständigen Webassembly-App mit Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="1acc3-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="1acc3-103">Von [Javier calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1acc3-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="1acc3-104">Die Anleitung in diesem Artikel gilt für ASP.net Core 3,2 Preview 4.</span><span class="sxs-lookup"><span data-stu-id="1acc3-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="1acc3-105">Dieses Thema wird aktualisiert, um Preview 5 am Freitag, dem 24. April, zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="1acc3-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="1acc3-106">So erstellen Sie Blazor eine eigenständige Webassembly-APP, die [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) für die Authentifizierung verwendet:</span><span class="sxs-lookup"><span data-stu-id="1acc3-106">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="1acc3-107">[Erstellen Sie einen Aad-Mandanten und eine Webanwendung](/azure/active-directory/develop/v2-overview):</span><span class="sxs-lookup"><span data-stu-id="1acc3-107">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="1acc3-108">Registrieren Sie eine Aad-App im **Azure Active Directory** > **App-Registrierungen** Bereich der Azure-Portal:</span><span class="sxs-lookup"><span data-stu-id="1acc3-108">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="1acc3-109">Geben Sie einen **Namen** für die APP an (z. b. \*\* Blazor Client Aad\*\*).</span><span class="sxs-lookup"><span data-stu-id="1acc3-109">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="1acc3-110">Wählen Sie einen **unterstützten Kontotyp**aus.</span><span class="sxs-lookup"><span data-stu-id="1acc3-110">Choose a **Supported account types**.</span></span> <span data-ttu-id="1acc3-111">Sie können **Konten in diesem Organisations Verzeichnis nur** für dieses Verfahren auswählen.</span><span class="sxs-lookup"><span data-stu-id="1acc3-111">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="1acc3-112">Belassen Sie den **Umleitungs-URI** -Dropdown auf **Web**, und geben Sie `https://localhost:5001/authentication/login-callback`einen Umleitungs-URI von an.</span><span class="sxs-lookup"><span data-stu-id="1acc3-112">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="1acc3-113">Deaktivieren Sie **das** > Kontrollkästchen**Administrator Berechtigungen für OpenID und offline_access Berechtigungen gewähren** .</span><span class="sxs-lookup"><span data-stu-id="1acc3-113">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="1acc3-114">Wählen Sie **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="1acc3-114">Select **Register**.</span></span>

<span data-ttu-id="1acc3-115">Im**Web**der **Authentifizierungs** > **Platt Form Konfigurationen** > :</span><span class="sxs-lookup"><span data-stu-id="1acc3-115">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="1acc3-116">Bestätigen Sie, dass der `https://localhost:5001/authentication/login-callback` **Umleitungs-URI** von vorhanden ist</span><span class="sxs-lookup"><span data-stu-id="1acc3-116">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="1acc3-117">Aktivieren Sie für **implizite Gewährung**die Kontrollkästchen für **Zugriffs Token** und **ID-Token**.</span><span class="sxs-lookup"><span data-stu-id="1acc3-117">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="1acc3-118">Die verbleibenden Standardwerte für die APP sind für diese Art von akzeptabel.</span><span class="sxs-lookup"><span data-stu-id="1acc3-118">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="1acc3-119">Wählen Sie die Schaltfläche **Speichern** aus.</span><span class="sxs-lookup"><span data-stu-id="1acc3-119">Select the **Save** button.</span></span>

<span data-ttu-id="1acc3-120">Notieren Sie sich die folgenden Informationen:</span><span class="sxs-lookup"><span data-stu-id="1acc3-120">Record the following information:</span></span>

* <span data-ttu-id="1acc3-121">Anwendungs-ID (Client-ID) (z `11111111-1111-1111-1111-111111111111`. b.)</span><span class="sxs-lookup"><span data-stu-id="1acc3-121">Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="1acc3-122">Verzeichnis-ID (Mandanten-ID) (z `22222222-2222-2222-2222-222222222222`. b.)</span><span class="sxs-lookup"><span data-stu-id="1acc3-122">Directory ID (Tenant ID) (for example, `22222222-2222-2222-2222-222222222222`)</span></span>

<span data-ttu-id="1acc3-123">Ersetzen Sie die Platzhalter im folgenden Befehl durch die zuvor aufgezeichneten Informationen, und führen Sie den Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="1acc3-123">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="1acc3-124">Um den Ausgabe Speicherort anzugeben, der einen Projektordner erstellt, wenn er nicht vorhanden ist, schließen Sie die Output-Option in den Befehl mit einem `-o BlazorSample`Pfad (z. b.) ein.</span><span class="sxs-lookup"><span data-stu-id="1acc3-124">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="1acc3-125">Der Name des Ordners wird auch Teil des Projekt namens.</span><span class="sxs-lookup"><span data-stu-id="1acc3-125">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="1acc3-126">Authentifizierungs Paket</span><span class="sxs-lookup"><span data-stu-id="1acc3-126">Authentication package</span></span>

<span data-ttu-id="1acc3-127">Wenn eine APP für die Verwendung von Geschäfts-, Schul-`SingleOrg`oder unikonten () erstellt wird, erhält die APP automatisch einen Paket Verweis`Microsoft.Authentication.WebAssembly.Msal`für die [Microsoft-Authentifizierungs Bibliothek](/azure/active-directory/develop/msal-overview) ().</span><span class="sxs-lookup"><span data-stu-id="1acc3-127">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="1acc3-128">Das Paket enthält eine Reihe von primitiven, die der APP helfen, Benutzer zu authentifizieren und Token abzurufen, um geschützte APIs aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="1acc3-128">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="1acc3-129">Wenn Sie eine Authentifizierung zu einer APP hinzufügen, fügen Sie das Paket manuell zur Projektdatei der APP hinzu:</span><span class="sxs-lookup"><span data-stu-id="1acc3-129">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="1acc3-130">Ersetzen `{VERSION}` Sie im vorherigen Paket Verweis durch die Version des `Microsoft.AspNetCore.Blazor.Templates` Pakets, das im <xref:blazor/get-started> Artikel angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="1acc3-130">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="1acc3-131">Das `Microsoft.Authentication.WebAssembly.Msal` Paket fügt das `Microsoft.AspNetCore.Components.WebAssembly.Authentication` Paket transitiv der APP hinzu.</span><span class="sxs-lookup"><span data-stu-id="1acc3-131">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="1acc3-132">Unterstützung für Authentifizierungsdienst</span><span class="sxs-lookup"><span data-stu-id="1acc3-132">Authentication service support</span></span>

<span data-ttu-id="1acc3-133">Die Unterstützung für das Authentifizieren von Benutzern wird im Dienst Container `AddMsalAuthentication` mit der vom `Microsoft.Authentication.WebAssembly.Msal` Paket bereitgestellten Erweiterungsmethode registriert.</span><span class="sxs-lookup"><span data-stu-id="1acc3-133">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="1acc3-134">Diese Methode richtet alle Dienste ein, die erforderlich sind, damit die APP mit dem Identitäts Anbieter (IP) interagieren muss.</span><span class="sxs-lookup"><span data-stu-id="1acc3-134">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="1acc3-135">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="1acc3-135">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="1acc3-136">Die `AddMsalAuthentication` -Methode akzeptiert einen Rückruf, um die Parameter zu konfigurieren, die zum Authentifizieren einer APP erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="1acc3-136">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="1acc3-137">Die für die Konfiguration der APP erforderlichen Werte können aus der Aad-Konfiguration des Azure-Portals abgerufen werden, wenn Sie die APP registrieren.</span><span class="sxs-lookup"><span data-stu-id="1acc3-137">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="1acc3-138">Zugriffs Token-Bereiche</span><span class="sxs-lookup"><span data-stu-id="1acc3-138">Access token scopes</span></span>

<span data-ttu-id="1acc3-139">Die Blazor webassemblyvorlage konfiguriert die APP nicht automatisch, um ein Zugriffs Token für eine sichere API anzufordern.</span><span class="sxs-lookup"><span data-stu-id="1acc3-139">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="1acc3-140">Fügen Sie zum Bereitstellen eines Tokens als Teil des Anmelde Flusses den Gültigkeitsbereich zu den standardmäßigen zugriffstokenbereichen von hinzu `MsalProviderOptions`:</span><span class="sxs-lookup"><span data-stu-id="1acc3-140">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="1acc3-141">Wenn die Azure-Portal einen Bereichs-URI bereitstellt und **die APP eine nicht behandelte Ausnahme** auslöst, wenn Sie eine 401-nicht *autorisierte* Antwort von der API empfängt, verwenden Sie einen Bereichs-URI, der das Schema und den Host nicht enthält.</span><span class="sxs-lookup"><span data-stu-id="1acc3-141">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="1acc3-142">Der Azure-Portal kann z. b. eines der folgenden Bereich-URI-Formate bereitstellen:</span><span class="sxs-lookup"><span data-stu-id="1acc3-142">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="1acc3-143">Geben Sie den Bereichs-URI ohne das Schema und den Host an:</span><span class="sxs-lookup"><span data-stu-id="1acc3-143">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="1acc3-144">Weitere Informationen finden Sie unter <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="1acc3-144">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

## <a name="imports-file"></a><span data-ttu-id="1acc3-145">Import Datei</span><span class="sxs-lookup"><span data-stu-id="1acc3-145">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="1acc3-146">Indexseite</span><span class="sxs-lookup"><span data-stu-id="1acc3-146">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="1acc3-147">App-Komponente</span><span class="sxs-lookup"><span data-stu-id="1acc3-147">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="1acc3-148">Redirecttologin-Komponente</span><span class="sxs-lookup"><span data-stu-id="1acc3-148">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="1acc3-149">Logindisplay-Komponente</span><span class="sxs-lookup"><span data-stu-id="1acc3-149">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="1acc3-150">Authentifizierungs Komponente</span><span class="sxs-lookup"><span data-stu-id="1acc3-150">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="1acc3-151">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="1acc3-151">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="1acc3-152">Dokumentation zur Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="1acc3-152">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)

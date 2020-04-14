---
title: Externe S-Anmeldung für Microsoft-Konten mit ASP.NET Core
author: rick-anderson
description: In diesem Beispiel wird die Integration der Benutzerauthentifizierung von Microsoft-Konten in eine vorhandene ASP.NET Core-App veranschaulicht.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 32315267e0672b0747917228f08591a15e4449f8
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277248"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a><span data-ttu-id="270ce-103">Externe S-Anmeldung für Microsoft-Konten mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="270ce-103">Microsoft Account external login setup with ASP.NET Core</span></span>

<span data-ttu-id="270ce-104">Von [Valeriy Novytskyy](https://github.com/01binary) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="270ce-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="270ce-105">In diesem Beispiel wird gezeigt, wie Benutzer sich mit ihrem Microsoft-Konto mit dem auf der [vorherigen Seite](xref:security/authentication/social/index)erstellten ASP.NET Core 3.0-Projekt anmelden können.</span><span class="sxs-lookup"><span data-stu-id="270ce-105">This sample shows you how to enable users to sign in with their Microsoft account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-microsoft-developer-portal"></a><span data-ttu-id="270ce-106">Erstellen der App im Microsoft Developer Portal</span><span class="sxs-lookup"><span data-stu-id="270ce-106">Create the app in Microsoft Developer Portal</span></span>

* <span data-ttu-id="270ce-107">Fügen Sie dem Projekt das [Paket Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet hinzu.</span><span class="sxs-lookup"><span data-stu-id="270ce-107">Add the [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet package to the project.</span></span>
* <span data-ttu-id="270ce-108">Navigieren Sie zum [Azure-Portal - App-Registrierungsseite,](https://go.microsoft.com/fwlink/?linkid=2083908) und erstellen oder melden Sie sich bei einem Microsoft-Konto an:</span><span class="sxs-lookup"><span data-stu-id="270ce-108">Navigate to the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page and create or sign into a Microsoft account:</span></span>

<span data-ttu-id="270ce-109">Wenn Sie kein Microsoft-Konto haben, wählen Sie **Erstellen eines**aus.</span><span class="sxs-lookup"><span data-stu-id="270ce-109">If you don't have a Microsoft account, select **Create one**.</span></span> <span data-ttu-id="270ce-110">Nach der Anmeldung werden Sie zur **App-Registrierungsseite** weitergeleitet:</span><span class="sxs-lookup"><span data-stu-id="270ce-110">After signing in, you are redirected to the **App registrations** page:</span></span>

* <span data-ttu-id="270ce-111">Neue **Registrierung auswählen**</span><span class="sxs-lookup"><span data-stu-id="270ce-111">Select **New registration**</span></span>
* <span data-ttu-id="270ce-112">Geben Sie einen **Namen**ein.</span><span class="sxs-lookup"><span data-stu-id="270ce-112">Enter a **Name**.</span></span>
* <span data-ttu-id="270ce-113">Wählen Sie eine Option für **Unterstützte Kontotypen**aus.</span><span class="sxs-lookup"><span data-stu-id="270ce-113">Select an option for **Supported account types**.</span></span>  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
* <span data-ttu-id="270ce-114">Geben Sie unter **Redirect**URI `/signin-microsoft` Ihre Entwicklungs-URL mit angehängten ein.</span><span class="sxs-lookup"><span data-stu-id="270ce-114">Under **Redirect URI**, enter your development URL with `/signin-microsoft` appended.</span></span> <span data-ttu-id="270ce-115">Beispiel: `https://localhost:5001/signin-microsoft`.</span><span class="sxs-lookup"><span data-stu-id="270ce-115">For example, `https://localhost:5001/signin-microsoft`.</span></span> <span data-ttu-id="270ce-116">Das später in diesem Beispiel konfigurierte Microsoft-Authentifizierungsschema verarbeitet automatisch Anforderungen auf `/signin-microsoft` der Route, um den OAuth-Fluss zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="270ce-116">The Microsoft authentication scheme configured later in this sample will automatically handle requests at `/signin-microsoft` route to implement the OAuth flow.</span></span>
* <span data-ttu-id="270ce-117">Wählen Sie **Registrieren** aus.</span><span class="sxs-lookup"><span data-stu-id="270ce-117">Select **Register**</span></span>

### <a name="create-client-secret"></a><span data-ttu-id="270ce-118">Erstellen eines geheimen Clientschlüssels</span><span class="sxs-lookup"><span data-stu-id="270ce-118">Create client secret</span></span>

* <span data-ttu-id="270ce-119">Wählen Sie im linken Bereich **Zertifikate und Geheimnisse** aus.</span><span class="sxs-lookup"><span data-stu-id="270ce-119">In the left pane, select **Certificates & secrets**.</span></span>
* <span data-ttu-id="270ce-120">Wählen Sie unter **Clientgeheimnisse**die Option **Neuer geheimer Clientschlüssel** aus</span><span class="sxs-lookup"><span data-stu-id="270ce-120">Under **Client secrets**, select **New client secret**</span></span>

  * <span data-ttu-id="270ce-121">Fügen Sie eine Beschreibung für den geheimen Clientschlüssel hinzu.</span><span class="sxs-lookup"><span data-stu-id="270ce-121">Add a description for the client secret.</span></span>
  * <span data-ttu-id="270ce-122">Wählen Sie die Schaltfläche **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="270ce-122">Select the **Add** button.</span></span>

* <span data-ttu-id="270ce-123">Kopieren Sie unter **Clientgeheimnisse**den Wert des geheimen Clientschlüssels.</span><span class="sxs-lookup"><span data-stu-id="270ce-123">Under **Client secrets**, copy the value of the client secret.</span></span>

<span data-ttu-id="270ce-124">Das URI-Segment `/signin-microsoft` wird als Standardrückruf des Microsoft-Authentifizierungsanbieters festgelegt.</span><span class="sxs-lookup"><span data-stu-id="270ce-124">The URI segment `/signin-microsoft` is set as the default callback of the Microsoft authentication provider.</span></span> <span data-ttu-id="270ce-125">Sie können den Standardrückruf-URI ändern, während Sie die Microsoft-Authentifizierungs-Middleware über die geerbte [RemoteAuthenticationOptions.CallbackPath-Eigenschaft](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) der [MicrosoftAccountOptions-Klasse](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="270ce-125">You can change the default callback URI while configuring the Microsoft authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) class.</span></span>

## <a name="store-the-microsoft-client-id-and-secret"></a><span data-ttu-id="270ce-126">Speichern der Microsoft-Client-ID und des geheimen Schlüssels</span><span class="sxs-lookup"><span data-stu-id="270ce-126">Store the Microsoft client ID and secret</span></span>

<span data-ttu-id="270ce-127">Speichern Sie vertrauliche Einstellungen wie die Microsoft-Client-ID und geheime Werte mit [Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="270ce-127">Store sensitive settings such as the Microsoft client ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="270ce-128">Gehen Sie für dieses Beispiel wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="270ce-128">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="270ce-129">Initialisieren Sie das Projekt für die geheime Speicherung gemäß den Anweisungen unter [Geheime Speicherung aktivieren](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="270ce-129">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="270ce-130">Speichern Sie die vertraulichen Einstellungen im `Authentication:Microsoft:ClientId` lokalen `Authentication:Microsoft:ClientSecret`geheimen Speicher mit den geheimen Schlüsseln und:</span><span class="sxs-lookup"><span data-stu-id="270ce-130">Store the sensitive settings in the local secret store with the secret keys `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a><span data-ttu-id="270ce-131">Konfigurieren der Microsoft-Kontoauthentifizierung</span><span class="sxs-lookup"><span data-stu-id="270ce-131">Configure Microsoft Account Authentication</span></span>

<span data-ttu-id="270ce-132">Fügen Sie den Microsoft-Kontodienst hinzu: `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="270ce-132">Add the Microsoft Account service to the `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

<span data-ttu-id="270ce-133">Weitere Informationen zu Konfigurationsoptionen, die von der Microsoft-Kontoauthentifizierung unterstützt werden, finden Sie in der [MicrosoftAccountOptions-API-Referenz.](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions)</span><span class="sxs-lookup"><span data-stu-id="270ce-133">For more information about configuration options supported by Microsoft Account authentication, see the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API reference.</span></span> <span data-ttu-id="270ce-134">Dies kann verwendet werden, um verschiedene Informationen über den Benutzer anzufordern.</span><span class="sxs-lookup"><span data-stu-id="270ce-134">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-microsoft-account"></a><span data-ttu-id="270ce-135">Anmelden mit Microsoft-Konto</span><span class="sxs-lookup"><span data-stu-id="270ce-135">Sign in with Microsoft Account</span></span>

<span data-ttu-id="270ce-136">Führen Sie die App aus, und klicken Sie auf **Anmelden**.</span><span class="sxs-lookup"><span data-stu-id="270ce-136">Run the app and click **Log in**.</span></span> <span data-ttu-id="270ce-137">Es wird eine Option zum Anmelden bei Microsoft angezeigt.</span><span class="sxs-lookup"><span data-stu-id="270ce-137">An option to sign in with Microsoft appears.</span></span> <span data-ttu-id="270ce-138">Wenn Sie auf Microsoft klicken, werden Sie zur Authentifizierung zu Microsoft weitergeleitet.</span><span class="sxs-lookup"><span data-stu-id="270ce-138">When you click on Microsoft, you are redirected to Microsoft for authentication.</span></span> <span data-ttu-id="270ce-139">Nachdem Sie sich mit Ihrem Microsoft-Konto angemeldet haben, werden Sie aufgefordert, der App den Zugriff auf Ihre Informationen zu gewähren:</span><span class="sxs-lookup"><span data-stu-id="270ce-139">After signing in with your Microsoft Account, you will be prompted to let the app access your info:</span></span>

<span data-ttu-id="270ce-140">Tippen Sie auf **Ja** und Sie werden zurück zur Website weitergeleitet, auf der Sie Ihre E-Mail-Adresse festlegen können.</span><span class="sxs-lookup"><span data-stu-id="270ce-140">Tap **Yes** and you will be redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="270ce-141">Sie sind jetzt mit Ihren Microsoft-Anmeldeinformationen angemeldet:</span><span class="sxs-lookup"><span data-stu-id="270ce-141">You are now logged in using your Microsoft credentials:</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="270ce-142">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="270ce-142">Troubleshooting</span></span>

* <span data-ttu-id="270ce-143">Wenn der Microsoft-Kontoanbieter Sie zu einer Anmeldefehlerseite umleitet, beachten Sie den `#` Fehlertitel und die Zeichenfolgenzeichenfolgenparameter der Beschreibung direkt nach dem (Hashtag) im Uri.</span><span class="sxs-lookup"><span data-stu-id="270ce-143">If the Microsoft Account provider redirects you to a sign in error page, note the error title and description query string parameters directly following the `#` (hashtag) in the Uri.</span></span>

  <span data-ttu-id="270ce-144">Obwohl die Fehlermeldung auf ein Problem mit der Microsoft-Authentifizierung zu hinweisen scheint, ist die häufigste Ursache, dass Ihre Anwendung URI keinem der für die **Webplattform** angegebenen **Redirect-URIs** entspricht.</span><span class="sxs-lookup"><span data-stu-id="270ce-144">Although the error message seems to indicate a problem with Microsoft authentication, the most common cause is your application Uri not matching any of the **Redirect URIs** specified for the **Web** platform.</span></span>
* <span data-ttu-id="270ce-145">Wenn Identity nicht durch Aufrufen `services.AddIdentity` konfiguriert `ConfigureServices`ist, führt der Versuch, sich zu authentifizieren, zu *ArgumentException: Die Option 'SignInScheme' muss bereitgestellt werden.*</span><span class="sxs-lookup"><span data-stu-id="270ce-145">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="270ce-146">Die in diesem Beispiel verwendete Projektvorlage stellt sicher, dass dies geschieht.</span><span class="sxs-lookup"><span data-stu-id="270ce-146">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="270ce-147">Wenn die Standortdatenbank nicht durch Anwenden der ersten Migration erstellt wurde, wird bei der Verarbeitung des Anforderungsfehlers *ein Datenbankvorgang fehlgeschlagen.*</span><span class="sxs-lookup"><span data-stu-id="270ce-147">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="270ce-148">Tippen Sie auf **Migrations anwenden,** um die Datenbank zu erstellen und zu aktualisieren, um nach dem Fehler fortzufahren.</span><span class="sxs-lookup"><span data-stu-id="270ce-148">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="270ce-149">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="270ce-149">Next steps</span></span>

* <span data-ttu-id="270ce-150">In diesem Artikel wurde gezeigt, wie Sie sich bei Microsoft authentifizieren können.</span><span class="sxs-lookup"><span data-stu-id="270ce-150">This article showed how you can authenticate with Microsoft.</span></span> <span data-ttu-id="270ce-151">Führen Sie einen ähnlichen Ansatz für die Authentifizierung mit anderen Anbietern aufgeführt, auf die [Vorgängerseite](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="270ce-151">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="270ce-152">Nachdem Sie Ihre Website in der Azure-Web-App veröffentlicht haben, erstellen Sie eine neue Client-Entsendestelle im Microsoft Developer Portal.</span><span class="sxs-lookup"><span data-stu-id="270ce-152">Once you publish your web site to Azure web app, create a new client secrets in the Microsoft Developer Portal.</span></span>

* <span data-ttu-id="270ce-153">Legen Sie die `Authentication:Microsoft:ClientId` und `Authentication:Microsoft:ClientSecret` Anwendungseinstellungen im Azure-Portal.</span><span class="sxs-lookup"><span data-stu-id="270ce-153">Set the `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="270ce-154">Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="270ce-154">The configuration system is set up to read keys from environment variables.</span></span>

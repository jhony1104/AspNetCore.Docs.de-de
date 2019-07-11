---
title: Setup von Microsoft Account externen Anmeldung mit ASP.NET Core
author: rick-anderson
description: Dieses Beispiel veranschaulicht die Integration von Microsoft-Konto-Benutzerauthentifizierung in eine vorhandene ASP.NET Core-app.
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2019
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 2c690e5bd8465806d42091616917cfdd747ef8f0
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815574"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a><span data-ttu-id="41b1d-103">Setup von Microsoft Account externen Anmeldung mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="41b1d-103">Microsoft Account external login setup with ASP.NET Core</span></span>

<span data-ttu-id="41b1d-104">Von [Valeriy Novytskyy](https://github.com/01binary) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="41b1d-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="41b1d-105">Dieses Beispiel zeigt, wie Sie Benutzern die Anmeldung mit ihrem Microsoft-Kontos mithilfe des ASP.NET Core-2.2-Projekts erstellt ermöglichen, sich die [Vorgängerseite](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="41b1d-105">This sample shows you how to enable users to sign in with their Microsoft account using the ASP.NET Core 2.2 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-microsoft-developer-portal"></a><span data-ttu-id="41b1d-106">Erstellen einer app in Microsoft-Entwicklerportal</span><span class="sxs-lookup"><span data-stu-id="41b1d-106">Create the app in Microsoft Developer Portal</span></span>

* <span data-ttu-id="41b1d-107">Navigieren Sie zu der [Azure-Portal – App-Registrierungen](https://go.microsoft.com/fwlink/?linkid=2083908) Seite, und erstellen, oder melden Sie sich bei einem Microsoft-Konto:</span><span class="sxs-lookup"><span data-stu-id="41b1d-107">Navigate to the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page and create or sign into a Microsoft account:</span></span>

<span data-ttu-id="41b1d-108">Wenn Sie nicht über ein Microsoft-Konto verfügen, wählen Sie **erstellen Sie eine**.</span><span class="sxs-lookup"><span data-stu-id="41b1d-108">If you don't have a Microsoft account, select **Create one**.</span></span> <span data-ttu-id="41b1d-109">Nach der Anmeldung werden Sie umgeleitet die **App-Registrierungen** Seite:</span><span class="sxs-lookup"><span data-stu-id="41b1d-109">After signing in you are redirected to the **App registrations** page:</span></span>

* <span data-ttu-id="41b1d-110">Wählen Sie **neue Registrierung**</span><span class="sxs-lookup"><span data-stu-id="41b1d-110">Select **New registration**</span></span>
* <span data-ttu-id="41b1d-111">Geben Sie einen **Namen**.</span><span class="sxs-lookup"><span data-stu-id="41b1d-111">Enter a **Name**.</span></span>
* <span data-ttu-id="41b1d-112">Wählen Sie eine Option für **unterstützt Kontotypen**.</span><span class="sxs-lookup"><span data-stu-id="41b1d-112">Select an option for **Supported account types**.</span></span>  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts -->
* <span data-ttu-id="41b1d-113">Klicken Sie unter **Umleitungs-URI**, geben Sie Ihre Entwicklung-URL mit `/signin-microsoft` angefügt.</span><span class="sxs-lookup"><span data-stu-id="41b1d-113">Under **Redirect URI**, enter your development URL with `/signin-microsoft` appended.</span></span> <span data-ttu-id="41b1d-114">Beispielsweise `https://localhost:44389/signin-microsoft`.</span><span class="sxs-lookup"><span data-stu-id="41b1d-114">For example, `https://localhost:44389/signin-microsoft`.</span></span> <span data-ttu-id="41b1d-115">Das Microsoft-Authentifizierungsschema, das weiter unten in diesem Beispiel konfiguriert übernimmt automatisch die Anforderungen an `/signin-microsoft` Route zum Implementieren von OAuth-Fluss.</span><span class="sxs-lookup"><span data-stu-id="41b1d-115">The Microsoft authentication scheme configured later in this sample will automatically handle requests at `/signin-microsoft` route to implement the OAuth flow.</span></span>
* <span data-ttu-id="41b1d-116">Wählen Sie **registrieren**</span><span class="sxs-lookup"><span data-stu-id="41b1d-116">Select **Register**</span></span>

### <a name="create-client-secret"></a><span data-ttu-id="41b1d-117">Erstellen Sie den geheimen Clientschlüssel</span><span class="sxs-lookup"><span data-stu-id="41b1d-117">Create client secret</span></span>

* <span data-ttu-id="41b1d-118">Wählen Sie im linken Bereich **Zertifikate und Geheimnisse**.</span><span class="sxs-lookup"><span data-stu-id="41b1d-118">In the left pane, select **Certificates & secrets**.</span></span>
* <span data-ttu-id="41b1d-119">Klicken Sie unter **geheime Clientschlüssel**Option **neuer geheimer Clientschlüssel**</span><span class="sxs-lookup"><span data-stu-id="41b1d-119">Under **Client secrets**, select **New client secret**</span></span>

  * <span data-ttu-id="41b1d-120">Fügen Sie eine Beschreibung für den geheimen Clientschlüssel hinzu.</span><span class="sxs-lookup"><span data-stu-id="41b1d-120">Add a description for the client secret.</span></span>
  * <span data-ttu-id="41b1d-121">Wählen Sie die **hinzufügen** Schaltfläche.</span><span class="sxs-lookup"><span data-stu-id="41b1d-121">Select the **Add** button.</span></span>

* <span data-ttu-id="41b1d-122">Klicken Sie unter **geheime Clientschlüssel**, kopieren Sie den Wert der geheime Clientschlüssel.</span><span class="sxs-lookup"><span data-stu-id="41b1d-122">Under **Client secrets**, copy the value of the client secret.</span></span>

> [!NOTE]
> <span data-ttu-id="41b1d-123">Der URI-Segment `/signin-microsoft` ist als der standardrückruf des Authentifizierungsanbieters Microsoft festgelegt.</span><span class="sxs-lookup"><span data-stu-id="41b1d-123">The URI segment `/signin-microsoft` is set as the default callback of the Microsoft authentication provider.</span></span> <span data-ttu-id="41b1d-124">Sie können den standardrückruf-URI ändern, während der Konfiguration der Microsoft-Authentifizierung-Middleware über die geerbte [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) Eigenschaft der [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) Klasse.</span><span class="sxs-lookup"><span data-stu-id="41b1d-124">You can change the default callback URI while configuring the Microsoft authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) class.</span></span>

## <a name="store-the-microsoft-client-id-and-client-secret"></a><span data-ttu-id="41b1d-125">Der geheime Clientschlüssel-ID und den Microsoft Store</span><span class="sxs-lookup"><span data-stu-id="41b1d-125">Store the Microsoft client ID and client secret</span></span>

<span data-ttu-id="41b1d-126">Führen Sie die folgenden Befehle zum sicheren Speichern von `ClientId` und `ClientSecret` mit [Secret Manager](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="41b1d-126">Run the following commands to securely store `ClientId` and `ClientSecret` using [Secret Manager](xref:security/app-secrets):</span></span>

```console
dotnet user-secrets set Authentication:Microsoft:ClientId <Client-Id>
dotnet user-secrets set Authentication:Microsoft:ClientSecret <Client-Secret>
```

<span data-ttu-id="41b1d-127">Verknüpfen Sie sensible Einstellungen wie Microsoft `ClientId` und `ClientSecret` zu Ihrer Anwendungskonfigurationsdatei mithilfe der [Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="41b1d-127">Link sensitive settings like Microsoft `ClientId` and `ClientSecret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="41b1d-128">Benennen Sie im Rahmen dieses Beispiels werden die Token `Authentication:Microsoft:ClientId` und `Authentication:Microsoft:ClientSecret`.</span><span class="sxs-lookup"><span data-stu-id="41b1d-128">For the purposes of this sample, name the tokens `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret`.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a><span data-ttu-id="41b1d-129">Konfigurieren von Microsoft-Kontoauthentifizierung</span><span class="sxs-lookup"><span data-stu-id="41b1d-129">Configure Microsoft Account Authentication</span></span>

<span data-ttu-id="41b1d-130">Fügen Sie den Dienst Microsoft Account, `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="41b1d-130">Add the Microsoft Account service to `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupMS.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="41b1d-131">Finden Sie unter den [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API-Referenz für Weitere Informationen zu den Konfigurationsoptionen, die von der Microsoft-Account-Authentifizierung unterstützt.</span><span class="sxs-lookup"><span data-stu-id="41b1d-131">See the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API reference for more information on configuration options supported by Microsoft Account authentication.</span></span> <span data-ttu-id="41b1d-132">Dies kann verwendet werden, um verschiedene Informationen über den Benutzer anzufordern.</span><span class="sxs-lookup"><span data-stu-id="41b1d-132">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-microsoft-account"></a><span data-ttu-id="41b1d-133">Melden Sie sich mit Microsoft-Konto</span><span class="sxs-lookup"><span data-stu-id="41b1d-133">Sign in with Microsoft Account</span></span>

<span data-ttu-id="41b1d-134">Führen Sie die, und klicken Sie auf **melden Sie sich bei**.</span><span class="sxs-lookup"><span data-stu-id="41b1d-134">Run the and click **Log in**.</span></span> <span data-ttu-id="41b1d-135">Eine Option aus, um sich mit Microsoft anmelden wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="41b1d-135">An option to sign in with Microsoft appears.</span></span> <span data-ttu-id="41b1d-136">Wenn Sie auf Microsoft klicken, werden Sie für die Authentifizierung an Microsoft weitergeleitet.</span><span class="sxs-lookup"><span data-stu-id="41b1d-136">When you click on Microsoft, you are redirected to Microsoft for authentication.</span></span> <span data-ttu-id="41b1d-137">Nachdem Sie sich mit Ihrem Microsoft Account (sofern nicht bereits angemeldet) werden Sie aufgefordert, zu der app den Zugriff auf Ihre Infos ermöglichen:</span><span class="sxs-lookup"><span data-stu-id="41b1d-137">After signing in with your Microsoft Account (if not already signed in) you will be prompted to let the app access your info:</span></span>

<span data-ttu-id="41b1d-138">Tippen Sie auf **Ja** und Sie gelangen zurück auf der Website, in dem Sie Ihre e-Mail-Adresse festlegen können.</span><span class="sxs-lookup"><span data-stu-id="41b1d-138">Tap **Yes** and you will be redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="41b1d-139">Sie sind jetzt angemeldet, sich mit Ihren Microsoft-Anmeldeinformationen:</span><span class="sxs-lookup"><span data-stu-id="41b1d-139">You are now logged in using your Microsoft credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="41b1d-140">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="41b1d-140">Troubleshooting</span></span>

* <span data-ttu-id="41b1d-141">Wenn der Microsoft-Account-Anbieter auf eine Fehler-Anmeldeseite umleitet, beachten Sie die Fehler Titel und Beschreibung Abfragezeichenfolgen-Parameter direkt nach der `#` (Hashtag) im Uri.</span><span class="sxs-lookup"><span data-stu-id="41b1d-141">If the Microsoft Account provider redirects you to a sign in error page, note the error title and description query string parameters directly following the `#` (hashtag) in the Uri.</span></span>

  <span data-ttu-id="41b1d-142">Obwohl die Fehlermeldung ein Problem mit der Microsoft-Authentifizierung angeben anscheinend, ist die häufigste Ursache Ihrer Anwendungs-Uri, die nicht mit einer der der **Umleitungs-URIs** für die **Web** Plattform .</span><span class="sxs-lookup"><span data-stu-id="41b1d-142">Although the error message seems to indicate a problem with Microsoft authentication, the most common cause is your application Uri not matching any of the **Redirect URIs** specified for the **Web** platform.</span></span>
* <span data-ttu-id="41b1d-143">Wenn Identität nicht, durch den Aufruf konfiguriert ist `services.AddIdentity` in `ConfigureServices`, Authentifizierungsversuch führt zu *ArgumentException: Die Option 'SignInScheme' muss angegeben werden*.</span><span class="sxs-lookup"><span data-stu-id="41b1d-143">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="41b1d-144">Die Projektvorlage, die in diesem Beispiel verwendete wird sichergestellt, dass dies geschehen ist.</span><span class="sxs-lookup"><span data-stu-id="41b1d-144">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="41b1d-145">Wenn die Standortdatenbank nicht erstellt wurde, indem die ursprüngliche Migration anwenden, erhalten Sie *Fehler bei ein Datenbankvorgang beim Verarbeiten der Anforderung* Fehler.</span><span class="sxs-lookup"><span data-stu-id="41b1d-145">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="41b1d-146">Tippen Sie auf **Migrationen anwenden** der Datenbank zu erstellen und aktualisieren, um den Fehler hinaus fortgesetzt.</span><span class="sxs-lookup"><span data-stu-id="41b1d-146">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="41b1d-147">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="41b1d-147">Next steps</span></span>

* <span data-ttu-id="41b1d-148">In diesem Artikel wurde gezeigt, wie Sie mit Microsoft authentifizieren können.</span><span class="sxs-lookup"><span data-stu-id="41b1d-148">This article showed how you can authenticate with Microsoft.</span></span> <span data-ttu-id="41b1d-149">Führen Sie einen ähnlichen Ansatz für die Authentifizierung mit anderen Anbietern aufgeführt, auf die [Vorgängerseite](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="41b1d-149">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="41b1d-150">Nachdem Sie Ihre Website in Azure-Web-app veröffentlichen, erstellen Sie einen neuen Client geheimer Schlüssel im Microsoft Developer Portal.</span><span class="sxs-lookup"><span data-stu-id="41b1d-150">Once you publish your web site to Azure web app, create a new client secrets in the Microsoft Developer Portal.</span></span>

* <span data-ttu-id="41b1d-151">Legen Sie die `Authentication:Microsoft:ClientId` und `Authentication:Microsoft:ClientSecret` Anwendungseinstellungen im Azure-Portal.</span><span class="sxs-lookup"><span data-stu-id="41b1d-151">Set the `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="41b1d-152">Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="41b1d-152">The configuration system is set up to read keys from environment variables.</span></span>
---
title: Einrichtung externer Anmelde Informationen für Microsoft-Konto mit ASP.net Core
author: rick-anderson
description: In diesem Beispiel wird die Integration von Microsoft-Konto Benutzerauthentifizierung in eine vorhandene ASP.net Core-App veranschaulicht.
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2019
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 91ace293fd16cd180b3d5c183c637af6db1d08c3
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082345"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a><span data-ttu-id="38c7c-103">Einrichtung externer Anmelde Informationen für Microsoft-Konto mit ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="38c7c-103">Microsoft Account external login setup with ASP.NET Core</span></span>

<span data-ttu-id="38c7c-104">Von [Valeriy Novytskyy](https://github.com/01binary) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="38c7c-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="38c7c-105">In diesem Beispiel wird gezeigt, wie Sie es Benutzern mithilfe des auf der [vorherigen Seite](xref:security/authentication/social/index)erstellten ASP.net Core 2,2-Projekts ermöglichen, sich mit Ihrem Microsoft-Konto anzumelden.</span><span class="sxs-lookup"><span data-stu-id="38c7c-105">This sample shows you how to enable users to sign in with their Microsoft account using the ASP.NET Core 2.2 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-microsoft-developer-portal"></a><span data-ttu-id="38c7c-106">Erstellen der APP im Microsoft-Entwickler Portal</span><span class="sxs-lookup"><span data-stu-id="38c7c-106">Create the app in Microsoft Developer Portal</span></span>

* <span data-ttu-id="38c7c-107">Navigieren Sie zur Seite " [Azure-Portal-App-Registrierungen](https://go.microsoft.com/fwlink/?linkid=2083908) ", und erstellen Sie eine Microsoft-Konto, oder melden Sie sich an:</span><span class="sxs-lookup"><span data-stu-id="38c7c-107">Navigate to the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page and create or sign into a Microsoft account:</span></span>

<span data-ttu-id="38c7c-108">Wenn Sie keine Microsoft-Konto haben, wählen Sie **Erstellen**aus.</span><span class="sxs-lookup"><span data-stu-id="38c7c-108">If you don't have a Microsoft account, select **Create one**.</span></span> <span data-ttu-id="38c7c-109">Nachdem Sie sich angemeldet haben, werden Sie auf die Seite **App-Registrierungen** umgeleitet:</span><span class="sxs-lookup"><span data-stu-id="38c7c-109">After signing in you are redirected to the **App registrations** page:</span></span>

* <span data-ttu-id="38c7c-110">**Neue Registrierung** auswählen</span><span class="sxs-lookup"><span data-stu-id="38c7c-110">Select **New registration**</span></span>
* <span data-ttu-id="38c7c-111">Geben Sie einen **Namen**ein.</span><span class="sxs-lookup"><span data-stu-id="38c7c-111">Enter a **Name**.</span></span>
* <span data-ttu-id="38c7c-112">Wählen Sie eine Option für **unterstützte Konto Typen**aus.</span><span class="sxs-lookup"><span data-stu-id="38c7c-112">Select an option for **Supported account types**.</span></span>  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts -->
* <span data-ttu-id="38c7c-113">Geben Sie unter **Umleitungs-URI**Ihre Entwicklungs `/signin-microsoft` -URL mit angefügt ein.</span><span class="sxs-lookup"><span data-stu-id="38c7c-113">Under **Redirect URI**, enter your development URL with `/signin-microsoft` appended.</span></span> <span data-ttu-id="38c7c-114">Beispielsweise `https://localhost:44389/signin-microsoft`.</span><span class="sxs-lookup"><span data-stu-id="38c7c-114">For example, `https://localhost:44389/signin-microsoft`.</span></span> <span data-ttu-id="38c7c-115">Das Microsoft-Authentifizierungsschema, das weiter unten in diesem Beispiel konfiguriert wird `/signin-microsoft` , verarbeitet Anforderungen bei der Route automatisch, um den OAuth-Fluss zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="38c7c-115">The Microsoft authentication scheme configured later in this sample will automatically handle requests at `/signin-microsoft` route to implement the OAuth flow.</span></span>
* <span data-ttu-id="38c7c-116">**Register** auswählen</span><span class="sxs-lookup"><span data-stu-id="38c7c-116">Select **Register**</span></span>

### <a name="create-client-secret"></a><span data-ttu-id="38c7c-117">Geheimen Client Schlüssel erstellen</span><span class="sxs-lookup"><span data-stu-id="38c7c-117">Create client secret</span></span>

* <span data-ttu-id="38c7c-118">Klicken Sie im linken Bereich auf **Zertifikate & Geheimnisse**.</span><span class="sxs-lookup"><span data-stu-id="38c7c-118">In the left pane, select **Certificates & secrets**.</span></span>
* <span data-ttu-id="38c7c-119">Wählen Sie unter geheime **Client**Schlüssel die Option **neuer geheimer Client** Schlüssel</span><span class="sxs-lookup"><span data-stu-id="38c7c-119">Under **Client secrets**, select **New client secret**</span></span>

  * <span data-ttu-id="38c7c-120">Fügen Sie eine Beschreibung für den geheimen Client Schlüssel hinzu.</span><span class="sxs-lookup"><span data-stu-id="38c7c-120">Add a description for the client secret.</span></span>
  * <span data-ttu-id="38c7c-121">Wählen Sie die Schaltfläche **Hinzufügen** .</span><span class="sxs-lookup"><span data-stu-id="38c7c-121">Select the **Add** button.</span></span>

* <span data-ttu-id="38c7c-122">Kopieren Sie unter geheime **Client**Schlüssel den Wert des geheimen Client Schlüssels.</span><span class="sxs-lookup"><span data-stu-id="38c7c-122">Under **Client secrets**, copy the value of the client secret.</span></span>

> [!NOTE]
> <span data-ttu-id="38c7c-123">Das URI- `/signin-microsoft` Segment wird als Standard Rückruf des Microsoft-Authentifizierungs Anbieters festgelegt.</span><span class="sxs-lookup"><span data-stu-id="38c7c-123">The URI segment `/signin-microsoft` is set as the default callback of the Microsoft authentication provider.</span></span> <span data-ttu-id="38c7c-124">Sie können den Standard-Rückruf-URI beim Konfigurieren der Microsoft-Authentifizierungs Middleware über die geerbte [remoteauthenticationoptions. callbackpath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) -Eigenschaft der [microsoftaccountoptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) -Klasse ändern.</span><span class="sxs-lookup"><span data-stu-id="38c7c-124">You can change the default callback URI while configuring the Microsoft authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) class.</span></span>

## <a name="store-the-microsoft-client-id-and-client-secret"></a><span data-ttu-id="38c7c-125">Speichern der Microsoft-Client-ID und des geheimen Client Schlüssels</span><span class="sxs-lookup"><span data-stu-id="38c7c-125">Store the Microsoft client ID and client secret</span></span>

<span data-ttu-id="38c7c-126">Führen Sie die folgenden Befehle zum sicheren `ClientId` speichern `ClientSecret` und Verwenden von [Secret Manager](xref:security/app-secrets)aus:</span><span class="sxs-lookup"><span data-stu-id="38c7c-126">Run the following commands to securely store `ClientId` and `ClientSecret` using [Secret Manager](xref:security/app-secrets):</span></span>

```dotnetcli
dotnet user-secrets set Authentication:Microsoft:ClientId <Client-Id>
dotnet user-secrets set Authentication:Microsoft:ClientSecret <Client-Secret>
```

<span data-ttu-id="38c7c-127">Verknüpfen Sie sensible Einstellungen wie `ClientId` Microsoft `ClientSecret` und die Anwendungskonfiguration mithilfe des [geheimen Managers](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="38c7c-127">Link sensitive settings like Microsoft `ClientId` and `ClientSecret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="38c7c-128">Benennen Sie für dieses Beispiel die Token `Authentication:Microsoft:ClientId` und. `Authentication:Microsoft:ClientSecret`</span><span class="sxs-lookup"><span data-stu-id="38c7c-128">For the purposes of this sample, name the tokens `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret`.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a><span data-ttu-id="38c7c-129">Konfigurieren der Microsoft-Konto Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="38c7c-129">Configure Microsoft Account Authentication</span></span>

<span data-ttu-id="38c7c-130">Fügen Sie den Microsoft-Konto `Startup.ConfigureServices`Dienst hinzu:</span><span class="sxs-lookup"><span data-stu-id="38c7c-130">Add the Microsoft Account service to `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupMS.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="38c7c-131">Weitere Informationen zu Konfigurationsoptionen, die von der Microsoft-Konto Authentifizierung unterstützt werden, finden Sie in der [microsoftaccountoptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) -API</span><span class="sxs-lookup"><span data-stu-id="38c7c-131">See the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API reference for more information on configuration options supported by Microsoft Account authentication.</span></span> <span data-ttu-id="38c7c-132">Dies kann verwendet werden, um verschiedene Informationen über den Benutzer anzufordern.</span><span class="sxs-lookup"><span data-stu-id="38c7c-132">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-microsoft-account"></a><span data-ttu-id="38c7c-133">Bei Microsoft anmelden Konto</span><span class="sxs-lookup"><span data-stu-id="38c7c-133">Sign in with Microsoft Account</span></span>

<span data-ttu-id="38c7c-134">Führen Sie aus, und klicken Sie auf **Anmelden**.</span><span class="sxs-lookup"><span data-stu-id="38c7c-134">Run the and click **Log in**.</span></span> <span data-ttu-id="38c7c-135">Eine Option zum Anmelden bei Microsoft wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="38c7c-135">An option to sign in with Microsoft appears.</span></span> <span data-ttu-id="38c7c-136">Wenn Sie auf Microsoft klicken, werden Sie zur Authentifizierung an Microsoft umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="38c7c-136">When you click on Microsoft, you are redirected to Microsoft for authentication.</span></span> <span data-ttu-id="38c7c-137">Nachdem Sie sich mit Ihrem Microsoft-Konto angemeldet haben (wenn es nicht bereits angemeldet ist), werden Sie aufgefordert, der APP den Zugriff auf Ihre Informationen zu gestatten:</span><span class="sxs-lookup"><span data-stu-id="38c7c-137">After signing in with your Microsoft Account (if not already signed in) you will be prompted to let the app access your info:</span></span>

<span data-ttu-id="38c7c-138">Tippen Sie auf **Ja** , und Sie werden zurück an die Website umgeleitet, auf der Sie Ihre e-Mail festlegen können.</span><span class="sxs-lookup"><span data-stu-id="38c7c-138">Tap **Yes** and you will be redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="38c7c-139">Sie sind jetzt mit Ihren Microsoft-Anmelde Informationen angemeldet:</span><span class="sxs-lookup"><span data-stu-id="38c7c-139">You are now logged in using your Microsoft credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="38c7c-140">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="38c7c-140">Troubleshooting</span></span>

* <span data-ttu-id="38c7c-141">Wenn der Microsoft-Konto Anbieter Sie an eine Anmeldefehler Seite weiterleitet, beachten Sie die Parameter für die Fehler Titel und die Beschreibungs `#` Abfrage Zeichenfolge direkt nach dem (hashtag) im URI.</span><span class="sxs-lookup"><span data-stu-id="38c7c-141">If the Microsoft Account provider redirects you to a sign in error page, note the error title and description query string parameters directly following the `#` (hashtag) in the Uri.</span></span>

  <span data-ttu-id="38c7c-142">Obwohl in der Fehlermeldung ein Problem mit der Microsoft-Authentifizierung angezeigt wird, ist die häufigste Ursache, dass ihr Anwendungs-URI mit keinem der für die **Webplattform** angegebenen **Umleitungs-URIs** übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="38c7c-142">Although the error message seems to indicate a problem with Microsoft authentication, the most common cause is your application Uri not matching any of the **Redirect URIs** specified for the **Web** platform.</span></span>
* <span data-ttu-id="38c7c-143">Wenn die Identität nicht durch Aufrufen `services.AddIdentity` `ConfigureServices`von konfiguriert wird, führt der Versuch der *Authentifizierung zu argumumtexception: Die Option "signinscheme" muss angegeben*werden.</span><span class="sxs-lookup"><span data-stu-id="38c7c-143">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="38c7c-144">Die in diesem Beispiel verwendete Projektvorlage stellt sicher, dass dies abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="38c7c-144">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="38c7c-145">Wenn die Standortdatenbank nicht erstellt wurde, indem die ursprüngliche Migration anwenden, erhalten Sie *Fehler bei ein Datenbankvorgang beim Verarbeiten der Anforderung* Fehler.</span><span class="sxs-lookup"><span data-stu-id="38c7c-145">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="38c7c-146">Tippen Sie auf **Migrationen anwenden** der Datenbank zu erstellen und aktualisieren, um den Fehler hinaus fortgesetzt.</span><span class="sxs-lookup"><span data-stu-id="38c7c-146">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="38c7c-147">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="38c7c-147">Next steps</span></span>

* <span data-ttu-id="38c7c-148">In diesem Artikel wurde gezeigt, wie Sie sich bei Microsoft authentifizieren können.</span><span class="sxs-lookup"><span data-stu-id="38c7c-148">This article showed how you can authenticate with Microsoft.</span></span> <span data-ttu-id="38c7c-149">Führen Sie einen ähnlichen Ansatz für die Authentifizierung mit anderen Anbietern aufgeführt, auf die [Vorgängerseite](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="38c7c-149">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="38c7c-150">Nachdem Sie die Website in der Azure-Web-App veröffentlicht haben, erstellen Sie im Microsoft-Entwickler Portal ein neues Client Geheimnis.</span><span class="sxs-lookup"><span data-stu-id="38c7c-150">Once you publish your web site to Azure web app, create a new client secrets in the Microsoft Developer Portal.</span></span>

* <span data-ttu-id="38c7c-151">Legen Sie die `Authentication:Microsoft:ClientId` und `Authentication:Microsoft:ClientSecret` Anwendungseinstellungen im Azure-Portal.</span><span class="sxs-lookup"><span data-stu-id="38c7c-151">Set the `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="38c7c-152">Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="38c7c-152">The configuration system is set up to read keys from environment variables.</span></span>
---
title: Einrichtung externer Anmelde Informationen für Microsoft-Konto mit ASP.net Core
author: rick-anderson
description: In diesem Beispiel wird die Integration von Microsoft-Konto Benutzerauthentifizierung in eine vorhandene ASP.net Core-App veranschaulicht.
ms.author: riande
ms.custom: mvc
ms.date: 12/4/2019
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/microsoft-logins
ms.openlocfilehash: ddaae1a25a1dcf167ffae0f24b480e2cde6aca5b
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652069"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a><span data-ttu-id="6cafa-103">Einrichtung externer Anmelde Informationen für Microsoft-Konto mit ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="6cafa-103">Microsoft Account external login setup with ASP.NET Core</span></span>

<span data-ttu-id="6cafa-104">Von [Valeriy Novytskyy](https://github.com/01binary) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6cafa-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="6cafa-105">In diesem Beispiel wird gezeigt, wie Sie es Benutzern mithilfe des auf der [vorherigen Seite](xref:security/authentication/social/index)erstellten ASP.net Core 3,0-Projekts ermöglichen, sich mit Ihrem Microsoft-Konto anzumelden.</span><span class="sxs-lookup"><span data-stu-id="6cafa-105">This sample shows you how to enable users to sign in with their Microsoft account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-microsoft-developer-portal"></a><span data-ttu-id="6cafa-106">Erstellen der APP im Microsoft-Entwickler Portal</span><span class="sxs-lookup"><span data-stu-id="6cafa-106">Create the app in Microsoft Developer Portal</span></span>

* <span data-ttu-id="6cafa-107">Fügen Sie dem Projekt das nuget-Paket [Microsoft. aspnetcore. Authentication. microsoftaccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) hinzu.</span><span class="sxs-lookup"><span data-stu-id="6cafa-107">Add the [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet package to the project.</span></span>
* <span data-ttu-id="6cafa-108">Navigieren Sie zur Seite " [Azure-Portal-App-Registrierungen](https://go.microsoft.com/fwlink/?linkid=2083908) ", und erstellen Sie eine Microsoft-Konto, oder melden Sie sich an:</span><span class="sxs-lookup"><span data-stu-id="6cafa-108">Navigate to the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page and create or sign into a Microsoft account:</span></span>

<span data-ttu-id="6cafa-109">Wenn Sie keine Microsoft-Konto haben, wählen Sie **Erstellen**aus.</span><span class="sxs-lookup"><span data-stu-id="6cafa-109">If you don't have a Microsoft account, select **Create one**.</span></span> <span data-ttu-id="6cafa-110">Nachdem Sie sich angemeldet haben, werden Sie auf die Seite **App-Registrierungen** umgeleitet:</span><span class="sxs-lookup"><span data-stu-id="6cafa-110">After signing in, you are redirected to the **App registrations** page:</span></span>

* <span data-ttu-id="6cafa-111">Wählen Sie **Neue Registrierung** aus.</span><span class="sxs-lookup"><span data-stu-id="6cafa-111">Select **New registration**</span></span>
* <span data-ttu-id="6cafa-112">Geben Sie einen **Namen** ein.</span><span class="sxs-lookup"><span data-stu-id="6cafa-112">Enter a **Name**.</span></span>
* <span data-ttu-id="6cafa-113">Wählen Sie eine Option für **unterstützte Konto Typen**aus.</span><span class="sxs-lookup"><span data-stu-id="6cafa-113">Select an option for **Supported account types**.</span></span>  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts -->
* <span data-ttu-id="6cafa-114">Geben Sie unter **Umleitungs-URI**Ihre Entwicklungs-URL mit angefügtem `/signin-microsoft` ein.</span><span class="sxs-lookup"><span data-stu-id="6cafa-114">Under **Redirect URI**, enter your development URL with `/signin-microsoft` appended.</span></span> <span data-ttu-id="6cafa-115">Beispiel: `https://localhost:5001/signin-microsoft`.</span><span class="sxs-lookup"><span data-stu-id="6cafa-115">For example, `https://localhost:5001/signin-microsoft`.</span></span> <span data-ttu-id="6cafa-116">Das Microsoft-Authentifizierungsschema, das weiter unten in diesem Beispiel konfiguriert wird, verarbeitet automatisch Anforderungen auf `/signin-microsoft` Route, um den OAuth-Fluss zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="6cafa-116">The Microsoft authentication scheme configured later in this sample will automatically handle requests at `/signin-microsoft` route to implement the OAuth flow.</span></span>
* <span data-ttu-id="6cafa-117">Wählen Sie **Registrieren** aus.</span><span class="sxs-lookup"><span data-stu-id="6cafa-117">Select **Register**</span></span>

### <a name="create-client-secret"></a><span data-ttu-id="6cafa-118">Erstellen eines geheimen Clientschlüssels</span><span class="sxs-lookup"><span data-stu-id="6cafa-118">Create client secret</span></span>

* <span data-ttu-id="6cafa-119">Wählen Sie im linken Bereich **Zertifikate und Geheimnisse** aus.</span><span class="sxs-lookup"><span data-stu-id="6cafa-119">In the left pane, select **Certificates & secrets**.</span></span>
* <span data-ttu-id="6cafa-120">Wählen Sie unter geheime **Client**Schlüssel die Option **neuer geheimer Client** Schlüssel</span><span class="sxs-lookup"><span data-stu-id="6cafa-120">Under **Client secrets**, select **New client secret**</span></span>

  * <span data-ttu-id="6cafa-121">Fügen Sie eine Beschreibung für den geheimen Client Schlüssel hinzu.</span><span class="sxs-lookup"><span data-stu-id="6cafa-121">Add a description for the client secret.</span></span>
  * <span data-ttu-id="6cafa-122">Wählen Sie die Schaltfläche **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="6cafa-122">Select the **Add** button.</span></span>

* <span data-ttu-id="6cafa-123">Kopieren Sie unter geheime **Client**Schlüssel den Wert des geheimen Client Schlüssels.</span><span class="sxs-lookup"><span data-stu-id="6cafa-123">Under **Client secrets**, copy the value of the client secret.</span></span>

> [!NOTE]
> <span data-ttu-id="6cafa-124">Das URI-Segment `/signin-microsoft` wird als Standard Rückruf des Microsoft-Authentifizierungs Anbieters festgelegt.</span><span class="sxs-lookup"><span data-stu-id="6cafa-124">The URI segment `/signin-microsoft` is set as the default callback of the Microsoft authentication provider.</span></span> <span data-ttu-id="6cafa-125">Sie können den Standard-Rückruf-URI beim Konfigurieren der Microsoft-Authentifizierungs Middleware über die geerbte [remoteauthenticationoptions. callbackpath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) -Eigenschaft der [microsoftaccountoptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) -Klasse ändern.</span><span class="sxs-lookup"><span data-stu-id="6cafa-125">You can change the default callback URI while configuring the Microsoft authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) class.</span></span>

## <a name="store-the-microsoft-client-id-and-client-secret"></a><span data-ttu-id="6cafa-126">Speichern der Microsoft-Client-ID und des geheimen Client Schlüssels</span><span class="sxs-lookup"><span data-stu-id="6cafa-126">Store the Microsoft client ID and client secret</span></span>

<span data-ttu-id="6cafa-127">Führen Sie die folgenden Befehle aus, um `ClientId` und `ClientSecret` mithilfe von [Secret Manager](xref:security/app-secrets)sicher zu speichern:</span><span class="sxs-lookup"><span data-stu-id="6cafa-127">Run the following commands to securely store `ClientId` and `ClientSecret` using [Secret Manager](xref:security/app-secrets):</span></span>

```dotnetcli
dotnet user-secrets set Authentication:Microsoft:ClientId <Client-Id>
dotnet user-secrets set Authentication:Microsoft:ClientSecret <Client-Secret>
```

<span data-ttu-id="6cafa-128">Verknüpfen Sie sensible Einstellungen wie Microsoft `ClientId` und `ClientSecret` mithilfe des [geheimen Managers](xref:security/app-secrets)mit ihrer Anwendungskonfiguration.</span><span class="sxs-lookup"><span data-stu-id="6cafa-128">Link sensitive settings like Microsoft `ClientId` and `ClientSecret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="6cafa-129">Benennen Sie die Token für die Zwecke dieses Beispiels `Authentication:Microsoft:ClientId` und `Authentication:Microsoft:ClientSecret`.</span><span class="sxs-lookup"><span data-stu-id="6cafa-129">For the purposes of this sample, name the tokens `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret`.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a><span data-ttu-id="6cafa-130">Konfigurieren der Microsoft-Konto Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="6cafa-130">Configure Microsoft Account Authentication</span></span>

<span data-ttu-id="6cafa-131">Fügen Sie dem `Startup.ConfigureServices`den Microsoft-Konto Dienst hinzu:</span><span class="sxs-lookup"><span data-stu-id="6cafa-131">Add the Microsoft Account service to the `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="6cafa-132">Weitere Informationen zu den Konfigurationsoptionen, die von der Microsoft-Konto Authentifizierung unterstützt werden, finden Sie in der [microsoftaccountoptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) -API</span><span class="sxs-lookup"><span data-stu-id="6cafa-132">For more information about configuration options supported by Microsoft Account authentication, see the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API reference.</span></span> <span data-ttu-id="6cafa-133">Dies kann verwendet werden, um verschiedene Informationen über den Benutzer anzufordern.</span><span class="sxs-lookup"><span data-stu-id="6cafa-133">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-microsoft-account"></a><span data-ttu-id="6cafa-134">Bei Microsoft anmelden Konto</span><span class="sxs-lookup"><span data-stu-id="6cafa-134">Sign in with Microsoft Account</span></span>

<span data-ttu-id="6cafa-135">Führen Sie die APP aus, und klicken Sie auf **Anmelden**.</span><span class="sxs-lookup"><span data-stu-id="6cafa-135">Run the app and click **Log in**.</span></span> <span data-ttu-id="6cafa-136">Eine Option zum Anmelden bei Microsoft wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="6cafa-136">An option to sign in with Microsoft appears.</span></span> <span data-ttu-id="6cafa-137">Wenn Sie auf Microsoft klicken, werden Sie zur Authentifizierung an Microsoft umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="6cafa-137">When you click on Microsoft, you are redirected to Microsoft for authentication.</span></span> <span data-ttu-id="6cafa-138">Nachdem Sie sich mit Ihrem Microsoft-Konto angemeldet haben, werden Sie aufgefordert, der APP den Zugriff auf Ihre Informationen zu gestatten:</span><span class="sxs-lookup"><span data-stu-id="6cafa-138">After signing in with your Microsoft Account, you will be prompted to let the app access your info:</span></span>

<span data-ttu-id="6cafa-139">Tippen Sie auf **Ja** , und Sie werden zurück an die Website umgeleitet, auf der Sie Ihre e-Mail festlegen können.</span><span class="sxs-lookup"><span data-stu-id="6cafa-139">Tap **Yes** and you will be redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="6cafa-140">Sie sind jetzt mit Ihren Microsoft-Anmelde Informationen angemeldet:</span><span class="sxs-lookup"><span data-stu-id="6cafa-140">You are now logged in using your Microsoft credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="6cafa-141">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="6cafa-141">Troubleshooting</span></span>

* <span data-ttu-id="6cafa-142">Wenn der Microsoft-Konto Anbieter Sie an eine Anmeldefehler Seite weiterleitet, notieren Sie sich die Parameter für die Fehler Titel und die Beschreibungs Abfrage Zeichenfolge direkt nach dem `#` (hashtag) im URI.</span><span class="sxs-lookup"><span data-stu-id="6cafa-142">If the Microsoft Account provider redirects you to a sign in error page, note the error title and description query string parameters directly following the `#` (hashtag) in the Uri.</span></span>

  <span data-ttu-id="6cafa-143">Obwohl in der Fehlermeldung ein Problem mit der Microsoft-Authentifizierung angezeigt wird, ist die häufigste Ursache, dass ihr Anwendungs-URI mit keinem der für die **Webplattform** angegebenen **Umleitungs-URIs** übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="6cafa-143">Although the error message seems to indicate a problem with Microsoft authentication, the most common cause is your application Uri not matching any of the **Redirect URIs** specified for the **Web** platform.</span></span>
* <span data-ttu-id="6cafa-144">Wenn die Identität nicht durch Aufrufen von `services.AddIdentity` in `ConfigureServices`konfiguriert wird, führt der Versuch, die Authentifizierung zu authentifizieren, zu *argumumtexception: die Option "signinscheme" muss angegeben werden*.</span><span class="sxs-lookup"><span data-stu-id="6cafa-144">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="6cafa-145">Die in diesem Beispiel verwendete Projektvorlage stellt sicher, dass dies abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="6cafa-145">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="6cafa-146">Wenn die Standortdatenbank nicht durch Anwenden der anfänglichen Migration erstellt wurde, wird *bei der Verarbeitung des Anforderungs Fehlers ein Daten Bank Vorgang* nicht ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="6cafa-146">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="6cafa-147">Tippen Sie auf **Migrations anwenden** , um die Datenbank zu erstellen und zu aktualisieren, um den Fehler zu überschreiten.</span><span class="sxs-lookup"><span data-stu-id="6cafa-147">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="6cafa-148">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="6cafa-148">Next steps</span></span>

* <span data-ttu-id="6cafa-149">In diesem Artikel wurde gezeigt, wie Sie sich bei Microsoft authentifizieren können.</span><span class="sxs-lookup"><span data-stu-id="6cafa-149">This article showed how you can authenticate with Microsoft.</span></span> <span data-ttu-id="6cafa-150">Führen Sie einen ähnlichen Ansatz für die Authentifizierung mit anderen Anbietern aufgeführt, auf die [Vorgängerseite](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="6cafa-150">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="6cafa-151">Nachdem Sie die Website in der Azure-Web-App veröffentlicht haben, erstellen Sie im Microsoft-Entwickler Portal ein neues Client Geheimnis.</span><span class="sxs-lookup"><span data-stu-id="6cafa-151">Once you publish your web site to Azure web app, create a new client secrets in the Microsoft Developer Portal.</span></span>

* <span data-ttu-id="6cafa-152">Legen Sie die `Authentication:Microsoft:ClientId` und `Authentication:Microsoft:ClientSecret` Anwendungseinstellungen im Azure-Portal.</span><span class="sxs-lookup"><span data-stu-id="6cafa-152">Set the `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="6cafa-153">Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="6cafa-153">The configuration system is set up to read keys from environment variables.</span></span>

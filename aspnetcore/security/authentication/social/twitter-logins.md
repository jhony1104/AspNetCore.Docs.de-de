---
title: Externe Twitter-Anmelde Einrichtung mit ASP.net Core
author: rick-anderson
description: In diesem Tutorial wird die Integration der Authentifizierung von Twitter-Konto Benutzern in eine vorhandene ASP.net Core-App veranschaulicht.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2019
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/twitter-logins
ms.openlocfilehash: 5d0695160d90d0c5d31b8e35bc6c4cc984829333
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74944212"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a><span data-ttu-id="92b76-103">Externe Twitter-Anmelde Einrichtung mit ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="92b76-103">Twitter external sign-in setup with ASP.NET Core</span></span>

<span data-ttu-id="92b76-104">Von [Valeriy Novytskyy](https://github.com/01binary) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="92b76-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="92b76-105">Dieses Beispiel zeigt, wie Sie es Benutzern mithilfe eines auf der [vorherigen Seite](xref:security/authentication/social/index)erstellten Beispiels ASP.net Core 3,0-Projekts ermöglichen, sich [mit Ihrem Twitter-Konto anzumelden](https://dev.twitter.com/web/sign-in/desktop-browser) .</span><span class="sxs-lookup"><span data-stu-id="92b76-105">This sample shows how to enable users to [sign in with their Twitter account](https://dev.twitter.com/web/sign-in/desktop-browser) using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-twitter"></a><span data-ttu-id="92b76-106">Erstellen der app in Twitter</span><span class="sxs-lookup"><span data-stu-id="92b76-106">Create the app in Twitter</span></span>

* <span data-ttu-id="92b76-107">Fügen Sie dem Projekt das nuget-Paket [Microsoft. aspnetcore. Authentication. Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) hinzu.</span><span class="sxs-lookup"><span data-stu-id="92b76-107">Add the [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet package to the project.</span></span>

* <span data-ttu-id="92b76-108">Navigieren Sie zu [ https://apps.twitter.com/ ](https://apps.twitter.com/) und melden Sie sich.</span><span class="sxs-lookup"><span data-stu-id="92b76-108">Navigate to [https://apps.twitter.com/](https://apps.twitter.com/) and sign in.</span></span> <span data-ttu-id="92b76-109">Wenn Sie noch nicht über ein Twitter-Konto verfügen, verwenden Sie den Link **[jetzt registrieren](https://twitter.com/signup)** , um einen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="92b76-109">If you don't already have a Twitter account, use the **[Sign up now](https://twitter.com/signup)** link to create one.</span></span>

* <span data-ttu-id="92b76-110">Klicken Sie auf **App erstellen**.</span><span class="sxs-lookup"><span data-stu-id="92b76-110">Select **Create an app**.</span></span> <span data-ttu-id="92b76-111">Geben Sie den **APP-Namen**, die **Anwendungsbeschreibung** und den URI der öffentlichen **Website** an (Dies kann temporär sein, bis Sie den Domänen Namen registrieren):</span><span class="sxs-lookup"><span data-stu-id="92b76-111">Fill out the **App name**, **Application description** and public **Website** URI (this can be temporary until you register the domain name):</span></span>

* <span data-ttu-id="92b76-112">Geben Sie Ihren Entwicklungs-URI ein, wobei `/signin-twitter` an das Feld **Rückruf-URLs** angehängt ist (z. b.: `https://webapp128.azurewebsites.net/signin-twitter`).</span><span class="sxs-lookup"><span data-stu-id="92b76-112">Enter your development URI with `/signin-twitter` appended into the **Callback URLs** field (for example: `https://webapp128.azurewebsites.net/signin-twitter`).</span></span> <span data-ttu-id="92b76-113">Das Twitter-Authentifizierungsschema, das weiter unten in diesem Beispiel konfiguriert wird, verarbeitet automatisch Anforderungen auf `/signin-twitter` Route, um den OAuth-Fluss zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="92b76-113">The Twitter authentication scheme configured later in this sample will automatically handle requests at `/signin-twitter` route to implement the OAuth flow.</span></span>

  > [!NOTE]
  > <span data-ttu-id="92b76-114">Das URI-Segment `/signin-twitter` wird als Standard Rückruf des Twitter-Authentifizierungs Anbieters festgelegt.</span><span class="sxs-lookup"><span data-stu-id="92b76-114">The URI segment `/signin-twitter` is set as the default callback of the Twitter authentication provider.</span></span> <span data-ttu-id="92b76-115">Sie können den Standard-Rückruf-URI ändern, während Sie die Twitter-Authentifizierungs Middleware über die geerbte [remoteauthenticationoptions. callbackpath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) -Eigenschaft der [twitteroptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) -Klasse konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="92b76-115">You can change the default callback URI while configuring the Twitter authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) class.</span></span>

* <span data-ttu-id="92b76-116">Füllen Sie das restliche Formular aus, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="92b76-116">Fill out the rest of the form and select **Create**.</span></span> <span data-ttu-id="92b76-117">Es werden neue Anwendungsdetails angezeigt:</span><span class="sxs-lookup"><span data-stu-id="92b76-117">New application details are displayed:</span></span>

## <a name="storing-twitter-consumer-api-key-and-secret"></a><span data-ttu-id="92b76-118">Speichern von Twitter-Consumer-API-Schlüssel und-Geheimnis</span><span class="sxs-lookup"><span data-stu-id="92b76-118">Storing Twitter Consumer API key and secret</span></span>

<span data-ttu-id="92b76-119">Führen Sie die folgenden Befehle aus, um `ClientId` und `ClientSecret` mithilfe von [Secret Manager](xref:security/app-secrets)sicher zu speichern:</span><span class="sxs-lookup"><span data-stu-id="92b76-119">Run the following commands to securely store `ClientId` and `ClientSecret` using [Secret Manager](xref:security/app-secrets):</span></span>

```dotnetcli
dotnet user-secrets set Authentication:Twitter:ConsumerAPIKey <Key>
dotnet user-secrets set Authentication:Twitter:ConsumerSecret <Secret>
```

<span data-ttu-id="92b76-120">Verknüpfen Sie sensible Einstellungen wie Twitter `Consumer Key` und `Consumer Secret` mit ihrer Anwendungskonfiguration mithilfe des [geheimen Managers](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="92b76-120">Link sensitive settings like Twitter `Consumer Key` and `Consumer Secret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="92b76-121">Benennen Sie die Token für die Zwecke dieses Beispiels `Authentication:Twitter:ConsumerKey` und `Authentication:Twitter:ConsumerSecret`.</span><span class="sxs-lookup"><span data-stu-id="92b76-121">For the purposes of this sample, name the tokens `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret`.</span></span>

<span data-ttu-id="92b76-122">Diese Token finden Sie auf der Registerkarte **Schlüssel und Zugriffs Token** , nachdem Sie eine neue Twitter-Anwendung erstellt haben:</span><span class="sxs-lookup"><span data-stu-id="92b76-122">These tokens can be found on the **Keys and Access Tokens** tab after creating a new Twitter application:</span></span>

## <a name="configure-twitter-authentication"></a><span data-ttu-id="92b76-123">Konfigurieren der Twitter-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="92b76-123">Configure Twitter Authentication</span></span>

<span data-ttu-id="92b76-124">Fügen Sie den Twitter-Dienst in der `ConfigureServices`-Methode in der Datei *Startup.cs* hinzu:</span><span class="sxs-lookup"><span data-stu-id="92b76-124">Add the Twitter service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="92b76-125">Weitere Informationen zu von der Twitter-Authentifizierung unterstützten Konfigurationsoptionen finden Sie in der [twitteroptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) -API-Referenz.</span><span class="sxs-lookup"><span data-stu-id="92b76-125">See the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API reference for more information on configuration options supported by Twitter authentication.</span></span> <span data-ttu-id="92b76-126">Dies kann verwendet werden, um verschiedene Informationen über den Benutzer anzufordern.</span><span class="sxs-lookup"><span data-stu-id="92b76-126">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-twitter"></a><span data-ttu-id="92b76-127">Mit Twitter anmelden</span><span class="sxs-lookup"><span data-stu-id="92b76-127">Sign in with Twitter</span></span>

<span data-ttu-id="92b76-128">Führen Sie die APP aus, und wählen Sie **Anmelden aus**.</span><span class="sxs-lookup"><span data-stu-id="92b76-128">Run the app and select **Log in**.</span></span> <span data-ttu-id="92b76-129">Eine Option zum Anmelden bei Twitter wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="92b76-129">An option to sign in with Twitter appears:</span></span>

<span data-ttu-id="92b76-130">Beim Klicken auf **Twitter** wird die Authentifizierung an Twitter umgeleitet:</span><span class="sxs-lookup"><span data-stu-id="92b76-130">Clicking on **Twitter** redirects to Twitter for authentication:</span></span>

<span data-ttu-id="92b76-131">Nachdem Sie Ihre Twitter-Anmelde Informationen eingegeben haben, werden Sie zurück an die Website umgeleitet, auf der Sie Ihre e-Mail festlegen können.</span><span class="sxs-lookup"><span data-stu-id="92b76-131">After entering your Twitter credentials, you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="92b76-132">Sie sind jetzt mit Ihren Twitter-Anmelde Informationen angemeldet:</span><span class="sxs-lookup"><span data-stu-id="92b76-132">You are now logged in using your Twitter credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="92b76-133">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="92b76-133">Troubleshooting</span></span>

* <span data-ttu-id="92b76-134">**ASP.NET Core 2.x nur:** Wenn Identität ist nicht konfiguriert, durch den Aufruf `services.AddIdentity` in `ConfigureServices`, Authentifizierungsversuch führt zu *ArgumentException: die Option 'SignInScheme' muss angegeben werden*.</span><span class="sxs-lookup"><span data-stu-id="92b76-134">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="92b76-135">Die in diesem Beispiel verwendete Projektvorlage stellt sicher, dass dies abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="92b76-135">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="92b76-136">Wenn die Standortdatenbank nicht erstellt wurde, indem die ursprüngliche Migration anwenden, erhalten Sie *Fehler bei ein Datenbankvorgang beim Verarbeiten der Anforderung* Fehler.</span><span class="sxs-lookup"><span data-stu-id="92b76-136">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="92b76-137">Tippen Sie auf **Migrationen anwenden** der Datenbank zu erstellen und aktualisieren, um den Fehler hinaus fortgesetzt.</span><span class="sxs-lookup"><span data-stu-id="92b76-137">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="92b76-138">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="92b76-138">Next steps</span></span>

* <span data-ttu-id="92b76-139">In diesem Artikel wurde gezeigt, wie Sie sich mit Twitter authentifizieren können.</span><span class="sxs-lookup"><span data-stu-id="92b76-139">This article showed how you can authenticate with Twitter.</span></span> <span data-ttu-id="92b76-140">Führen Sie einen ähnlichen Ansatz für die Authentifizierung mit anderen Anbietern aufgeführt, auf die [Vorgängerseite](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="92b76-140">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="92b76-141">Nachdem Sie die Website in der Azure-Web-App veröffentlicht haben, sollten Sie die `ConsumerSecret` im Twitter-Entwickler Portal zurücksetzen.</span><span class="sxs-lookup"><span data-stu-id="92b76-141">Once you publish your web site to Azure web app, you should reset the `ConsumerSecret` in the Twitter developer portal.</span></span>

* <span data-ttu-id="92b76-142">Legen Sie die `Authentication:Twitter:ConsumerKey` und `Authentication:Twitter:ConsumerSecret` Anwendungseinstellungen im Azure-Portal.</span><span class="sxs-lookup"><span data-stu-id="92b76-142">Set the `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="92b76-143">Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="92b76-143">The configuration system is set up to read keys from environment variables.</span></span>

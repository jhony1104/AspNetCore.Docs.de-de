---
title: Externe Twitter-Anmelde Einrichtung mit ASP.net Core
author: rick-anderson
description: In diesem Tutorial wird die Integration der Authentifizierung von Twitter-Konto Benutzern in eine vorhandene ASP.net Core-App veranschaulicht.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2019
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/twitter-logins
ms.openlocfilehash: 4710c033018710ce3620f8d7221ae2253b2c0b69
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172526"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a><span data-ttu-id="33ec6-103">Externe Twitter-Anmelde Einrichtung mit ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="33ec6-103">Twitter external sign-in setup with ASP.NET Core</span></span>

<span data-ttu-id="33ec6-104">Von [Valeriy Novytskyy](https://github.com/01binary) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="33ec6-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="33ec6-105">Dieses Beispiel zeigt, wie Sie es Benutzern mithilfe eines auf der [vorherigen Seite](xref:security/authentication/social/index)erstellten Beispiels ASP.net Core 3,0-Projekts ermöglichen, sich [mit Ihrem Twitter-Konto anzumelden](https://dev.twitter.com/web/sign-in/desktop-browser) .</span><span class="sxs-lookup"><span data-stu-id="33ec6-105">This sample shows how to enable users to [sign in with their Twitter account](https://dev.twitter.com/web/sign-in/desktop-browser) using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-twitter"></a><span data-ttu-id="33ec6-106">Erstellen der app in Twitter</span><span class="sxs-lookup"><span data-stu-id="33ec6-106">Create the app in Twitter</span></span>

* <span data-ttu-id="33ec6-107">Fügen Sie dem Projekt das nuget-Paket [Microsoft. aspnetcore. Authentication. Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) hinzu.</span><span class="sxs-lookup"><span data-stu-id="33ec6-107">Add the [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet package to the project.</span></span>

* <span data-ttu-id="33ec6-108">Navigieren Sie zu [https://apps.twitter.com/](https://apps.twitter.com/) , und melden Sie sich an.</span><span class="sxs-lookup"><span data-stu-id="33ec6-108">Navigate to [https://apps.twitter.com/](https://apps.twitter.com/) and sign in.</span></span> <span data-ttu-id="33ec6-109">Wenn Sie noch nicht über ein Twitter-Konto verfügen, verwenden Sie den Link **[jetzt registrieren](https://twitter.com/signup)** , um einen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="33ec6-109">If you don't already have a Twitter account, use the **[Sign up now](https://twitter.com/signup)** link to create one.</span></span>

* <span data-ttu-id="33ec6-110">Wählen Sie **Create an app** (App erstellen) aus.</span><span class="sxs-lookup"><span data-stu-id="33ec6-110">Select **Create an app**.</span></span> <span data-ttu-id="33ec6-111">Geben Sie den **APP-Namen**, die **Anwendungsbeschreibung** und den URI der öffentlichen **Website** an (Dies kann temporär sein, bis Sie den Domänen Namen registrieren):</span><span class="sxs-lookup"><span data-stu-id="33ec6-111">Fill out the **App name**, **Application description** and public **Website** URI (this can be temporary until you register the domain name):</span></span>

* <span data-ttu-id="33ec6-112">Aktivieren Sie das Kontrollkästchen neben **Anmelden mit Twitter aktivieren** .</span><span class="sxs-lookup"><span data-stu-id="33ec6-112">Check the box next to **Enable Sign in with Twitter**</span></span>

* <span data-ttu-id="33ec6-113">Microsoft. aspnetcore. Identity erfordert, dass Benutzer standardmäßig über eine e-Mail-Adresse verfügen.</span><span class="sxs-lookup"><span data-stu-id="33ec6-113">Microsoft.AspNetCore.Identity requires users to have an email address by default.</span></span> <span data-ttu-id="33ec6-114">Wechseln Sie zur Registerkarte **Berechtigungen** , klicken Sie auf die Schaltfläche **Bearbeiten** , und aktivieren Sie das Kontrollkästchen neben **e-Mail-Adresse anfordern von Benutzern**</span><span class="sxs-lookup"><span data-stu-id="33ec6-114">Go to the **Permissions** tab, click the **Edit** button and check the box next to **Request email address from users**.</span></span>

* <span data-ttu-id="33ec6-115">Geben Sie Ihren Entwicklungs-URI ein, wobei `/signin-twitter` an das Feld **Rückruf-URLs** angehängt ist (z. b.: `https://webapp128.azurewebsites.net/signin-twitter`).</span><span class="sxs-lookup"><span data-stu-id="33ec6-115">Enter your development URI with `/signin-twitter` appended into the **Callback URLs** field (for example: `https://webapp128.azurewebsites.net/signin-twitter`).</span></span> <span data-ttu-id="33ec6-116">Das Twitter-Authentifizierungsschema, das weiter unten in diesem Beispiel konfiguriert wird, verarbeitet automatisch Anforderungen auf `/signin-twitter` Route, um den OAuth-Fluss zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="33ec6-116">The Twitter authentication scheme configured later in this sample will automatically handle requests at `/signin-twitter` route to implement the OAuth flow.</span></span>

  > [!NOTE]
  > <span data-ttu-id="33ec6-117">Das URI-Segment `/signin-twitter` wird als Standard Rückruf des Twitter-Authentifizierungs Anbieters festgelegt.</span><span class="sxs-lookup"><span data-stu-id="33ec6-117">The URI segment `/signin-twitter` is set as the default callback of the Twitter authentication provider.</span></span> <span data-ttu-id="33ec6-118">Sie können den Standard-Rückruf-URI ändern, während Sie die Twitter-Authentifizierungs Middleware über die geerbte [remoteauthenticationoptions. callbackpath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) -Eigenschaft der [twitteroptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) -Klasse konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="33ec6-118">You can change the default callback URI while configuring the Twitter authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) class.</span></span>

* <span data-ttu-id="33ec6-119">Füllen Sie das restliche Formular aus, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="33ec6-119">Fill out the rest of the form and select **Create**.</span></span> <span data-ttu-id="33ec6-120">Es werden neue Anwendungsdetails angezeigt:</span><span class="sxs-lookup"><span data-stu-id="33ec6-120">New application details are displayed:</span></span>

## <a name="storing-twitter-consumer-api-key-and-secret"></a><span data-ttu-id="33ec6-121">Speichern von Twitter-Consumer-API-Schlüssel und-Geheimnis</span><span class="sxs-lookup"><span data-stu-id="33ec6-121">Storing Twitter Consumer API key and secret</span></span>

<span data-ttu-id="33ec6-122">Führen Sie die folgenden Befehle aus, um `ClientId` und `ClientSecret` mithilfe von [Secret Manager](xref:security/app-secrets)sicher zu speichern:</span><span class="sxs-lookup"><span data-stu-id="33ec6-122">Run the following commands to securely store `ClientId` and `ClientSecret` using [Secret Manager](xref:security/app-secrets):</span></span>

```dotnetcli
dotnet user-secrets set Authentication:Twitter:ConsumerAPIKey <Key>
dotnet user-secrets set Authentication:Twitter:ConsumerSecret <Secret>
```

<span data-ttu-id="33ec6-123">Verknüpfen Sie sensible Einstellungen wie Twitter `Consumer Key` und `Consumer Secret` mit ihrer Anwendungskonfiguration mithilfe des [geheimen Managers](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="33ec6-123">Link sensitive settings like Twitter `Consumer Key` and `Consumer Secret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="33ec6-124">Benennen Sie die Token für die Zwecke dieses Beispiels `Authentication:Twitter:ConsumerKey` und `Authentication:Twitter:ConsumerSecret`.</span><span class="sxs-lookup"><span data-stu-id="33ec6-124">For the purposes of this sample, name the tokens `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret`.</span></span>

<span data-ttu-id="33ec6-125">Diese Token finden Sie auf der Registerkarte **Schlüssel und Zugriffs Token** , nachdem Sie eine neue Twitter-Anwendung erstellt haben:</span><span class="sxs-lookup"><span data-stu-id="33ec6-125">These tokens can be found on the **Keys and Access Tokens** tab after creating a new Twitter application:</span></span>

## <a name="configure-twitter-authentication"></a><span data-ttu-id="33ec6-126">Konfigurieren der Twitter-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="33ec6-126">Configure Twitter Authentication</span></span>

<span data-ttu-id="33ec6-127">Fügen Sie den Twitter-Dienst in der `ConfigureServices`-Methode in der Datei *Startup.cs* hinzu:</span><span class="sxs-lookup"><span data-stu-id="33ec6-127">Add the Twitter service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="33ec6-128">Weitere Informationen zu von der Twitter-Authentifizierung unterstützten Konfigurationsoptionen finden Sie in der [twitteroptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) -API-Referenz.</span><span class="sxs-lookup"><span data-stu-id="33ec6-128">See the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API reference for more information on configuration options supported by Twitter authentication.</span></span> <span data-ttu-id="33ec6-129">Dies kann verwendet werden, um verschiedene Informationen über den Benutzer anzufordern.</span><span class="sxs-lookup"><span data-stu-id="33ec6-129">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-twitter"></a><span data-ttu-id="33ec6-130">Mit Twitter anmelden</span><span class="sxs-lookup"><span data-stu-id="33ec6-130">Sign in with Twitter</span></span>

<span data-ttu-id="33ec6-131">Führen Sie die APP aus, und wählen Sie **Anmelden aus**.</span><span class="sxs-lookup"><span data-stu-id="33ec6-131">Run the app and select **Log in**.</span></span> <span data-ttu-id="33ec6-132">Eine Option zum Anmelden bei Twitter wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="33ec6-132">An option to sign in with Twitter appears:</span></span>

<span data-ttu-id="33ec6-133">Beim Klicken auf **Twitter** wird die Authentifizierung an Twitter umgeleitet:</span><span class="sxs-lookup"><span data-stu-id="33ec6-133">Clicking on **Twitter** redirects to Twitter for authentication:</span></span>

<span data-ttu-id="33ec6-134">Nachdem Sie Ihre Twitter-Anmelde Informationen eingegeben haben, werden Sie zurück an die Website umgeleitet, auf der Sie Ihre e-Mail festlegen können.</span><span class="sxs-lookup"><span data-stu-id="33ec6-134">After entering your Twitter credentials, you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="33ec6-135">Sie sind jetzt mit Ihren Twitter-Anmelde Informationen angemeldet:</span><span class="sxs-lookup"><span data-stu-id="33ec6-135">You are now logged in using your Twitter credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="33ec6-136">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="33ec6-136">Troubleshooting</span></span>

* <span data-ttu-id="33ec6-137">**Nur ASP.net Core 2. x:** Wenn die Identität nicht durch Aufrufen von `services.AddIdentity` in `ConfigureServices`konfiguriert wird, führt der Versuch, die Authentifizierung zu authentifizieren, zu *argumumtexception: die Option "signinscheme" muss angegeben werden*.</span><span class="sxs-lookup"><span data-stu-id="33ec6-137">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="33ec6-138">Die in diesem Beispiel verwendete Projektvorlage stellt sicher, dass dies abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="33ec6-138">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="33ec6-139">Wenn die Standortdatenbank nicht durch Anwenden der anfänglichen Migration erstellt wurde, wird *bei der Verarbeitung des Anforderungs Fehlers ein Daten Bank Vorgang* nicht ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="33ec6-139">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="33ec6-140">Tippen Sie auf **Migrations anwenden** , um die Datenbank zu erstellen und zu aktualisieren, um den Fehler zu überschreiten.</span><span class="sxs-lookup"><span data-stu-id="33ec6-140">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="33ec6-141">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="33ec6-141">Next steps</span></span>

* <span data-ttu-id="33ec6-142">In diesem Artikel wurde gezeigt, wie Sie sich mit Twitter authentifizieren können.</span><span class="sxs-lookup"><span data-stu-id="33ec6-142">This article showed how you can authenticate with Twitter.</span></span> <span data-ttu-id="33ec6-143">Führen Sie einen ähnlichen Ansatz für die Authentifizierung mit anderen Anbietern aufgeführt, auf die [Vorgängerseite](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="33ec6-143">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="33ec6-144">Nachdem Sie die Website in der Azure-Web-App veröffentlicht haben, sollten Sie die `ConsumerSecret` im Twitter-Entwickler Portal zurücksetzen.</span><span class="sxs-lookup"><span data-stu-id="33ec6-144">Once you publish your web site to Azure web app, you should reset the `ConsumerSecret` in the Twitter developer portal.</span></span>

* <span data-ttu-id="33ec6-145">Legen Sie die `Authentication:Twitter:ConsumerKey` und `Authentication:Twitter:ConsumerSecret` Anwendungseinstellungen im Azure-Portal.</span><span class="sxs-lookup"><span data-stu-id="33ec6-145">Set the `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="33ec6-146">Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="33ec6-146">The configuration system is set up to read keys from environment variables.</span></span>

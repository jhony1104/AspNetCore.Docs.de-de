---
title: Twitter-externen Anmeldung Setup mit ASP.NET Core
author: rick-anderson
description: Dieses Tutorial veranschaulicht die Integration von Twitter-Konto der Benutzerauthentifizierung in eine vorhandene ASP.NET Core-app.
ms.author: riande
ms.custom: mvc
ms.date: 5/11/2019
uid: security/authentication/twitter-logins
ms.openlocfilehash: 486d58b600ca5326a0728de40bb386fbb9440f67
ms.sourcegitcommit: 3376f224b47a89acf329b2d2f9260046a372f924
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/10/2019
ms.locfileid: "65516884"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a><span data-ttu-id="5d9c6-103">Twitter-externen Anmeldung Setup mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5d9c6-103">Twitter external sign-in setup with ASP.NET Core</span></span>

<span data-ttu-id="5d9c6-104">Von [Valeriy Novytskyy](https://github.com/01binary) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5d9c6-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="5d9c6-105">In diesem Beispiel wird gezeigt, wie Sie es Benutzern ermöglichen [melden Sie sich mit ihrem Twitter-Konto](https://dev.twitter.com/web/sign-in/desktop-browser) erstellt mit einem ASP.NET Core-2.2-Beispielprojekt die [Vorgängerseite](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="5d9c6-105">This sample shows how to enable users to [sign in with their Twitter account](https://dev.twitter.com/web/sign-in/desktop-browser) using a sample ASP.NET Core 2.2 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-twitter"></a><span data-ttu-id="5d9c6-106">Erstellen einer app in Twitter</span><span class="sxs-lookup"><span data-stu-id="5d9c6-106">Create the app in Twitter</span></span>

* <span data-ttu-id="5d9c6-107">Navigieren Sie zu [ https://apps.twitter.com/ ](https://apps.twitter.com/) und melden Sie sich.</span><span class="sxs-lookup"><span data-stu-id="5d9c6-107">Navigate to [https://apps.twitter.com/](https://apps.twitter.com/) and sign in.</span></span> <span data-ttu-id="5d9c6-108">Wenn Sie noch kein Twitter-Konto besitzen, verwenden Sie die **[registrieren Sie sich jetzt](https://twitter.com/signup)** Link zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="5d9c6-108">If you don't already have a Twitter account, use the **[Sign up now](https://twitter.com/signup)** link to create one.</span></span>

* <span data-ttu-id="5d9c6-109">Tippen Sie auf **Create New App** , und füllen Sie die Anwendung **Namen**, **Beschreibung** und öffentliche **Website** URI (Dies kann temporär sein bis Registrieren Sie den Domänennamen):</span><span class="sxs-lookup"><span data-stu-id="5d9c6-109">Tap **Create New App** and fill out the application **Name**, **Description** and public **Website** URI (this can be temporary until you register the domain name):</span></span>

* <span data-ttu-id="5d9c6-110">Geben Sie Ihre Entwicklung URI mit `/signin-twitter` angefügt, die in der **gültige OAuth-Umleitungs-URIs** Feld (z. B.: `https://webapp128.azurewebsites.net/signin-twitter`).</span><span class="sxs-lookup"><span data-stu-id="5d9c6-110">Enter your development URI with `/signin-twitter` appended into the **Valid OAuth Redirect URIs** field (for example: `https://webapp128.azurewebsites.net/signin-twitter`).</span></span> <span data-ttu-id="5d9c6-111">Das Twitter-Authentifizierungsschema, das weiter unten in diesem Beispiel konfiguriert übernimmt automatisch die Anforderungen an `/signin-twitter` Route zum Implementieren von OAuth-Fluss.</span><span class="sxs-lookup"><span data-stu-id="5d9c6-111">The Twitter authentication scheme configured later in this sample will automatically handle requests at `/signin-twitter` route to implement the OAuth flow.</span></span>

  > [!NOTE]
  > <span data-ttu-id="5d9c6-112">Der URI-Segment `/signin-twitter` ist als der standardrückruf des Authentifizierungsanbieters Twitter festgelegt.</span><span class="sxs-lookup"><span data-stu-id="5d9c6-112">The URI segment `/signin-twitter` is set as the default callback of the Twitter authentication provider.</span></span> <span data-ttu-id="5d9c6-113">Sie können den standardrückruf-URI ändern, während der Konfiguration der Twitter-Authentifizierung-Middleware über die geerbte [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) Eigenschaft der [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) -Klasse.</span><span class="sxs-lookup"><span data-stu-id="5d9c6-113">You can change the default callback URI while configuring the Twitter authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) class.</span></span>

* <span data-ttu-id="5d9c6-114">Geben Sie im Rest des Formulars, und tippen Sie auf **Erstellen Ihrer Twitter-Anwendung**.</span><span class="sxs-lookup"><span data-stu-id="5d9c6-114">Fill out the rest of the form and tap **Create your Twitter application**.</span></span> <span data-ttu-id="5d9c6-115">Die Anwendungsdetails der neuen werden angezeigt:</span><span class="sxs-lookup"><span data-stu-id="5d9c6-115">New application details are displayed:</span></span>

## <a name="storing-twitter-consumer-api-key-and-secret"></a><span data-ttu-id="5d9c6-116">Das Speichern von Twitter-Consumer-API-Schlüssel und geheimen Schlüssel</span><span class="sxs-lookup"><span data-stu-id="5d9c6-116">Storing Twitter Consumer API key and secret</span></span>

<span data-ttu-id="5d9c6-117">Führen Sie die folgenden Befehle zum sicheren Speichern von `ClientId` und `ClientSecret` mit [Secret Manager](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="5d9c6-117">Run the following commands to securely store `ClientId` and `ClientSecret` using [Secret Manager](xref:security/app-secrets):</span></span>

```console
dotnet user-secrets set Authentication:Twitter:ConsumerAPIKey <Key>
dotnet user-secrets set Authentication:Twitter:ConsumerAPISecret <Secret>
```

<span data-ttu-id="5d9c6-118">Verknüpfen Sie sensible Einstellungen wie Twitter `Consumer Key` und `Consumer Secret` zu Ihrer Anwendungskonfigurationsdatei mithilfe der [Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="5d9c6-118">Link sensitive settings like Twitter `Consumer Key` and `Consumer Secret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="5d9c6-119">Benennen Sie im Rahmen dieses Beispiels werden die Token `Authentication:Twitter:ConsumerKey` und `Authentication:Twitter:ConsumerSecret`.</span><span class="sxs-lookup"><span data-stu-id="5d9c6-119">For the purposes of this sample, name the tokens `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret`.</span></span>

<span data-ttu-id="5d9c6-120">Diese Token finden Sie auf die **Keys and Access Tokens** Registerkarte nach dem Erstellen einer neuen Twitter-Anwendung:</span><span class="sxs-lookup"><span data-stu-id="5d9c6-120">These tokens can be found on the **Keys and Access Tokens** tab after creating a new Twitter application:</span></span>

## <a name="configure-twitter-authentication"></a><span data-ttu-id="5d9c6-121">Konfigurieren der Twitter-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="5d9c6-121">Configure Twitter Authentication</span></span>

<span data-ttu-id="5d9c6-122">Fügen Sie den Twitter-Dienst in der `ConfigureServices` -Methode in der *"Startup.cs"* Datei:</span><span class="sxs-lookup"><span data-stu-id="5d9c6-122">Add the Twitter service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupTwitter.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="5d9c6-123">Finden Sie unter den [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API-Referenz für Weitere Informationen zu den Konfigurationsoptionen, die von Twitter-Authentifizierung unterstützt.</span><span class="sxs-lookup"><span data-stu-id="5d9c6-123">See the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API reference for more information on configuration options supported by Twitter authentication.</span></span> <span data-ttu-id="5d9c6-124">Dies kann verwendet werden, um verschiedene Informationen über den Benutzer anzufordern.</span><span class="sxs-lookup"><span data-stu-id="5d9c6-124">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-twitter"></a><span data-ttu-id="5d9c6-125">Melden Sie sich mit Twitter</span><span class="sxs-lookup"><span data-stu-id="5d9c6-125">Sign in with Twitter</span></span>

<span data-ttu-id="5d9c6-126">Führen Sie die app, und wählen Sie **melden Sie sich bei**.</span><span class="sxs-lookup"><span data-stu-id="5d9c6-126">Run the app and select **Log in**.</span></span> <span data-ttu-id="5d9c6-127">Eine Option aus, um sich mit Twitter anmelden wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="5d9c6-127">An option to sign in with Twitter appears:</span></span>

<span data-ttu-id="5d9c6-128">Durch Klicken auf **Twitter** an Twitter umgeleitet wird, für die Authentifizierung:</span><span class="sxs-lookup"><span data-stu-id="5d9c6-128">Clicking on **Twitter** redirects to Twitter for authentication:</span></span>

<span data-ttu-id="5d9c6-129">Geben Sie Ihre Twitter-Anmeldeinformationen, werden Sie zurück zur Website weitergeleitet, in dem Sie Ihre e-Mail-Adresse festlegen können.</span><span class="sxs-lookup"><span data-stu-id="5d9c6-129">After entering your Twitter credentials, you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="5d9c6-130">Sie sind jetzt angemeldet, sich mit Ihren Twitter-Anmeldeinformationen:</span><span class="sxs-lookup"><span data-stu-id="5d9c6-130">You are now logged in using your Twitter credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="5d9c6-131">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="5d9c6-131">Troubleshooting</span></span>

* <span data-ttu-id="5d9c6-132">**ASP.NET Core 2.x nur:** Wenn Identität nicht, durch den Aufruf konfiguriert ist `services.AddIdentity` in `ConfigureServices`, Authentifizierungsversuch führt zu *ArgumentException: Die Option 'SignInScheme' muss angegeben werden*.</span><span class="sxs-lookup"><span data-stu-id="5d9c6-132">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="5d9c6-133">Die Projektvorlage, die in diesem Beispiel verwendete wird sichergestellt, dass dies geschehen ist.</span><span class="sxs-lookup"><span data-stu-id="5d9c6-133">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="5d9c6-134">Wenn die Standortdatenbank nicht erstellt wurde, indem die ursprüngliche Migration anwenden, erhalten Sie *Fehler bei ein Datenbankvorgang beim Verarbeiten der Anforderung* Fehler.</span><span class="sxs-lookup"><span data-stu-id="5d9c6-134">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="5d9c6-135">Tippen Sie auf **Migrationen anwenden** der Datenbank zu erstellen und aktualisieren, um den Fehler hinaus fortgesetzt.</span><span class="sxs-lookup"><span data-stu-id="5d9c6-135">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="5d9c6-136">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="5d9c6-136">Next steps</span></span>

* <span data-ttu-id="5d9c6-137">In diesem Artikel wurde gezeigt, wie Sie Twitter authentifizieren können.</span><span class="sxs-lookup"><span data-stu-id="5d9c6-137">This article showed how you can authenticate with Twitter.</span></span> <span data-ttu-id="5d9c6-138">Führen Sie einen ähnlichen Ansatz für die Authentifizierung mit anderen Anbietern aufgeführt, auf die [Vorgängerseite](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="5d9c6-138">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="5d9c6-139">Nachdem Sie Ihre Website in Azure-Web-app veröffentlichen, sollten Sie Zurücksetzen der `ConsumerSecret` im Twitter-Entwicklerportal.</span><span class="sxs-lookup"><span data-stu-id="5d9c6-139">Once you publish your web site to Azure web app, you should reset the `ConsumerSecret` in the Twitter developer portal.</span></span>

* <span data-ttu-id="5d9c6-140">Legen Sie die `Authentication:Twitter:ConsumerKey` und `Authentication:Twitter:ConsumerSecret` Anwendungseinstellungen im Azure-Portal.</span><span class="sxs-lookup"><span data-stu-id="5d9c6-140">Set the `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="5d9c6-141">Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="5d9c6-141">The configuration system is set up to read keys from environment variables.</span></span>
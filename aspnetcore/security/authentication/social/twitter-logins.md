---
title: Externe S-Anmeldung von Twitter mit ASP.NET Core
author: rick-anderson
description: In diesem Tutorial wird die Integration der Benutzerauthentifizierung des Twitter-Kontos in eine vorhandene ASP.NET Core-App veranschaulicht.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/twitter-logins
ms.openlocfilehash: 1f5d667e905e49ae05f5aa31bd5b69ad126f6e28
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277287"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a><span data-ttu-id="15652-103">Externe S-Anmeldung von Twitter mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="15652-103">Twitter external sign-in setup with ASP.NET Core</span></span>

<span data-ttu-id="15652-104">Von [Valeriy Novytskyy](https://github.com/01binary) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="15652-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="15652-105">Dieses Beispiel zeigt, wie Benutzer sich [mit ihrem Twitter-Konto](https://dev.twitter.com/web/sign-in/desktop-browser) mit einem Beispiel ASP.NET Core 3.0-Projekt anmelden können, das auf der [vorherigen Seite](xref:security/authentication/social/index)erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="15652-105">This sample shows how to enable users to [sign in with their Twitter account](https://dev.twitter.com/web/sign-in/desktop-browser) using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-twitter"></a><span data-ttu-id="15652-106">Erstellen der App in Twitter</span><span class="sxs-lookup"><span data-stu-id="15652-106">Create the app in Twitter</span></span>

* <span data-ttu-id="15652-107">Fügen Sie dem Projekt das [Paket Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet hinzu.</span><span class="sxs-lookup"><span data-stu-id="15652-107">Add the [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet package to the project.</span></span>

* <span data-ttu-id="15652-108">Navigieren [https://apps.twitter.com/](https://apps.twitter.com/) Sie zu und melden Sie sich an.</span><span class="sxs-lookup"><span data-stu-id="15652-108">Navigate to [https://apps.twitter.com/](https://apps.twitter.com/) and sign in.</span></span> <span data-ttu-id="15652-109">Wenn du noch kein Twitter-Konto hast, verwende den Link **[Jetzt anmelden,](https://twitter.com/signup)** um eines zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="15652-109">If you don't already have a Twitter account, use the **[Sign up now](https://twitter.com/signup)** link to create one.</span></span>

* <span data-ttu-id="15652-110">Wählen **Sie App erstellen**aus.</span><span class="sxs-lookup"><span data-stu-id="15652-110">Select **Create an app**.</span></span> <span data-ttu-id="15652-111">Füllen Sie den **App-Namen**, **die Anwendungsbeschreibung** und den öffentlichen **Website-URI** aus (dies kann vorübergehend sein, bis Sie den Domänennamen registrieren):</span><span class="sxs-lookup"><span data-stu-id="15652-111">Fill out the **App name**, **Application description** and public **Website** URI (this can be temporary until you register the domain name):</span></span>

* <span data-ttu-id="15652-112">Aktivieren Sie das Kontrollkästchen neben **Anmelden bei Twitter aktivieren**</span><span class="sxs-lookup"><span data-stu-id="15652-112">Check the box next to **Enable Sign in with Twitter**</span></span>

* <span data-ttu-id="15652-113">Microsoft.AspNetCore.Identity erfordert standardmäßig eine E-Mail-Adresse für Benutzer.</span><span class="sxs-lookup"><span data-stu-id="15652-113">Microsoft.AspNetCore.Identity requires users to have an email address by default.</span></span> <span data-ttu-id="15652-114">Wechseln Sie zur Registerkarte **Berechtigungen,** klicken Sie auf die Schaltfläche **Bearbeiten,** und aktivieren Sie das Kontrollkästchen neben **E-Mail-Adresse von Benutzern anfordern**.</span><span class="sxs-lookup"><span data-stu-id="15652-114">Go to the **Permissions** tab, click the **Edit** button and check the box next to **Request email address from users**.</span></span>

* <span data-ttu-id="15652-115">Geben Sie Ihren `/signin-twitter` Entwicklungs-URI mit Anfügt in das `https://webapp128.azurewebsites.net/signin-twitter`Feld **Rückruf-URLs** ein (z. B.: ).</span><span class="sxs-lookup"><span data-stu-id="15652-115">Enter your development URI with `/signin-twitter` appended into the **Callback URLs** field (for example: `https://webapp128.azurewebsites.net/signin-twitter`).</span></span> <span data-ttu-id="15652-116">Das später in diesem Beispiel konfigurierte Twitter-Authentifizierungsschema verarbeitet automatisch Anforderungen auf der `/signin-twitter` Route, um den OAuth-Fluss zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="15652-116">The Twitter authentication scheme configured later in this sample will automatically handle requests at `/signin-twitter` route to implement the OAuth flow.</span></span>

  > [!NOTE]
  > <span data-ttu-id="15652-117">Das URI-Segment `/signin-twitter` wird als Standardrückruf des Twitter-Authentifizierungsanbieters festgelegt.</span><span class="sxs-lookup"><span data-stu-id="15652-117">The URI segment `/signin-twitter` is set as the default callback of the Twitter authentication provider.</span></span> <span data-ttu-id="15652-118">Sie können den Standardrückruf-URI ändern, während Sie die Twitter-Authentifizierungs-Middleware über die geerbte [RemoteAuthenticationOptions.CallbackPath-Eigenschaft](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) der [TwitterOptions-Klasse](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="15652-118">You can change the default callback URI while configuring the Twitter authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) class.</span></span>

* <span data-ttu-id="15652-119">Füllen Sie den Rest des Formulars aus, und wählen Sie **Erstellen**aus.</span><span class="sxs-lookup"><span data-stu-id="15652-119">Fill out the rest of the form and select **Create**.</span></span> <span data-ttu-id="15652-120">Neue Anwendungsdetails werden angezeigt:</span><span class="sxs-lookup"><span data-stu-id="15652-120">New application details are displayed:</span></span>

## <a name="store-the-twitter-consumer-api-key-and-secret"></a><span data-ttu-id="15652-121">Speichern des Twitter-Verbraucher-API-Schlüssels und des geheimen</span><span class="sxs-lookup"><span data-stu-id="15652-121">Store the Twitter consumer API key and secret</span></span>

<span data-ttu-id="15652-122">Speichern Sie vertrauliche Einstellungen wie den Twitter-Verbraucher-API-Schlüssel und den geheimen Schlüssel mit [Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="15652-122">Store sensitive settings such as the Twitter consumer API key and secret with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="15652-123">Gehen Sie für dieses Beispiel wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="15652-123">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="15652-124">Initialisieren Sie das Projekt für die geheime Speicherung gemäß den Anweisungen unter [Geheime Speicherung aktivieren](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="15652-124">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="15652-125">Speichern Sie die vertraulichen Einstellungen im `Authentication:Twitter:ConsumerKey` lokalen `Authentication:Twitter:ConsumerSecret`geheimen Speicher mit den geheimen Schlüsseln und:</span><span class="sxs-lookup"><span data-stu-id="15652-125">Store the sensitive settings in the local secret store with the secrets keys `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="15652-126">Diese Token finden Sie auf der Registerkarte **Schlüssel und Zugriffstoken,** nachdem Sie eine neue Twitter-Anwendung erstellt haben:</span><span class="sxs-lookup"><span data-stu-id="15652-126">These tokens can be found on the **Keys and Access Tokens** tab after creating a new Twitter application:</span></span>

## <a name="configure-twitter-authentication"></a><span data-ttu-id="15652-127">Konfigurieren der Twitter-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="15652-127">Configure Twitter Authentication</span></span>

<span data-ttu-id="15652-128">Fügen Sie den `ConfigureServices` Twitter-Dienst in der Methode in *Startup.cs* Datei hinzu:</span><span class="sxs-lookup"><span data-stu-id="15652-128">Add the Twitter service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="15652-129">Weitere [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) Informationen zu Konfigurationsoptionen, die von der Twitter-Authentifizierung unterstützt werden, finden Sie in der TwitterOptions-API-Referenz.</span><span class="sxs-lookup"><span data-stu-id="15652-129">See the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API reference for more information on configuration options supported by Twitter authentication.</span></span> <span data-ttu-id="15652-130">Dies kann verwendet werden, um verschiedene Informationen über den Benutzer anzufordern.</span><span class="sxs-lookup"><span data-stu-id="15652-130">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-twitter"></a><span data-ttu-id="15652-131">Melden Sie sich mit Twitter an</span><span class="sxs-lookup"><span data-stu-id="15652-131">Sign in with Twitter</span></span>

<span data-ttu-id="15652-132">Führen Sie die App aus, und wählen Sie **Anmelden**aus.</span><span class="sxs-lookup"><span data-stu-id="15652-132">Run the app and select **Log in**.</span></span> <span data-ttu-id="15652-133">Es wird eine Option zur Anmeldung bei Twitter angezeigt:</span><span class="sxs-lookup"><span data-stu-id="15652-133">An option to sign in with Twitter appears:</span></span>

<span data-ttu-id="15652-134">Ein Klick auf **Twitter** leitet zur Authentifizierung zu Twitter um:</span><span class="sxs-lookup"><span data-stu-id="15652-134">Clicking on **Twitter** redirects to Twitter for authentication:</span></span>

<span data-ttu-id="15652-135">Nachdem Sie Ihre Twitter-Anmeldeinformationen eingegeben haben, werden Sie zurück zur Website weitergeleitet, auf der Sie Ihre E-Mail-Adresse festlegen können.</span><span class="sxs-lookup"><span data-stu-id="15652-135">After entering your Twitter credentials, you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="15652-136">Sie sind jetzt mit Ihren Twitter-Anmeldeinformationen angemeldet:</span><span class="sxs-lookup"><span data-stu-id="15652-136">You are now logged in using your Twitter credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

<!-- 
### React to cancel Authorize External sign-in
Twitter doesn't support AccessDeniedPath
Rather in the twitter setup, you can provide an External sign-in homepage. The external sign-in homepage doesn't support localhost. Tested with https://cors3.azurewebsites.net/ and that works.
-->

## <a name="troubleshooting"></a><span data-ttu-id="15652-137">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="15652-137">Troubleshooting</span></span>

* <span data-ttu-id="15652-138">**ASP.NET Core 2.x nur:** Wenn Identity nicht durch Aufrufen `services.AddIdentity` konfiguriert `ConfigureServices`ist, führt der Versuch, sich zu authentifizieren, zu *ArgumentException: Die Option 'SignInScheme' muss bereitgestellt werden.*</span><span class="sxs-lookup"><span data-stu-id="15652-138">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="15652-139">Die in diesem Beispiel verwendete Projektvorlage stellt sicher, dass dies geschieht.</span><span class="sxs-lookup"><span data-stu-id="15652-139">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="15652-140">Wenn die Standortdatenbank nicht durch Anwenden der ersten Migration erstellt wurde, wird bei der Verarbeitung des Anforderungsfehlers *ein Datenbankvorgang fehlgeschlagen.*</span><span class="sxs-lookup"><span data-stu-id="15652-140">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="15652-141">Tippen Sie auf **Migrations anwenden,** um die Datenbank zu erstellen und zu aktualisieren, um nach dem Fehler fortzufahren.</span><span class="sxs-lookup"><span data-stu-id="15652-141">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="15652-142">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="15652-142">Next steps</span></span>

* <span data-ttu-id="15652-143">Dieser Artikel zeigte, wie Sie sich bei Twitter authentifizieren können.</span><span class="sxs-lookup"><span data-stu-id="15652-143">This article showed how you can authenticate with Twitter.</span></span> <span data-ttu-id="15652-144">Führen Sie einen ähnlichen Ansatz für die Authentifizierung mit anderen Anbietern aufgeführt, auf die [Vorgängerseite](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="15652-144">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="15652-145">Nachdem Sie Ihre Website in der Azure-Web-App veröffentlicht haben, sollten Sie die `ConsumerSecret` im Twitter-Entwicklerportal zurücksetzen.</span><span class="sxs-lookup"><span data-stu-id="15652-145">Once you publish your web site to Azure web app, you should reset the `ConsumerSecret` in the Twitter developer portal.</span></span>

* <span data-ttu-id="15652-146">Legen Sie die `Authentication:Twitter:ConsumerKey` und `Authentication:Twitter:ConsumerSecret` Anwendungseinstellungen im Azure-Portal.</span><span class="sxs-lookup"><span data-stu-id="15652-146">Set the `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="15652-147">Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="15652-147">The configuration system is set up to read keys from environment variables.</span></span>

---
title: Facebook externe Anmeldung Setup in ASP.NET Core
author: rick-anderson
description: Tutorial mit Codebeispielen, die die Integration der Authentifizierung von Facebook-Konto Benutzern in eine vorhandene ASP.net Core-App veranschaulichen.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/facebook-logins
ms.openlocfilehash: bb26a27f026e744c7d4925aa2281bf0625fff8a2
ms.sourcegitcommit: 9b6e7f421c243963d5e419bdcfc5c4bde71499aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/21/2020
ms.locfileid: "79989778"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a><span data-ttu-id="efa81-103">Facebook externe Anmeldung Setup in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="efa81-103">Facebook external login setup in ASP.NET Core</span></span>

<span data-ttu-id="efa81-104">Von [Valeriy Novytskyy](https://github.com/01binary) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="efa81-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="efa81-105">In diesem Tutorial mit Codebeispielen wird veranschaulicht, wie Sie es Ihren Benutzern ermöglichen können, sich mit Ihrem Facebook-Konto zu anmelden, indem Sie ein Beispiel ASP.net Core auf der [vorherigen Seite](xref:security/authentication/social/index)erstellten Beispiel Projekt 3,0.</span><span class="sxs-lookup"><span data-stu-id="efa81-105">This tutorial with code examples shows how to enable your users to sign in with their Facebook account using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span> <span data-ttu-id="efa81-106">Wir beginnen damit, eine Facebook-APP-ID zu erstellen, indem wir die [offiziellen Schritte](https://developers.facebook.com)befolgen.</span><span class="sxs-lookup"><span data-stu-id="efa81-106">We start by creating a Facebook App ID by following the [official steps](https://developers.facebook.com).</span></span>

## <a name="create-the-app-in-facebook"></a><span data-ttu-id="efa81-107">Erstellen einer app in Facebook</span><span class="sxs-lookup"><span data-stu-id="efa81-107">Create the app in Facebook</span></span>

* <span data-ttu-id="efa81-108">Fügen Sie das nuget-Paket [Microsoft. aspnetcore. Authentication. Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) zum Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="efa81-108">Add the [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet package to the project.</span></span>

* <span data-ttu-id="efa81-109">Navigieren Sie zur Seite [Facebook-Entwickler-App](https://developers.facebook.com/apps/) , und melden Sie sich an.</span><span class="sxs-lookup"><span data-stu-id="efa81-109">Navigate to the [Facebook Developers app](https://developers.facebook.com/apps/) page and sign in.</span></span> <span data-ttu-id="efa81-110">Wenn Sie noch nicht über ein Facebook-Konto verfügen, verwenden Sie den Link **für Facebook registrieren** auf der Anmeldeseite, um einen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="efa81-110">If you don't already have a Facebook account, use the **Sign up for Facebook** link on the login page to create one.</span></span>  <span data-ttu-id="efa81-111">Wenn Sie über ein Facebook-Konto verfügen, befolgen Sie die Anweisungen, um sich als Facebook-Entwickler zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="efa81-111">Once you have a Facebook account, follow the instructions to register as a Facebook Developer.</span></span>

* <span data-ttu-id="efa81-112">Wählen Sie im Menü **meine apps** die Option **app erstellen** aus, um eine neue APP-ID zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="efa81-112">From the **My Apps** menu select **Create App** to create a new App ID.</span></span>

   ![Facebook Entwicklerportal in Microsoft Edge geöffnete](index/_static/FBMyApps.png)

* <span data-ttu-id="efa81-114">Füllen Sie das Formular aus, und tippen Sie auf die Schaltfläche **App-ID erstellen** .</span><span class="sxs-lookup"><span data-stu-id="efa81-114">Fill out the form and tap the **Create App ID** button.</span></span>

  ![Erstellen Sie ein Formular neue App-ID](index/_static/FBNewAppId.png)

* <span data-ttu-id="efa81-116">Wählen Sie auf der neuen App-Karte **Produkt hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="efa81-116">On the new App card, select **Add a Product**.</span></span>  <span data-ttu-id="efa81-117">Klicken Sie auf der **Facebook-Anmelde** Karte auf **Einrichten** .</span><span class="sxs-lookup"><span data-stu-id="efa81-117">On the **Facebook Login** card, click **Set Up**</span></span> 

  ![Setup-Produktseite](index/_static/FBProductSetup.png)

* <span data-ttu-id="efa81-119">Der **Schnellstart** -Assistent wird gestartet, und **Wählen Sie eine Plattform** als erste Seite aus.</span><span class="sxs-lookup"><span data-stu-id="efa81-119">The **Quickstart** wizard launches with **Choose a Platform** as the first page.</span></span> <span data-ttu-id="efa81-120">Umgehen Sie den Assistenten, indem Sie im Menü unten links auf den Link **Facebook Login** **Settings** (Facebook-Anmelde Einstellungen) klicken:</span><span class="sxs-lookup"><span data-stu-id="efa81-120">Bypass the wizard for now by clicking the **FaceBook Login** **Settings** link in the menu on the lower left:</span></span>

  ![Skip-Schnellstart](index/_static/FBSkipQuickStart.png)

* <span data-ttu-id="efa81-122">Die Seite "Client- **OAuth-Einstellungen** " wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="efa81-122">You are presented with the **Client OAuth Settings** page:</span></span>

  ![Seite "Client-OAuth-Einstellungen"](index/_static/FBOAuthSetup.png)

* <span data-ttu-id="efa81-124">Geben Sie Ihren Entwicklungs-URI mit */SignIn-Facebook* an das **gültige OAuth-Umleitungs-URIs** -Feld an (z. b.: `https://localhost:44320/signin-facebook`).</span><span class="sxs-lookup"><span data-stu-id="efa81-124">Enter your development URI with */signin-facebook* appended into the **Valid OAuth Redirect URIs** field (for example: `https://localhost:44320/signin-facebook`).</span></span> <span data-ttu-id="efa81-125">Bei der Facebook-Authentifizierung, die später in diesem Tutorial konfiguriert wird, werden Anforderungen an */SignIn-Facebook* Route automatisch verarbeitet, um den OAuth-Fluss zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="efa81-125">The Facebook authentication configured later in this tutorial will automatically handle requests at */signin-facebook* route to implement the OAuth flow.</span></span>

> [!NOTE]
> <span data-ttu-id="efa81-126">Der URI */SignIn-Facebook* wird als Standard Rückruf des Facebook-Authentifizierungs Anbieters festgelegt.</span><span class="sxs-lookup"><span data-stu-id="efa81-126">The URI */signin-facebook* is set as the default callback of the Facebook authentication provider.</span></span> <span data-ttu-id="efa81-127">Sie können den Standard-Rückruf-URI beim Konfigurieren der Facebook-Authentifizierungs Middleware über die geerbte [remoteauthenticationoptions. callbackpath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) -Eigenschaft der [facebookoptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) -Klasse ändern.</span><span class="sxs-lookup"><span data-stu-id="efa81-127">You can change the default callback URI while configuring the Facebook authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) class.</span></span>

* <span data-ttu-id="efa81-128">Klicken Sie auf **Save Changes** (Änderungen speichern).</span><span class="sxs-lookup"><span data-stu-id="efa81-128">Click **Save Changes**.</span></span>

* <span data-ttu-id="efa81-129">Klicken Sie im linken Navigationsbereich auf **Einstellungen** > **grundlegenden** Link.</span><span class="sxs-lookup"><span data-stu-id="efa81-129">Click **Settings** > **Basic** link in the left navigation.</span></span>

  <span data-ttu-id="efa81-130">Notieren Sie sich auf dieser Seite Ihren `App ID` und ihren `App Secret`.</span><span class="sxs-lookup"><span data-stu-id="efa81-130">On this page, make a note of your `App ID` and your `App Secret`.</span></span> <span data-ttu-id="efa81-131">Im nächsten Abschnitt fügen Sie beide Zertifikate in Ihrer ASP.NET Core-Anwendung:</span><span class="sxs-lookup"><span data-stu-id="efa81-131">You will add both into your ASP.NET Core application in the next section:</span></span>

* <span data-ttu-id="efa81-132">Wenn Sie den Standort bereitstellen, müssen Sie die Seite für die **Facebook-Anmelde** Einrichtung erneut aufrufen und einen neuen öffentlichen URI registrieren.</span><span class="sxs-lookup"><span data-stu-id="efa81-132">When deploying the site you need to revisit the **Facebook Login** setup page and register a new public URI.</span></span>

## <a name="store-the-facebook-app-id-and-secret"></a><span data-ttu-id="efa81-133">Speichern der Facebook-APP-ID und des geheimen Schlüssels</span><span class="sxs-lookup"><span data-stu-id="efa81-133">Store the Facebook app ID and secret</span></span>

<span data-ttu-id="efa81-134">Speichern Sie sensible Einstellungen wie die Facebook-APP-ID und die geheimen Werte mit dem [Geheimnis-Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="efa81-134">Store sensitive settings such as the Facebook app ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="efa81-135">Führen Sie für dieses Beispiel die folgenden Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="efa81-135">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="efa81-136">Initialisieren Sie das Projekt für die geheime Speicherung gemäß den Anweisungen unter [Aktivieren der geheimen Speicherung](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="efa81-136">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="efa81-137">Speichern Sie die sensiblen Einstellungen im lokalen Geheimnis Speicher mit den geheimen Schlüsseln `Authentication:Facebook:AppId` und `Authentication:Facebook:AppSecret`:</span><span class="sxs-lookup"><span data-stu-id="efa81-137">Store the sensitive settings in the local secret store with the secret keys `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a><span data-ttu-id="efa81-138">Konfigurieren der Facebook-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="efa81-138">Configure Facebook Authentication</span></span>

<span data-ttu-id="efa81-139">Fügen Sie den Facebook-Dienst in der `ConfigureServices`-Methode in der Datei *Startup.cs* hinzu:</span><span class="sxs-lookup"><span data-stu-id="efa81-139">Add the Facebook service in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="efa81-140">Weitere Informationen zu den von der Facebook-Authentifizierung unterstützten Konfigurationsoptionen finden Sie in der [facebookoptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) -API-Referenz.</span><span class="sxs-lookup"><span data-stu-id="efa81-140">See the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API reference for more information on configuration options supported by Facebook authentication.</span></span> <span data-ttu-id="efa81-141">Optionen für die Konfiguration können zu verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="efa81-141">Configuration options can be used to:</span></span>

* <span data-ttu-id="efa81-142">Fordern Sie verschiedene Informationen über den Benutzer.</span><span class="sxs-lookup"><span data-stu-id="efa81-142">Request different information about the user.</span></span>
* <span data-ttu-id="efa81-143">Fügen Sie die Abfragezeichenfolge-Argumente zum Anpassen der Anmeldung hinzu.</span><span class="sxs-lookup"><span data-stu-id="efa81-143">Add query string arguments to customize the login experience.</span></span>

## <a name="sign-in-with-facebook"></a><span data-ttu-id="efa81-144">Melden Sie sich mit Facebook</span><span class="sxs-lookup"><span data-stu-id="efa81-144">Sign in with Facebook</span></span>

<span data-ttu-id="efa81-145">Führen Sie die Anwendung aus, und klicken Sie auf **Anmelden**.</span><span class="sxs-lookup"><span data-stu-id="efa81-145">Run your application and click **Log in**.</span></span> <span data-ttu-id="efa81-146">Daraufhin wird eine Option aus, um sich mit Facebook anmelden.</span><span class="sxs-lookup"><span data-stu-id="efa81-146">You see an option to sign in with Facebook.</span></span>

![Web-Anwendung: Benutzer nicht authentifiziert.](index/_static/DoneFacebook.png)

<span data-ttu-id="efa81-148">Wenn Sie auf **Facebook**klicken, werden Sie zur Authentifizierung an Facebook umgeleitet:</span><span class="sxs-lookup"><span data-stu-id="efa81-148">When you click on **Facebook**, you are redirected to Facebook for authentication:</span></span>

![Seite der Facebook-Authentifizierung](index/_static/FBLogin.png)

<span data-ttu-id="efa81-150">Facebook-Authentifizierung standardmäßige öffentliche Profil und e-Mail-Adresse anfordert:</span><span class="sxs-lookup"><span data-stu-id="efa81-150">Facebook authentication requests public profile and email address by default:</span></span>

![Facebook-Authentifizierung Seite zustimmungsbildschirm](index/_static/FBLoginDone.png)

<span data-ttu-id="efa81-152">Nachdem Sie Ihre Facebook-Anmeldeinformationen eingeben, werden Sie an Ihrem Standort weitergeleitet, in dem Sie Ihre e-Mail-Adresse festlegen können.</span><span class="sxs-lookup"><span data-stu-id="efa81-152">Once you enter your Facebook credentials you are redirected back to your site where you can set your email.</span></span>

<span data-ttu-id="efa81-153">Sie sind jetzt angemeldet, sich mit Ihren Facebook-Anmeldeinformationen:</span><span class="sxs-lookup"><span data-stu-id="efa81-153">You are now logged in using your Facebook credentials:</span></span>

![Web-Anwendung: Benutzerauthentifizierung](index/_static/Done.png)

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="efa81-155">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="efa81-155">Troubleshooting</span></span>

* <span data-ttu-id="efa81-156">**Nur ASP.net Core 2. x:** Wenn die Identität nicht durch Aufrufen von `services.AddIdentity` in `ConfigureServices`konfiguriert wird, führt der Versuch, die Authentifizierung zu authentifizieren, zu *argumumtexception: die Option "signinscheme" muss angegeben werden*.</span><span class="sxs-lookup"><span data-stu-id="efa81-156">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="efa81-157">Die Projektvorlage, die in diesem Tutorial verwendete wird sichergestellt, dass dies geschehen ist.</span><span class="sxs-lookup"><span data-stu-id="efa81-157">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="efa81-158">Wenn die Standortdatenbank nicht erstellt wurde, indem die ursprüngliche Migration anwenden, erhalten Sie *Fehler bei ein Datenbankvorgang beim Verarbeiten der Anforderung* Fehler.</span><span class="sxs-lookup"><span data-stu-id="efa81-158">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="efa81-159">Tippen Sie auf **Migrations anwenden** , um die Datenbank zu erstellen und zu aktualisieren, um den Fehler zu überschreiten.</span><span class="sxs-lookup"><span data-stu-id="efa81-159">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="efa81-160">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="efa81-160">Next steps</span></span>

* <span data-ttu-id="efa81-161">In diesem Artikel wurde gezeigt, wie Sie mit Facebook authentifizieren können.</span><span class="sxs-lookup"><span data-stu-id="efa81-161">This article showed how you can authenticate with Facebook.</span></span> <span data-ttu-id="efa81-162">Führen Sie einen ähnlichen Ansatz für die Authentifizierung mit anderen Anbietern aufgeführt, auf die [Vorgängerseite](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="efa81-162">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="efa81-163">Nachdem Sie die Website in der Azure-Web-App veröffentlicht haben, sollten Sie die `AppSecret` im Facebook-Entwickler Portal zurücksetzen.</span><span class="sxs-lookup"><span data-stu-id="efa81-163">Once you publish your web site to Azure web app, you should reset the `AppSecret` in the Facebook developer portal.</span></span>

* <span data-ttu-id="efa81-164">Legen Sie die `Authentication:Facebook:AppId` und `Authentication:Facebook:AppSecret` Anwendungseinstellungen im Azure-Portal.</span><span class="sxs-lookup"><span data-stu-id="efa81-164">Set the `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="efa81-165">Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="efa81-165">The configuration system is set up to read keys from environment variables.</span></span>

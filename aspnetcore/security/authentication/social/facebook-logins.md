---
title: Einrichtung externer Facebook-Anmelde Informationen in ASP.net Core
author: rick-anderson
description: Tutorial mit Codebeispielen, die die Integration der Authentifizierung von Facebook-Konto Benutzern in eine vorhandene ASP.net Core-App veranschaulichen.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 12/02/2019
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/facebook-logins
ms.openlocfilehash: 2e4cc04c6e7ff8e5f5701cc7f9ede73dbc1b4685
ms.sourcegitcommit: 3b6b0a54b20dc99b0c8c5978400c60adf431072f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74717105"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a><span data-ttu-id="15390-103">Einrichtung externer Facebook-Anmelde Informationen in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="15390-103">Facebook external login setup in ASP.NET Core</span></span>

<span data-ttu-id="15390-104">Von [Valeriy Novytskyy](https://github.com/01binary) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="15390-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="15390-105">In diesem Tutorial mit Codebeispielen wird veranschaulicht, wie Sie es Ihren Benutzern ermöglichen können, sich mit Ihrem Facebook-Konto zu anmelden, indem Sie ein Beispiel ASP.net Core auf der [vorherigen Seite](xref:security/authentication/social/index)erstellten Beispiel Projekt 3,0.</span><span class="sxs-lookup"><span data-stu-id="15390-105">This tutorial with code examples shows how to enable your users to sign in with their Facebook account using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span> <span data-ttu-id="15390-106">Wir beginnen damit, eine Facebook-APP-ID zu erstellen, indem wir die [offiziellen Schritte](https://developers.facebook.com)befolgen.</span><span class="sxs-lookup"><span data-stu-id="15390-106">We start by creating a Facebook App ID by following the [official steps](https://developers.facebook.com).</span></span>

## <a name="create-the-app-in-facebook"></a><span data-ttu-id="15390-107">Erstellen der app in Facebook</span><span class="sxs-lookup"><span data-stu-id="15390-107">Create the app in Facebook</span></span>

* <span data-ttu-id="15390-108">Fügen Sie das nuget-Paket [Microsoft. aspnetcore. Authentication. Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) zum Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="15390-108">Add the [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet package to the project.</span></span>

* <span data-ttu-id="15390-109">Navigieren Sie zur Seite [Facebook-Entwickler-App](https://developers.facebook.com/apps/) , und melden Sie sich an.</span><span class="sxs-lookup"><span data-stu-id="15390-109">Navigate to the [Facebook Developers app](https://developers.facebook.com/apps/) page and sign in.</span></span> <span data-ttu-id="15390-110">Wenn Sie noch nicht über ein Facebook-Konto verfügen, verwenden Sie den Link **für Facebook registrieren** auf der Anmeldeseite, um einen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="15390-110">If you don't already have a Facebook account, use the **Sign up for Facebook** link on the login page to create one.</span></span>  <span data-ttu-id="15390-111">Wenn Sie über ein Facebook-Konto verfügen, befolgen Sie die Anweisungen, um sich als Facebook-Entwickler zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="15390-111">Once you have a Facebook account, follow the instructions to register as a Facebook Developer.</span></span>

* <span data-ttu-id="15390-112">Wählen Sie im Menü **meine apps** die Option **app erstellen** aus, um eine neue APP-ID zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="15390-112">From the **My Apps** menu select **Create App** to create a new App ID.</span></span>

   ![Facebook für Entwickler Portal in Microsoft Edge geöffnet](index/_static/FBMyApps.png)

* <span data-ttu-id="15390-114">Füllen Sie das Formular aus, und tippen Sie auf die Schaltfläche **App-ID erstellen** .</span><span class="sxs-lookup"><span data-stu-id="15390-114">Fill out the form and tap the **Create App ID** button.</span></span>

  ![Erstellen Sie ein neues APP-ID-Formular.](index/_static/FBNewAppId.png)

* <span data-ttu-id="15390-116">Wählen Sie auf der neuen App-Karte **Produkt hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="15390-116">On the new App card, select **Add a Product**.</span></span>  <span data-ttu-id="15390-117">Klicken Sie auf der **Facebook-Anmelde** Karte auf **Einrichten** .</span><span class="sxs-lookup"><span data-stu-id="15390-117">On the **Facebook Login** card, click **Set Up**</span></span> 

  ![Seite "Produkt Setup"](index/_static/FBProductSetup.png)

* <span data-ttu-id="15390-119">Der **Schnellstart** -Assistent wird gestartet, und **Wählen Sie eine Plattform** als erste Seite aus.</span><span class="sxs-lookup"><span data-stu-id="15390-119">The **Quickstart** wizard launches with **Choose a Platform** as the first page.</span></span> <span data-ttu-id="15390-120">Umgehen Sie den Assistenten, indem Sie im Menü unten links auf den Link **Facebook Login** **Settings** (Facebook-Anmelde Einstellungen) klicken:</span><span class="sxs-lookup"><span data-stu-id="15390-120">Bypass the wizard for now by clicking the **FaceBook Login** **Settings** link in the menu on the lower left:</span></span>

  ![Schnellstart überspringen](index/_static/FBSkipQuickStart.png)

* <span data-ttu-id="15390-122">Die Seite "Client- **OAuth-Einstellungen** " wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="15390-122">You are presented with the **Client OAuth Settings** page:</span></span>

  ![Client-OAuth-Einstellungsseite](index/_static/FBOAuthSetup.png)

* <span data-ttu-id="15390-124">Geben Sie Ihren Entwicklungs-URI mit */SignIn-Facebook* an das **gültige OAuth-Umleitungs-URIs** -Feld an (z. b.: `https://localhost:44320/signin-facebook`).</span><span class="sxs-lookup"><span data-stu-id="15390-124">Enter your development URI with */signin-facebook* appended into the **Valid OAuth Redirect URIs** field (for example: `https://localhost:44320/signin-facebook`).</span></span> <span data-ttu-id="15390-125">Bei der Facebook-Authentifizierung, die später in diesem Tutorial konfiguriert wird, werden Anforderungen an */SignIn-Facebook* Route automatisch verarbeitet, um den OAuth-Fluss zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="15390-125">The Facebook authentication configured later in this tutorial will automatically handle requests at */signin-facebook* route to implement the OAuth flow.</span></span>

> [!NOTE]
> <span data-ttu-id="15390-126">Der URI */SignIn-Facebook* wird als Standard Rückruf des Facebook-Authentifizierungs Anbieters festgelegt.</span><span class="sxs-lookup"><span data-stu-id="15390-126">The URI */signin-facebook* is set as the default callback of the Facebook authentication provider.</span></span> <span data-ttu-id="15390-127">Sie können den Standard-Rückruf-URI beim Konfigurieren der Facebook-Authentifizierungs Middleware über die geerbte [remoteauthenticationoptions. callbackpath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) -Eigenschaft der [facebookoptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) -Klasse ändern.</span><span class="sxs-lookup"><span data-stu-id="15390-127">You can change the default callback URI while configuring the Facebook authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) class.</span></span>

* <span data-ttu-id="15390-128">Klicken Sie auf **Änderungen speichern**.</span><span class="sxs-lookup"><span data-stu-id="15390-128">Click **Save Changes**.</span></span>

* <span data-ttu-id="15390-129">Klicken Sie im linken Navigationsbereich auf **Einstellungen** > **grundlegenden** Link.</span><span class="sxs-lookup"><span data-stu-id="15390-129">Click **Settings** > **Basic** link in the left navigation.</span></span>

  <span data-ttu-id="15390-130">Notieren Sie sich auf dieser Seite Ihren `App ID` und ihren `App Secret`.</span><span class="sxs-lookup"><span data-stu-id="15390-130">On this page, make a note of your `App ID` and your `App Secret`.</span></span> <span data-ttu-id="15390-131">Sie fügen beide im nächsten Abschnitt Ihrer ASP.net Core Anwendung hinzu:</span><span class="sxs-lookup"><span data-stu-id="15390-131">You will add both into your ASP.NET Core application in the next section:</span></span>

* <span data-ttu-id="15390-132">Wenn Sie den Standort bereitstellen, müssen Sie die Seite für die **Facebook-Anmelde** Einrichtung erneut aufrufen und einen neuen öffentlichen URI registrieren.</span><span class="sxs-lookup"><span data-stu-id="15390-132">When deploying the site you need to revisit the **Facebook Login** setup page and register a new public URI.</span></span>

## <a name="store-facebook-app-id-and-app-secret"></a><span data-ttu-id="15390-133">Speichern der Facebook-APP-ID und des App-Geheimnisses</span><span class="sxs-lookup"><span data-stu-id="15390-133">Store Facebook App ID and App Secret</span></span>

<span data-ttu-id="15390-134">Verknüpfen Sie sensible Einstellungen wie Facebook `App ID` und `App Secret` mit ihrer Anwendungskonfiguration mithilfe des [geheimen Managers](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="15390-134">Link sensitive settings like Facebook `App ID` and `App Secret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="15390-135">Benennen Sie die Token für die Zwecke dieses Tutorials `Authentication:Facebook:AppId` und `Authentication:Facebook:AppSecret`.</span><span class="sxs-lookup"><span data-stu-id="15390-135">For the purposes of this tutorial, name the tokens `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret`.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="15390-136">Führen Sie die folgenden Befehle aus, um `App ID` und `App Secret` mithilfe von Secret Manager sicher zu speichern:</span><span class="sxs-lookup"><span data-stu-id="15390-136">Execute the following commands to securely store `App ID` and `App Secret` using Secret Manager:</span></span>

```dotnetcli
dotnet user-secrets set Authentication:Facebook:AppId <app-id>
dotnet user-secrets set Authentication:Facebook:AppSecret <app-secret>
```

## <a name="configure-facebook-authentication"></a><span data-ttu-id="15390-137">Konfigurieren der Facebook-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="15390-137">Configure Facebook Authentication</span></span>

<span data-ttu-id="15390-138">Fügen Sie den Facebook-Dienst in der `ConfigureServices`-Methode in der Datei *Startup.cs* hinzu:</span><span class="sxs-lookup"><span data-stu-id="15390-138">Add the Facebook service in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="15390-139">Weitere Informationen zu den von der Facebook-Authentifizierung unterstützten Konfigurationsoptionen finden Sie in der [facebookoptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) -API-Referenz.</span><span class="sxs-lookup"><span data-stu-id="15390-139">See the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API reference for more information on configuration options supported by Facebook authentication.</span></span> <span data-ttu-id="15390-140">Konfigurationsoptionen können für Folgendes verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="15390-140">Configuration options can be used to:</span></span>

* <span data-ttu-id="15390-141">Anfordern von anderen Informationen über den Benutzer.</span><span class="sxs-lookup"><span data-stu-id="15390-141">Request different information about the user.</span></span>
* <span data-ttu-id="15390-142">Fügen Sie Abfrage Zeichen folgen Argumente hinzu, um die Anmeldung anzupassen.</span><span class="sxs-lookup"><span data-stu-id="15390-142">Add query string arguments to customize the login experience.</span></span>

## <a name="sign-in-with-facebook"></a><span data-ttu-id="15390-143">Mit Facebook anmelden</span><span class="sxs-lookup"><span data-stu-id="15390-143">Sign in with Facebook</span></span>

<span data-ttu-id="15390-144">Führen Sie die Anwendung aus, und klicken Sie auf **Anmelden**.</span><span class="sxs-lookup"><span data-stu-id="15390-144">Run your application and click **Log in**.</span></span> <span data-ttu-id="15390-145">Sie sehen eine Option zum Anmelden mit Facebook.</span><span class="sxs-lookup"><span data-stu-id="15390-145">You see an option to sign in with Facebook.</span></span>

![Webanwendung: Benutzer ist nicht authentifiziert.](index/_static/DoneFacebook.png)

<span data-ttu-id="15390-147">Wenn Sie auf **Facebook**klicken, werden Sie zur Authentifizierung an Facebook umgeleitet:</span><span class="sxs-lookup"><span data-stu-id="15390-147">When you click on **Facebook**, you are redirected to Facebook for authentication:</span></span>

![Facebook-Authentifizierungsseite](index/_static/FBLogin.png)

<span data-ttu-id="15390-149">Facebook-Authentifizierung fordert standardmäßig Öffentliches Profil und e-Mail-Adresse an:</span><span class="sxs-lookup"><span data-stu-id="15390-149">Facebook authentication requests public profile and email address by default:</span></span>

![Zustimmungs Bildschirm der Facebook-Authentifizierungsseite](index/_static/FBLoginDone.png)

<span data-ttu-id="15390-151">Nachdem Sie Ihre Facebook-Anmelde Informationen eingegeben haben, werden Sie zurück zu Ihrer Website umgeleitet, auf der Sie Ihre e-Mail festlegen können.</span><span class="sxs-lookup"><span data-stu-id="15390-151">Once you enter your Facebook credentials you are redirected back to your site where you can set your email.</span></span>

<span data-ttu-id="15390-152">Sie sind jetzt mit ihren Facebook-Anmelde Informationen angemeldet:</span><span class="sxs-lookup"><span data-stu-id="15390-152">You are now logged in using your Facebook credentials:</span></span>

![Webanwendung: Benutzer authentifiziert](index/_static/Done.png)

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="15390-154">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="15390-154">Troubleshooting</span></span>

* <span data-ttu-id="15390-155">**Nur ASP.net Core 2. x:** Wenn die Identität nicht durch Aufrufen von `services.AddIdentity` in `ConfigureServices`konfiguriert wird, führt der Versuch, die Authentifizierung zu authentifizieren, zu *argumumtexception: die Option "signinscheme" muss angegeben werden*.</span><span class="sxs-lookup"><span data-stu-id="15390-155">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="15390-156">Die Projektvorlage, die in diesem Tutorial verwendete wird sichergestellt, dass dies geschehen ist.</span><span class="sxs-lookup"><span data-stu-id="15390-156">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="15390-157">Wenn die Standortdatenbank nicht erstellt wurde, indem die ursprüngliche Migration anwenden, erhalten Sie *Fehler bei ein Datenbankvorgang beim Verarbeiten der Anforderung* Fehler.</span><span class="sxs-lookup"><span data-stu-id="15390-157">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="15390-158">Tippen Sie auf **Migrations anwenden** , um die Datenbank zu erstellen und zu aktualisieren, um den Fehler zu überschreiten.</span><span class="sxs-lookup"><span data-stu-id="15390-158">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="15390-159">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="15390-159">Next steps</span></span>

* <span data-ttu-id="15390-160">In diesem Artikel wurde gezeigt, wie Sie sich mit Facebook authentifizieren können.</span><span class="sxs-lookup"><span data-stu-id="15390-160">This article showed how you can authenticate with Facebook.</span></span> <span data-ttu-id="15390-161">Führen Sie einen ähnlichen Ansatz für die Authentifizierung mit anderen Anbietern aufgeführt, auf die [Vorgängerseite](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="15390-161">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="15390-162">Nachdem Sie die Website in der Azure-Web-App veröffentlicht haben, sollten Sie die `AppSecret` im Facebook-Entwickler Portal zurücksetzen.</span><span class="sxs-lookup"><span data-stu-id="15390-162">Once you publish your web site to Azure web app, you should reset the `AppSecret` in the Facebook developer portal.</span></span>

* <span data-ttu-id="15390-163">Legen Sie die `Authentication:Facebook:AppId` und `Authentication:Facebook:AppSecret` Anwendungseinstellungen im Azure-Portal.</span><span class="sxs-lookup"><span data-stu-id="15390-163">Set the `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="15390-164">Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="15390-164">The configuration system is set up to read keys from environment variables.</span></span>

---
title: Facebook externe Login-Setup in ASP.NET Core
author: rick-anderson
description: Tutorial mit Codebeispielen, die die Integration der Facebook-Kontobenutzerauthentifizierung in eine vorhandene ASP.NET Core-App demonstrieren.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/facebook-logins
ms.openlocfilehash: 9b3128addafb41ad6ec44af5cb12e89607e1ae59
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277300"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a><span data-ttu-id="5477f-103">Facebook externe Login-Setup in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5477f-103">Facebook external login setup in ASP.NET Core</span></span>

<span data-ttu-id="5477f-104">Von [Valeriy Novytskyy](https://github.com/01binary) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5477f-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

<span data-ttu-id="5477f-105">In diesem Tutorial mit Codebeispielen wird gezeigt, wie Sie Ihren Benutzern die Anmeldung mit ihrem Facebook-Konto mithilfe eines Beispiels ASP.NET Core 3.0-Projekts ermöglichen, das auf der [vorherigen Seite](xref:security/authentication/social/index)erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="5477f-105">This tutorial with code examples shows how to enable your users to sign in with their Facebook account using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span> <span data-ttu-id="5477f-106">Wir beginnen mit der Erstellung einer Facebook App ID, indem wir die [offiziellen Schritte](https://developers.facebook.com)befolgen.</span><span class="sxs-lookup"><span data-stu-id="5477f-106">We start by creating a Facebook App ID by following the [official steps](https://developers.facebook.com).</span></span>

## <a name="create-the-app-in-facebook"></a><span data-ttu-id="5477f-107">Erstellen der App in Facebook</span><span class="sxs-lookup"><span data-stu-id="5477f-107">Create the app in Facebook</span></span>

* <span data-ttu-id="5477f-108">Fügen Sie dem Projekt das [Paket Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet hinzu.</span><span class="sxs-lookup"><span data-stu-id="5477f-108">Add the [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet package to the project.</span></span>

* <span data-ttu-id="5477f-109">Navigieren Sie zur [Facebook Developers App-Seite,](https://developers.facebook.com/apps/) und melden Sie sich an.</span><span class="sxs-lookup"><span data-stu-id="5477f-109">Navigate to the [Facebook Developers app](https://developers.facebook.com/apps/) page and sign in.</span></span> <span data-ttu-id="5477f-110">Wenn du noch kein Facebook-Konto hast, nutze den Link **Registrieren für Facebook** auf der Anmeldeseite, um eines zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="5477f-110">If you don't already have a Facebook account, use the **Sign up for Facebook** link on the login page to create one.</span></span>  <span data-ttu-id="5477f-111">Sobald sie ein Facebook-Konto haben, folgen Sie den Anweisungen, um sich als Facebook-Entwickler zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="5477f-111">Once you have a Facebook account, follow the instructions to register as a Facebook Developer.</span></span>

* <span data-ttu-id="5477f-112">Wählen Sie im Menü **Meine Apps** **App erstellen** aus, um eine neue App-ID zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="5477f-112">From the **My Apps** menu select **Create App** to create a new App ID.</span></span>

   ![Facebook für Entwicklerportal in Microsoft Edge geöffnet](index/_static/FBMyApps.png)

* <span data-ttu-id="5477f-114">Füllen Sie das Formular aus, und tippen Sie auf die Schaltfläche **App-ID erstellen.**</span><span class="sxs-lookup"><span data-stu-id="5477f-114">Fill out the form and tap the **Create App ID** button.</span></span>

  ![Erstellen eines neuen App-ID-Formulars](index/_static/FBNewAppId.png)

* <span data-ttu-id="5477f-116">Wählen Sie auf der neuen App-Karte **Ein Produkt**hinzufügen aus.</span><span class="sxs-lookup"><span data-stu-id="5477f-116">On the new App card, select **Add a Product**.</span></span>  <span data-ttu-id="5477f-117">Klicken Sie auf der **Facebook-Login-Karte** auf **Einrichten**</span><span class="sxs-lookup"><span data-stu-id="5477f-117">On the **Facebook Login** card, click **Set Up**</span></span> 

  ![Produkt-Setup-Seite](index/_static/FBProductSetup.png)

* <span data-ttu-id="5477f-119">Der **Schnellstart-Assistent** wird mit "Plattform als erste Seite **auswählen"** gestartet.</span><span class="sxs-lookup"><span data-stu-id="5477f-119">The **Quickstart** wizard launches with **Choose a Platform** as the first page.</span></span> <span data-ttu-id="5477f-120">Umgehen Sie den Assistenten vorerst, indem Sie im Menü unten links auf den Link **FaceBook-Anmeldeeinstellungen** **Settings** klicken:</span><span class="sxs-lookup"><span data-stu-id="5477f-120">Bypass the wizard for now by clicking the **FaceBook Login** **Settings** link in the menu on the lower left:</span></span>

  ![Schnellstart überspringen](index/_static/FBSkipQuickStart.png)

* <span data-ttu-id="5477f-122">Sie werden mit der **Seite Client OAuth-Einstellungen** angezeigt:</span><span class="sxs-lookup"><span data-stu-id="5477f-122">You are presented with the **Client OAuth Settings** page:</span></span>

  ![Seite Client-OAuth-Einstellungen](index/_static/FBOAuthSetup.png)

* <span data-ttu-id="5477f-124">Geben Sie Ihren Entwicklungs-URI mit */signin-facebook* in das Feld **Gültige OAuth-Umleitungs-URIs** (z. B.: `https://localhost:44320/signin-facebook`) ein.</span><span class="sxs-lookup"><span data-stu-id="5477f-124">Enter your development URI with */signin-facebook* appended into the **Valid OAuth Redirect URIs** field (for example: `https://localhost:44320/signin-facebook`).</span></span> <span data-ttu-id="5477f-125">Die später in diesem Tutorial konfigurierte Facebook-Authentifizierung verarbeitet automatisch Anforderungen auf */signin-facebook-Route,* um den OAuth-Fluss zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="5477f-125">The Facebook authentication configured later in this tutorial will automatically handle requests at */signin-facebook* route to implement the OAuth flow.</span></span>

> [!NOTE]
> <span data-ttu-id="5477f-126">Der URI */signin-facebook* wird als Standardrückruf des Facebook-Authentifizierungsanbieters festgelegt.</span><span class="sxs-lookup"><span data-stu-id="5477f-126">The URI */signin-facebook* is set as the default callback of the Facebook authentication provider.</span></span> <span data-ttu-id="5477f-127">Sie können den Standardrückruf-URI ändern, während Sie die Facebook-Authentifizierungs-Middleware über die geerbte [RemoteAuthenticationOptions.CallbackPath-Eigenschaft](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) der [FacebookOptions-Klasse](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="5477f-127">You can change the default callback URI while configuring the Facebook authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) class.</span></span>

* <span data-ttu-id="5477f-128">Klicken Sie auf **Save Changes** (Änderungen speichern).</span><span class="sxs-lookup"><span data-stu-id="5477f-128">Click **Save Changes**.</span></span>

* <span data-ttu-id="5477f-129">Klicken Sie in der linken Navigation auf den Link **Einstellungen** > **Basic.**</span><span class="sxs-lookup"><span data-stu-id="5477f-129">Click **Settings** > **Basic** link in the left navigation.</span></span>

  <span data-ttu-id="5477f-130">Notieren Sie sich auf `App ID` dieser `App Secret`Seite Ihre und Ihre .</span><span class="sxs-lookup"><span data-stu-id="5477f-130">On this page, make a note of your `App ID` and your `App Secret`.</span></span> <span data-ttu-id="5477f-131">Sie fügen beide zu Ihrer ASP.NET Core-Anwendung im nächsten Abschnitt hinzu:</span><span class="sxs-lookup"><span data-stu-id="5477f-131">You will add both into your ASP.NET Core application in the next section:</span></span>

* <span data-ttu-id="5477f-132">Wenn Sie die Website bereitstellen, müssen Sie die **Facebook-Anmeldeeinrichtungsseite** erneut aufrufen und einen neuen öffentlichen URI registrieren.</span><span class="sxs-lookup"><span data-stu-id="5477f-132">When deploying the site you need to revisit the **Facebook Login** setup page and register a new public URI.</span></span>

## <a name="store-the-facebook-app-id-and-secret"></a><span data-ttu-id="5477f-133">Speichern der Facebook-App-ID und des geheimen</span><span class="sxs-lookup"><span data-stu-id="5477f-133">Store the Facebook app ID and secret</span></span>

<span data-ttu-id="5477f-134">Speichern Sie vertrauliche Einstellungen wie die Facebook-App-ID und geheime Werte mit [Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="5477f-134">Store sensitive settings such as the Facebook app ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="5477f-135">Gehen Sie für dieses Beispiel wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="5477f-135">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="5477f-136">Initialisieren Sie das Projekt für die geheime Speicherung gemäß den Anweisungen unter [Geheime Speicherung aktivieren](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="5477f-136">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="5477f-137">Speichern Sie die vertraulichen Einstellungen im `Authentication:Facebook:AppId` lokalen `Authentication:Facebook:AppSecret`geheimen Speicher mit den geheimen Schlüsseln und:</span><span class="sxs-lookup"><span data-stu-id="5477f-137">Store the sensitive settings in the local secret store with the secret keys `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a><span data-ttu-id="5477f-138">Konfigurieren der Facebook-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="5477f-138">Configure Facebook Authentication</span></span>

<span data-ttu-id="5477f-139">Fügen Sie den `ConfigureServices` Facebook-Dienst in der Methode in der *Startup.cs-Datei* hinzu:</span><span class="sxs-lookup"><span data-stu-id="5477f-139">Add the Facebook service in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a><span data-ttu-id="5477f-140">Melden Sie sich mit Facebook an</span><span class="sxs-lookup"><span data-stu-id="5477f-140">Sign in with Facebook</span></span>

* <span data-ttu-id="5477f-141">Führen Sie die App aus, und wählen Sie **Anmelden**aus.</span><span class="sxs-lookup"><span data-stu-id="5477f-141">Run the app and select **Log in**.</span></span> 
* <span data-ttu-id="5477f-142">Wählen Sie unter **Verwenden eines anderen Dienstes zum Anmelden aus.**, wählen Sie Facebook aus.</span><span class="sxs-lookup"><span data-stu-id="5477f-142">Under **Use another service to log in.**, select Facebook.</span></span>
* <span data-ttu-id="5477f-143">Sie werden zur Authentifizierung zu **Facebook** weitergeleitet.</span><span class="sxs-lookup"><span data-stu-id="5477f-143">You are redirected to **Facebook** for authentication.</span></span>
* <span data-ttu-id="5477f-144">Geben Sie Ihre Facebook-Anmeldeinformationen ein.</span><span class="sxs-lookup"><span data-stu-id="5477f-144">Enter your Facebook credentials.</span></span>
* <span data-ttu-id="5477f-145">Sie werden zurück zu Ihrer Website weitergeleitet, wo Sie Ihre E-Mail-Adresse festlegen können.</span><span class="sxs-lookup"><span data-stu-id="5477f-145">You are redirected back to your site where you can set your email.</span></span>

<span data-ttu-id="5477f-146">Sie sind jetzt mit Ihren Facebook-Anmeldeinformationen angemeldet:</span><span class="sxs-lookup"><span data-stu-id="5477f-146">You are now logged in using your Facebook credentials:</span></span>

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a><span data-ttu-id="5477f-147">Reagieren, um die Autorisierung externer Anmeldungen aufzuheben</span><span class="sxs-lookup"><span data-stu-id="5477f-147">React to cancel authorize external sign-in</span></span>

<span data-ttu-id="5477f-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath>kann einen Umleitungspfad für den Benutzer-Agent bereitstellen, wenn der Benutzer die angeforderte Autorisierungsanforderung nicht genehmigt.</span><span class="sxs-lookup"><span data-stu-id="5477f-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath> can provide a redirect path to the user agent when the user doesn't approve the requested authorization demand.</span></span>

<span data-ttu-id="5477f-149">Der folgende Code `AccessDeniedPath` `"/AccessDeniedPathInfo"`legt fest:</span><span class="sxs-lookup"><span data-stu-id="5477f-149">The following code sets the `AccessDeniedPath` to `"/AccessDeniedPathInfo"`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

<span data-ttu-id="5477f-150">Wir empfehlen, dass die `AccessDeniedPath` Seite die folgenden Informationen enthält:</span><span class="sxs-lookup"><span data-stu-id="5477f-150">We recommend the `AccessDeniedPath` page contain the following information:</span></span>

*  <span data-ttu-id="5477f-151">Die Remoteauthentifizierung wurde abgebrochen.</span><span class="sxs-lookup"><span data-stu-id="5477f-151">Remote authentication was canceled.</span></span>
* <span data-ttu-id="5477f-152">Diese App erfordert eine Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="5477f-152">This app requires authentication.</span></span>
* <span data-ttu-id="5477f-153">Um sich erneut anzumelden, wählen Sie den Anmeldelink aus.</span><span class="sxs-lookup"><span data-stu-id="5477f-153">To try sign-in again, select the Login link.</span></span>

### <a name="test-accessdeniedpath"></a><span data-ttu-id="5477f-154">Testen von AccessDeniedPath</span><span class="sxs-lookup"><span data-stu-id="5477f-154">Test AccessDeniedPath</span></span>

* <span data-ttu-id="5477f-155">Navigieren zu [facebook.com](https://www.facebook.com/)</span><span class="sxs-lookup"><span data-stu-id="5477f-155">Navigate to [facebook.com](https://www.facebook.com/)</span></span>
* <span data-ttu-id="5477f-156">Wenn Sie angemeldet sind, müssen Sie sich abmelden.</span><span class="sxs-lookup"><span data-stu-id="5477f-156">If you are signed in, you must sign out.</span></span>
* <span data-ttu-id="5477f-157">Führen Sie die App aus, und wählen Sie die Facebook-Anmeldung aus.</span><span class="sxs-lookup"><span data-stu-id="5477f-157">Run the app and select Facebook sign-in.</span></span>
* <span data-ttu-id="5477f-158">Wählen Sie **Jetzt nicht**aus .</span><span class="sxs-lookup"><span data-stu-id="5477f-158">Select **Not now**.</span></span> <span data-ttu-id="5477f-159">Sie werden zur angegebenen `AccessDeniedPath` Seite umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="5477f-159">You are redirected to the specified `AccessDeniedPath` page.</span></span>

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="5477f-160">Weitere [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) Informationen zu Konfigurationsoptionen, die von der Facebook-Authentifizierung unterstützt werden, finden Sie in der FacebookOptions-API-Referenz.</span><span class="sxs-lookup"><span data-stu-id="5477f-160">See the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API reference for more information on configuration options supported by Facebook authentication.</span></span> <span data-ttu-id="5477f-161">Konfigurationsoptionen können verwendet werden, um:</span><span class="sxs-lookup"><span data-stu-id="5477f-161">Configuration options can be used to:</span></span>

* <span data-ttu-id="5477f-162">Fordern Sie andere Informationen über den Benutzer an.</span><span class="sxs-lookup"><span data-stu-id="5477f-162">Request different information about the user.</span></span>
* <span data-ttu-id="5477f-163">Fügen Sie Abfragezeichenfolgenargumente hinzu, um die Anmeldeerfahrung anzupassen.</span><span class="sxs-lookup"><span data-stu-id="5477f-163">Add query string arguments to customize the login experience.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="5477f-164">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="5477f-164">Troubleshooting</span></span>

* <span data-ttu-id="5477f-165">**ASP.NET Core 2.x nur:** Wenn Identity nicht durch Aufrufen `services.AddIdentity` konfiguriert `ConfigureServices`ist, führt der Versuch, sich zu authentifizieren, zu *ArgumentException: Die Option 'SignInScheme' muss bereitgestellt werden.*</span><span class="sxs-lookup"><span data-stu-id="5477f-165">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="5477f-166">Die Projektvorlage, die in diesem Tutorial verwendete wird sichergestellt, dass dies geschehen ist.</span><span class="sxs-lookup"><span data-stu-id="5477f-166">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="5477f-167">Wenn die Standortdatenbank nicht erstellt wurde, indem die ursprüngliche Migration anwenden, erhalten Sie *Fehler bei ein Datenbankvorgang beim Verarbeiten der Anforderung* Fehler.</span><span class="sxs-lookup"><span data-stu-id="5477f-167">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="5477f-168">Tippen Sie auf **Migrations anwenden,** um die Datenbank zu erstellen und zu aktualisieren, um nach dem Fehler fortzufahren.</span><span class="sxs-lookup"><span data-stu-id="5477f-168">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="5477f-169">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="5477f-169">Next steps</span></span>

* <span data-ttu-id="5477f-170">Dieser Artikel zeigte, wie Sie sich bei Facebook authentifizieren können.</span><span class="sxs-lookup"><span data-stu-id="5477f-170">This article showed how you can authenticate with Facebook.</span></span> <span data-ttu-id="5477f-171">Führen Sie einen ähnlichen Ansatz für die Authentifizierung mit anderen Anbietern aufgeführt, auf die [Vorgängerseite](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="5477f-171">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="5477f-172">Nachdem Sie Ihre Website in der Azure-Web-App veröffentlicht haben, sollten Sie die `AppSecret` im Facebook-Entwicklerportal zurücksetzen.</span><span class="sxs-lookup"><span data-stu-id="5477f-172">Once you publish your web site to Azure web app, you should reset the `AppSecret` in the Facebook developer portal.</span></span>

* <span data-ttu-id="5477f-173">Legen Sie die `Authentication:Facebook:AppId` und `Authentication:Facebook:AppSecret` Anwendungseinstellungen im Azure-Portal.</span><span class="sxs-lookup"><span data-stu-id="5477f-173">Set the `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="5477f-174">Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="5477f-174">The configuration system is set up to read keys from environment variables.</span></span>
